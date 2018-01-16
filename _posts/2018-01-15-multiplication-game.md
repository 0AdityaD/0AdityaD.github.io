Multiplication Game is a number theory / minimax dp problem
from the ICPC Rocky Mountain Regional Contest (RMRC) 2017

# Problem

Here's the link:
<https://open.kattis.com/problems/multiplicationgame>

In this problem, we are asked to compute the winner of an
interesting two-player game assuming it is played optimally. 

The game described in this problem starts with a random target
integer *2 <= N <= 2^31 - 1* and an integer *M*. The two players,
Alice and Bob take turns multiplying *M* by any prime divisors
of *N* until *M >= N*. If *M > N*, then the game results in a tie.
Otherwise if *M = N*, then the last player to play wins the game.

We are given *N* and the name of the starting player. The
objective is to determine the winner of the game.

# Approach and Code

It's immediately clear that we can split this problem into two
parts:

1. Determine all of the prime divisors of *N*.
2. Determine the result of the game.

We can start by solving Part 1.

## Finding the prime divisors of *N*

The easiest efficient way to do this is to generate a large amount
of primes and then determine which ones divide *N*.

We can generate primes efficiently with the sieve Erastothenes as
shown below. The sieve complexity is *O(n log log n)*

```cpp
const int MAXN = 1 << 16;

bool sieve[MAXN];
vector<llint> primes;

void doSieve(int end) {
    memset(sieve, -1, sizeof sieve);
    sieve[0] = false;
    sieve[1] = false;
    for(int i = 2; i < MAXN; i++) {
        if(!sieve[i]) {
            for(int j = i + i; j < MAXN; j += i) {
                sieve[j] = false;
            }
        }
    }
    for(int i = 0; i < end; i++) {
        if(sieve[i]) {
            primes.push_back(i);
        }
    }
}
```

Determining which of the primes divide *N* is a simple linear
scan through the list of primes.

```cpp
vector<llint> divisors(llint n) {
    vector<llint> res;
    for(llint prime: primes) {
        if(prime * prime > n) {
            break;
        }
        if(n % prime == 0) {
            res.push_back(prime);
        }
        while(n % prime == 0) {
            n /= prime;
        }
    }
    if(n > 1) {
        res.push_back(n);
    }
    return res;
}
```

With these pieces we can play the game.

## Playing the game

Immediately what comes to mind is brute force simulation of
the game. Due to the optimality of both players, one possible
way to simulate a game is through minimax. Minimax means that
a player plays in order to minimize the maximum value of the
next player's turn.

In this game it means that each turn, the current player can
simulate the game for every single possible move and determine
which one yields the worst outcome for the opponent in order
to make his move.

Coding this up is not difficult. We define a few states to
represent a win, loss, or tie. We can define *-1* to represent
a loss, *0* to represent a tie and *1* to represent a win.

```cpp
int minimax(vector<llint>& divs, llint n, llint m=1) {
    if(m > n) {
        // tie
        return 0;
    }
    if(m == n) {
        // the previous player just won so it is a
        // loss for the current player
        return -1;
    }
    // we take the minimum of the maximum of all the possible
    // moves by the next player
    int best = 1;
    for(llint prime: divs) {
        best = min(best, minimax(divs, n, m * prime));
    }
    return -best;
}
```

This code is very inefficient, it is exponential in runtime. 
Notice that there many overlapping subproblems that will be 
calculated when running this code. For example, we can consider
the case of *N=12*. The prime divisors of *12* are *2* and *3*.
The code above will run the function *minimax([2, 3], 12, 6)*
twice. We can eliminate these overlapping subproblems to make
the runtime *O(sqrt n)* with a very simple memoization.

```cpp
unordered_map<llint, int> dp; 

int minimax(vector<llint>& divs, llint n, llint m) {
    if(m > n) {
        return 0;
    }
    if(m == n) {
        return -1;
    }
    if(dp.find(m) != dp.end()) {
        return dp[m];
    }
    int best = 1;
    for(llint prime: divs) {
        best = min(best, minimax(divs, n, m * prime));
    }
    dp[m] = -best;
    return -best;
}
```

From here, we can complete the problem easily.
