---
title: "LC Tips" 
date: 2026-01-05
tags: []
weight: -3800
---

I spent about two weeks working on the grind 75 peoblems, and would like to record some stuffs.
Perhaps There are more stuffs and I'd share in later post. 

#### implicit stack space complexity
When dealing with a DFS/BFS problem, remember to calculate the implicit time and 'space' complexity for the recursive call. 

#### Neighbor walking trick 
I was using somthing like `{{0,-1}, {0, 1}, {-1, 0}, {1, 0}}` for neighbor grid searching, and find out that there is a method to get the `i,i+1` of `{0, 1, 0, -1, 0}`.
This saves a little time for writing.

#### Remember vector<> copy cost is high
Sometimes I store a vector, and copy it, or in most of the case I traverse the 2D vector without using reference, this can create a lot of unnecessary run time.

#### Getting k smallest number can be O(klgN)
You can build a min heap in `O(N)`, and find each element wiht `lgN`!

#### Many thin can be prefixed
In LC-3756, we use prefix sum, prefix power, prefix count, and this is interseting.

#### Be careful for the stack for +/-/x//
when doing somthing like `3 2 -`, when we extract the number from the stack, the order is reversed.

#### Union Find is powerful
Learn to write the union find method, and do path compression based on the node size (some use the node height, both are having amortized O(1) complexity).

#### Reserve vector is useful
many problems would have a upper bound for the final length of a vector, and this can be reserved first. You might need a really large length of vector, and it would be really time consuming if did not do reserve.

#### Container copt cost is also high
Sometimes doing a double loop iteration, I forgot to use reference for the outer loop and this will copy the would container and get a worse runtime.

#### Do not iteratively call substr
The complexity for substr is `O(N)`, and would cause a lot of time in a searching loop. I try to record the index range in a pair, and then get the final answer withint that range.

#### Think to narrow case for DP
Some problems have a lot of combinations for the DP, but actually we can represent by some category, and do not need to calculate that much.

#### Minimum subsequence
This is a really interesting problem, and I originally though it might be `O(N^2)` with DP, and find that we can maintain a monotonic stack to do this.

