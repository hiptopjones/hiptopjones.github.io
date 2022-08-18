---
layout: post
title:  "Road Excavation"
date:   2022-08-17 21:44:00 -0700
categories: 
---
Took a few detours, but I found my way back to the terrain project this past weekend.

The goal was to place a road on the terrain that follows the landscape.  My minimum viable product required to not just place a road on the terrain, but also deform the mesh so that the road surface is flat even when traveling along the edge of a hill.

To start simply, I created a new project and pulled in the old height map and a few bits of the previous mesh generation code.

![Terrain Mesh]({{ "/assets/images/TerrainMesh.png" }})

I then used a set of waypoints to specify a meandering path, and converted that to a smooth spline using Catmull-Rom.

![Road Spline]({{ "/assets/images/RoadSpline.png" }})

Using the road spline, I then placed quads along either side to create a flat mesh representing the road surface.  At this point, the path knows nothing about the terrain.  It has no hills, only turns.

![Road Mesh]({{ "/assets/images/RoadMesh.png" }})

With the road mesh, I could gather up the vertices under each road segment and deform them.  The terrain height at the centerline (the spline) was used as a reference.  Terrain vertices lower than the centerline in that segment were raised and vertices higher were lowered.  This created a nice flat road bed for the road mesh to sit on.

![Terrain Excavation]({{ "/assets/images/TerrainExcavation.png" }})

Finally, I could conform the road mesh to the terrain mesh.  The road mesh vertices were lifted or lowered to match the road bed at each road segment.

![Road On Terrain]({{ "/assets/images/RoadOnTerrain.png" }})

This is looking really promising.

