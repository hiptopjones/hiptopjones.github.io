---
layout: post
title:  "Recursive Neighbors"
date:   2022-03-19 14:15:00 -0700
categories: 
---
So following contours is pretty nice now, but traveling uphill still looks too jaggy.

![Jagged Path]({{ "/assets/images/RecursiveNeighbors1.png" }})

I’m going for the below effect, which seems impossible to achieve by following edges in the mesh.

![Real Switchbacks]({{ "/assets/images/RecursiveNeighbors2.png" }})

I changed the graph implementation to return neighbors two steps (and more) away by recursion during neighbor enumeration.  My hope was that this would allow for more gradual slopes than single steps, but it still wasn’t working.

Wait a minute.  Slope!  I realized my cost calculator was assuming a grid of 1x1.  Now that the graph is not a grid and I’m going to step more than 1 unit at a time, then I need to check slope, not delta.  Dummy.

![Smooth Switchbacks]({{ "/assets/images/RecursiveNeighbors3.png" }})

That creates a much more natural-looking road.  Still kind of weird angles, but could probably be smoothed to something believable.
