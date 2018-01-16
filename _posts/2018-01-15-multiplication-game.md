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
    for(prime: primes) {
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
the game. However, the bounds for *N* are too large for a
brute force simulation of the game to be effective.


