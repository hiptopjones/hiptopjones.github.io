---
layout: post
title:  "Unnatural Paths"
date:   2022-03-19 12:42:00 -0700
categories: 
---
We now have a smooth mesh, but this still produces unnatural paths.

![Unnatural Path]({{ "/assets/images/UnnaturalPath1.png" }})

Paths are now awkwardly straight and have weird short steps.

![Short Steps]({{ "/assets/images/UnnaturalPath2.png" }})

I really want the paths to follow the actual contours better, but the cost model provided to A* drives everything, and straight lines are simply lower cost.  Plus, if it did try to follow the contour, it would be very jagged due to the low resolution grid and I wouldn’t be happy with that either.

Some of this can be resolved by changing the cost model, or adding other considerations into the model, like incentivizing longer runs.  But the search space is still fundamentally a grid, and the path will snap to the vertices of that grid.

I’m wondering if a different approach might be useful.

> NOTE: An unresolved question related to this is what the scale of the terrain is.  What does one unit correspond to. If I were a car or a person on the surface of the terrain, how high is the highest point, and how low is the lowest point relative to me.  How long would it take me to walk across the chunk of terrain?

> The jagged paths appear to be a problem, but what do they look like at the scale of a person on the surface of the mesh. Especially if they were spline-smoothed.  Is it just a set of switchbacks, or is it still awkward and unnatural?
