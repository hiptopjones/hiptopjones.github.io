---
layout: post
title:  "Terrain Resolution"
date:   2022-03-19 11:51:00 -0700
categories: 
---
While investigating the unexpectedly jagged path, I looked more closely at the underlying mesh.

The mesh is a square grid of 1x1 quads on the XZ plane, with height values encoded in Y.  Since the height values come from Perlin noise, they should result in a smooth mesh.

However, there are a lot of unexpected ridges in the mesh.

![Mesh Variation]({{ "/assets/images/MeshVariation1.png" }})

Looking at it from another direction, it’s actually stratified.

![Stratified Mesh]({{ "/assets/images/MeshVariation2.png" }})

By applying the source noise map as the mesh texture, we can see that the grayscale values are not as we'd expect.

![Aliased Texture]({{ "/assets/images/MeshVariation3.png" }})

The height values at every point should be fairly unique due to sampling the noise curves, but here they appear to fall into just a few well-defined buckets.
