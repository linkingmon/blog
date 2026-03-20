---
title: "Image processing - RDP algorithm"
date: 2026-03-20
tags: ["image", "rdp"]
---

## Introduction
Recently I am working on a small  to process hand written curve and come up with the RDP algorithm, which stands for [Ramer–Douglas–Peucker algorithm](https://en.wikipedia.org/wiki/Ramer%E2%80%93Douglas%E2%80%93Peucker_algorithm). This is the iterative method to simpify the curve by recursively calculateing the distance between the curve, and leave the points that are far away from the curve, and this alogrith ends when the there are no points left.

The input is a vector of points, and each step we search for all the points distance, and find hte maximum distance one, and then we recursively call the function for two sides.

## Implementation Details
Following is an implemetatoin for the code, and implementation details
1. The `RDP` function
* input a list of points, and a threshold `eps`. 
* it will call `RDPRec` recursively and find out which points need `keep`
2. The `RDPRec` function
* would find the farest distance from `pts[l]` and `pts[r]` betweeb the trajectory.
* This point is keep, and will recurisively call the other two segments (see right hand side for the following figure)
* the process would end until the distance for all points betweeb is smaller than the threshold `eps`
3. The `PerpDist` function
* it would calculate hte perpendicular distance to point `l` and `r`, see left hand side for the following figure:

![](../rdp/rdp.png)

```c++
static std::vector<Point2f> RDP(const std::vector<Point2f>& pts, float eps) {
    if ((int)pts.size() <= 2) return pts;
    std::vector<uint8_t> keep(pts.size(), 0);
    keep[0] = keep.back() = 1;
    RDPRec(pts, 0, (int)pts.size()-1, eps, keep);
    std::vector<Point2f> out;
    out.reserve(pts.size());
    for (int i = 0; i < (int)pts.size(); ++i) if (keep[i]) out.push_back(pts[i]);
    return out;
}

static void RDPRec(const std::vector<Point2f>& pts, int l, int r, float eps,
                   std::vector<uint8_t>& keep) {
    if (r <= l+1) return;
    float best = -1.f;
    int idx = -1;
    for (int i = l+1; i < r; ++i) {
        float d = PerpDist(pts[i], pts[l], pts[r]);
        if (d > best) { best = d; idx = i; }
    }
    if (best > eps) {
        keep[idx] = 1;
        RDPRec(pts, l, idx, eps, keep);
        RDPRec(pts, idx, r, eps, keep);
    }
}

static float PerpDist(const Point2f& p, const Point2f& a, const Point2f& b) {
    float vx = b.x - a.x, vy = b.y - a.y;
    float wx = p.x - a.x, wy = p.y - a.y;
    float c1 = vx*wx + vy*wy;
    if (c1 <= 0) return std::hypot(p.x-a.x, p.y-a.y);
    float c2 = vx*vx + vy*vy;
    if (c2 <= c1) return std::hypot(p.x-b.x, p.y-b.y);
    float t = c1 / c2;
    float px = a.x + t*vx, py = a.y + t*vy;
    return std::hypot(p.x-px, p.y-py);
}

```

## Time Complexity 
In this case the algoriuthm complexity is `T(n) = T(k) + T(n-k) + O(n)`, and this depends on how the split is balanced.

If this is balanced we can have nearly `T(n) = 2T(n/2) + O(n)`, which results in overall complexity is `O(nlogn)`. However, we might also encounter really unbalnced case that explode hte complexity to `O(n^2)`

In this case the algoriuthm complexity is `T(n) = 2T(n/2) + O(n)`, and the overall complexity is `O(nlogn)`.

