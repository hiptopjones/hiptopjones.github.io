---
layout: post
title:  "Navigation Mesh"
date:   2022-03-19 12:51:00 -0700
categories: 
---
The path below is really awkward because it is following the mesh grid.

![Unnatural Path]({{ "/assets/images/NavigationMesh1.png" }})

![Grid-based Path]({{ "/assets/images/NavigationMesh2.png" }})

Since the heightmap is a grid, the graph structure used during pathfinding will always navigate the terrain in a grid pattern.  In other words, the graph's toplogy guides the pathfinder.  A solution then is to create a mesh or graph that is dedicated to pathfinding activities.  So-called navigation meshes are common and often used for AI pathfinding.

Here are some examples of where we might want to use a pathfinding graph that does not map 1:1 to the terrain mesh.

* Create a graph with nodes distributed along contour lines.  This would enable roads that conform nicely to contours.
* Retain the simple single-octave noise map for pathfinding, while using a more complex noise map for terrain generation.  This would enable the road to cut through smaller features, rather than navigating around every little bump.