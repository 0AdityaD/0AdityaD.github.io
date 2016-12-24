---
layout: post
title: Minimax Decision Making
---

# m-ary Decision Trees

Trees in computer science can be used to represent and organize a variety of data. For example, binary search trees can be used to order data according to a defined ordering property. m-ary decision trees can be used to model to cascading effects of decisions to manipulate and change an initial state. A good way to imagine this would be to think of a simple coin flipping game where a player tries to guess the correct outcome of a coin flip. After n turns in the game, the set of possible coin histories has a cardinality of 2^N because there are 2 choices for each of the n coins. These 2^n coin spaces can be thought of as 2^N paths from the root of a tree to its leaves. An example is shown below.

![an image alt text]({{ site.baseurl }}/images/dtht "Coin Flip Decision Tree")
