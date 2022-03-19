---
layout: post
title:  "Smooth Mesh"
date:   2022-03-19 12:35:00 -0700
categories: 
---
Some background.  For terrain generation, I have a dedicated Unity project that uses inspector parameters to produce a heightmap which is saved to a PNG file on disk.  For pathfinding, I import that PNG file, and convert it back to a heightmap.

The resolution (or bit depth?) problem described previously appears to be due to some unintended processing step somewhere in the heightmap -> PNG -> heightmap pipeline.

I tried to determine what was going on and address it, but ultimately decided I was trying to be too clever / lazy using an image file for communication of the heightmap.  Even if these artifacts weren’t present, the resolution was too low and would require changing longer term anyway.  Currently, there were only 256 possible height levels the way I was using the PNG.

Instead, I decided to export a HeightTable.csv file from the terrain generaiton project that contains a flat list of height values, and use that for communication between the projects.

> NOTE: The consumer has to know what the width / height is, so probably would be better to use a more structured format like JSON where the data could be described.

This works well and produces a nice smooth mesh with continuously-varying height values.

![Smooth Mesh]({{ "/assets/images/SmoothMesh1.png" }})

![Smooth Texture]({{ "/assets/images/SmoothMesh2.png" }})