Keeping On Track is a Tree Problem from the 2017 ICPC East
Central North America Regional

# Problem

Here's the link:
<https://open.kattis.com/problems/ontrack>

In this problem, we are given a tree *T*. The main objective of
the problem is to find a vertex *v* such that removing *v*
maximizes the number of pairs of disconnected vertices in the tree.
We are then allowed to add exactly one edge to the graph
in order to reconnect vertices that have been disconnected when
we removed vertex *v*. The goal of this operation is to reconnect
as many pairs of vertices as possible. We are asked to output
the number of vertices disconnected after removing *v* and the
number of vertices disconnected after removing *v* and adding
a maximal edge.

# Approach

We split the problem into three parts.

## Part 1: Given size of subtrees, compute number of vertices disconnected.

One key observation we can make in this problem is that
the number of vertices disconnected by the removal of a vertex
is the sum of the products of the number of children in 
each of the vertices subtrees, if we consider a tree rooted at
the vertex.

The proof is very simple. Consider an arbitrary
vertex *v* in the tree. We root the tree at *v*. Because
there is exactly one path from one vertex to another in a tree,
every path from vertex *a* to *b* in the tree that goes through
*v* is cut by the removal of the edge *v*. All paths that include
vertex *v* are the ones that start at some subtree of *v*
and end at another subtree of *v*. The number of these paths is
exactly the sum of the products of the number of vertices in 
each subtree of the tree rooted at *v*. Also the sum of products
(AB + BC + AC ...) can be computed in *O(n)* with a very simple
algorithm shown later.

## Part 2: Compute size of subtrees of each node.

We now know how to compute the number of disconnected vertices for
vertex *v* given the number of vertices in each of the subtrees of
the tree rooted at vertex *v*. We now must find an efficient method
to compute the number of vertices in each of the subtrees of the
tree rooted at vertex *v*.

A naive method would be to just do a dfs or bfs from each vertex to
compute the size of each of its subtress. However this because *O(n^2)*
which is much too slow for the bounds on this problems. It turns out
there is a faster method to compute this quantity with some clever
preprocessing.

We can root the tree at an arbitrary vertex *r* and recursively compute
the size of each subtree of the vertex. We call this the descendant
labeling. For each vertex, the it's descendant labeling is the size
of the subtree of that node relative to a tree rooted at the arbitrary
vertex *r*. This can be done with one dfs.

Computing the size of the subtrees rooted at each vertex becomes very
simple using the descendant labeling. For each vertex that is a "child"
of the current vertex, given by our current labeling, we add the size
of that subtree to our list of subtree sizes. To compute the size of
the rest of the tree, we just compute the size of the entire tree
and subtract out the descendant labeling of the current vertex. With
this information, we can easily compute the subtree sizes for each
vertex.

## Part 3: Sum of products of numbers in a list.

Consider a list *[A, B, C, D]*. We can compute
*AB + BC + AC + AD + BD + CD* naively with 2 for loops over the list.
This is *O(n^2)*. Another way to compute this is to represent the sum
as *(A)(B) + (A + B)(C) + (A + B + C)(D)*. By computing these running
sums, *A, A + B, A + B + C*, we can create a very simple *O(N)*
algorithm for computing the sum of products of numbers in a list.

Combining, these 3 parts we can get a solution.

# Code

The following code computes the descendant labelings:

```cpp
int n;
vector<int> tree[MAXN];
int size[MAXN];
int pars[MAXN];

int dfs(int node, int par) {
    int total = 1;
    for(int next: tree[node]) {
        if(next != par) {
            total += dfs(next, node);
        }
    }
    pars[node] = par;
    size[node] = total;
    return total;
}
```

The following code computes the size of the subtrees.

```cpp
vector<int> splits[MAXN];

void find_splits() {
    for(int i = 0; i <= n; i++) {
        int parsize = size[0] - size[i];
        if(parsize > 0) {
            splits[i].push_back(size[0] - size[i]);
        }
        for(int next: tree[i]) {
            if(next != pars[i]) {
                splits[i].push_back(size[next]);
            }
        }
        sort(splits[i].begin(), splits[i].end(), greater<int>());
    }
}
```

The following code computs the sum of products and
the solution to the problem

```cpp
pair<int, int> max_pair() {
    int worst = -1;
    int fixed = -1;
    for(int i = 0; i <= n; i++) {
        int sum = 0;
        int run = splits[i][0];
        for(int j = 1; j < splits[i].size(); j++) {
            sum += run * splits[i][j];
            run += splits[i][j];
        }
        if(sum > worst) {
            worst = sum;
            fixed = sum - splits[i][0] * splits[i][1];
        }
    }
    return make_pair(worst, fixed);
}
```

We can put all these together to solve the problem.
