---
layout: post
title:  "Pathfinder Visualization"
date:   2022-04-25 22:39:00 -0700
categories: 
---
I had a bit of a mishap with my pathfinding algorithm, which suggested that it wasn't working quite as I expected.

![Degenerate Pathfinding]({{ "/assets/images/DegeneratePathfinding.png" }})

To verify operation, I moved the algorithm into a dedicated GameObject.  Using a coroutine the incremental advancement of the algorithm could be visualized.

![Pathfinding Animation]({{ "/assets/images/PathfindingAnimation.gif" }})

As a result, some problems were identified.  But it produces a much nicer path now.
 
![Improved Pathfinding]({{ "/assets/images/ImprovedPathfinding.png" }})

