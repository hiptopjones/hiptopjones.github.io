---
layout: post
title:  "Delaunay Triangulation"
date:   2022-03-19 13:47:00 -0700
categories: 
---
Now that we have vertices that follow the contours of the map, we need to create a mesh.

Getting to this point took a lot of research, including a long drive into Computational Geometry.

* Computational Geometry: Algorithms and Applications - Mark de Berg, et al.
* Computational Geometry in C - Joseph O'Rourke
* [CMSC 754 - Computational Geometry](https://www.cs.umd.edu/class/spring2020/cmsc754/lectures.html) - David Mount

When I was thinking about the contour problem, I explored Voronoi diagrams and Lloyd relaxation as a possible solution to get a single line of points along a contour and have them evenly spaced.  This exploration was not fruitful, but it did lead me to the dual of the Voronoi diagram, which is the Delaunay triangulation.

Once I saw what Delaunay could do, it was clear that was the solution for creating the mesh I needed.

Rolling my own implementation of the algorithm is something I'd like to do, but it would be significant work to put on the critical path of just validating my hypothesis.  **Paul Bourke** came up as the solution again (remember CONREC?).

* [Triangulate](http://paulbourke.net/papers/triangulate/)
* [Delaunay Triangulation in .NET 2.0 by Morten Nielsen](http://paulbourke.net/papers/triangulate/morten.html)

Hooking this up resulted in an instantly beautiful result.

![Delaunay Triangulation]({{ "/assets/images/DelaunayTriangulation1.png" }})
