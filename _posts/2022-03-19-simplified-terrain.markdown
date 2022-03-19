---
layout: post
title:  "Simplified Terrain"
date:   2022-03-19 11:38:00 -0700
categories: 
---
It's hard to visually understand the pathfinding results because the terrain is so variable.

I decided to do what I should have done earlier, which is to simplify the noise.  I went from 5 octaves of Perlin noise down to just one.

![Simple Terrain]({{ "/assets/images/SimpleTerrain.png" }})

This provides a rolling landscape with a very smooth and simple set of contours.

![Jagged Path]({{ "/assets/images/JaggedPath.png" }})

With the simplified terrain, we can see that the path generally follows the contours, but there are lots of weird path wiggles.