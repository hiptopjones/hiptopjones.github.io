---
layout: post
title:  "Pathfinding"
date:   2022-03-19 10:35:00 -0700
categories: 
---
My goal is to create a world I can drive in, so I need to somehow add in roads.

I accessed a lot of papers, tutorials, and articles on pathfinding.

Again, **Sebastian Lague** came through with a decent tutorial:

* [A* Pathfinding](https://www.youtube.com/watch?v=-L-WgKMFuhE&list=PLFt_AvWsXl0cq5Umv3pMC9SPnKjfp9eGW)

But the hero in this space was clearly **Amit Patel** at Red Blob Games:

* [Introduction to the A* Algorithm](https://www.redblobgames.com/pathfinding/a-star/introduction.html)
* [Amit's A* Pages](http://theory.stanford.edu/~amitp/GameProgramming/)

This is an absolutely stunning collection of clear explanations, visualizations, implementations, ancillary discussions.

Using these as guides, I was able to get a working implementation of A*.

I exported the noise map generated during procedural generation as a texture, and used it to create a mesh representing the terrain.  Not unlike what was already happening in the generation project.

Using raycasts, I was able to determine which triangle the mouse was hovering over, and also which vertex it was closest to.  This allowed me to create a vertex cursor and set the start and end point for the path at runtime.

After implementing a min binary heap and a grid graph, I had basic A* pathfinding working.

![Basic Pathfinding]({{ "/assets/images/Pathfinding1.png" }})

I ran into some issues with longer paths because the binary heap would exceed its maximum size.  This was due to the initial implementation adding duplicate nodes, rather than reusing and updating the cost of existing ones.  With some further changes, existing nodes could be updated, and the path length is basically unlimited.
