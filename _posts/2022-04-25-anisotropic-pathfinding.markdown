---
layout: post
title:  "Anisotropic Pathfinding"
date:   2022-04-25 21:14:00 -0700
categories: 
---
The paper I talk about [here]({% post_url 2022-04-01-procedural-generation-of-roads %}) is a goldmine, although it is very light on implementation detail, leaving most things to the imagination.

In any case, it turns out that accounting for slope in the direction of travel is a form of "anisotropic pathfinding", along with accounting for curvature, etc.

Also, it seems that using an extended neighborhood graph is a common solution for improving the realism of roads.  However, instead of recursing which is O(n<sup>2</sup>) for one additional level, they use an explicit set of (x, y) offsets where x < k and y < k and GCD(x, y) = 1.

![Extended Neighbors]({{ "/assets/images/ExtendedNeighbors.png" }})

Good to know I was on the right path with those ideas.