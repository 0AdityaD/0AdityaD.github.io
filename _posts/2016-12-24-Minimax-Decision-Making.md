---
layout: post
title: Game Decision Making
---

# m-ary Decision Trees

Trees in computer science can be used to represent and organize a variety of data. For example, binary search trees can be used to order data according to a defined ordering property. m-ary decision trees can be used to model to cascading effects of decisions to manipulate and change an initial state. A good way to imagine this would be to think of a simple coin flipping game where a player tries to guess the correct outcome of a coin flip. After n turns in the game, the set of possible coin histories has a cardinality of 2^N because there are 2 choices for each of the n coins. These 2^n coin spaces can be thought of as 2^N paths from the root of a tree to its leaves. An example is shown below.

![Coin Flip Decision Tree]({{ site.baseurl }}/images/dtht.gif "Coin Flip Decision Tree")

These trees can also be used to model more complex sets of states and actions such as a game of chess or a game of tetris. The nth level in a tree represents the possible game states n moves after the initial state. Each node in the tree has a variable number of children depending on the number of possible actions or moves possible from the current game state. This tree is known as a decision tree, and it can be used to "lookahead" in games as a form of artificial intelligence or even in knapsacking. As is obvious from the game chess, these trees can get extremely large very quickly. In fact the rate of growth of these trees is O(m^n) where m is the max number of children of a node in the tree and n is its height.

The state of the game at each node can be evaluated using a game scoring function. In order to determine the best sequence of moves from a certain game state max game state path can be recursively calculated by performing a depth first search on the nodes of the tree. However, the expoonential growth of game states presents a scalability issue that limits our ability to use brute force depth first search to select best possible moves when playing a game. One naive solution to this issue would be to prune dead end paths that lead to very low scoring states before reaching a leaf. However, in practice this method does not help solve the scalibility issue. This is the case especially in games that require significant "look ahead" to observe differences in game state such as chess.
