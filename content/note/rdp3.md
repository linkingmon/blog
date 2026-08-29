---
title: "Image processing - Zhang-Suen Thinning"
date: 2026-07-29
tags: ["image", "zhang-suen"]
---

# Introduction
The next step is to implement the thinning algorithm, where we use the zhang-suen thinning algorithm. This method aims to remove all the points until becoming a single line (skeleton).

The algoritmh check all colored points (will call black), then see if the neighbor's black and white is under some condition, if the condition mets the black point would be removed (that is become white).

# Implementation
1. `CountNonZero8` (result as `A`)
* count how many `1`'s in the 8 neighbors.

2. `Transitions01` (results as `B`)
* count how many `0` to `1` for 8 neighbors.

3. Two pass checking (slightly differnet)
* `A` should be `2~6`
* `B` should be `1`
* `p2` or `p4` or `p6` should be `0` (`p2`, `p4`, `p8` in second pass)
* `p4` or `p6` or `p8` should be `0` (`p2`, `p6`, `p8` in second pass)

denoting as:
```bash
p9 p2 p3
p8 p1 p4
p7 p6 p5
```

# Intuition
the intuition for the `B` to become one is that `B` means for that point's neighbor, how many groups did they formed.
If `B` is 0 that means this point is isolated, if `B` is 1 that means removing this point would not affect the connection topology. For larger than 2, it means that removing would make it unconencted

```bash
  1 1 1          1 1 1        1 1 1
  1 1 0          0 1 0        0 1 0
  1 0 0          0 1 1        1 0 1
< B = 1 >      < B = 2 >    < B = 3 >

```


For the two-pass checking part, it is seperated into two different direction to avoid removing a 2-pixel wide structure.

```bash
1 1    becomes    0 0
1 1  ---------->  0 0
1 1               0 0
```
