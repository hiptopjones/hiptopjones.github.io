---
layout: post
title:  "DCEL Pathfinding"
date:   2022-03-19 14:05:00 -0700
categories: 
---
Now that we have a mesh, we need to connect our A* implementation to it.

The baseline requirement is to find all neighbors when given a vertex.  Based on some previous research, a doubly-connected edge list (DCEL) is a good way to navigate an arbitrary mesh.

I loaded the mesh into a DCEL and added a mapping between my Unity Vector3 positions and the DCEL Vertex instances.  The mapping allowed locating any vertex in the DCEL without much fuss.

After changing the pathfinding algorithm to use the new graph, it generates very nice paths around hills, following the contours.  The spline smoothing helps make it even better.

![DCEL Pathfinding]({{ "/assets/images/DcelPathfinding1.png" }})

You could ask why it decided to go all the way around the hill, rather than directly over that col.  I would guess it’s a matter of scoring, and that there were no steps over that col that cost less than going around the peak.  But that’s a guess until I actually debug it.
