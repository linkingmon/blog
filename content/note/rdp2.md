---
title: "Image processing - Chaikin Smoothing"
date: 2026-04-10
tags: ["image", "chaikin"]
---

## Introduction 
Follow by the RDP algorithm, we imeplemtat hte chakin smoothhing algorithm. Rhe algorithm tries to smooth the curve by adding new points witin each segmetns:

For the following example, we can see that we try to split each edge into 3 segements (that is insert 2 points as the red dots), and then tried to connect them.

![](../rdp/chaikin.jpg)

## Implementation
This function is simple nad the input are a list of points and iteration number. Notice that in each iteration, the number of points would become doubled!

```c++
static std::vector<Point2f> Chaikin(const std::vector<Point2f>& pts, int iters) {
    if (iters <= 0 || pts.size() < 3) return pts;
    std::vector<Point2f> cur = pts;
    for (int it = 0; it < iters; ++it) {
        std::vector<Point2f> nxt;
        nxt.reserve(cur.size()*2);
        nxt.push_back(cur.front());
        for (size_t i = 0; i+1 < cur.size(); ++i) {
            const auto& p0 = cur[i];
            const auto& p1 = cur[i+1];
            Point2f q{0.75f*p0.x + 0.25f*p1.x, 0.75f*p0.y + 0.25f*p1.y};
            Point2f r{0.25f*p0.x + 0.75f*p1.x, 0.25f*p0.y + 0.75f*p1.y};
            nxt.push_back(q);
            nxt.push_back(r);
        }
        nxt.push_back(cur.back());
        cur.swap(nxt);
    }
    return cur;
}
```
