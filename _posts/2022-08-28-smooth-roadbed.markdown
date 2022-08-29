---
layout: post
title:  "Smooth Roadbed"
date:   2022-08-28 20:54:00 -0700
categories: 
---

I wasn't happy with the roadbed.  It was a rough approximation of the road mesh, but on any sort of slope there would be "aliasing".  This was because the deformation of the terrain would grab all points under a road segment and lower them to the minimum height of the spline on that segment.

To make a smooth roadbed, the height should be interpolated between the start and end point of the segment.  This is done using bilinear interpolation.  This is made a bit more complicated because the road segment is a a somewhat arbitrary quadrilateral, not necessarily a rectangle.

After some research, I found [this post](https://iquilezles.org/articles/ibilinear/) by Inigo Quilez had the answer I was looking for.

The results are amazing. 

Before:
![No Bilinear Interpolation 1]({{ "/assets/images/NoBilinearInterpolation1.png" }})
After:
![With Bilinear Interpolation 1]({{ "/assets/images/WithBilinearInterpolation1.png" }})

Before:
![No Bilinear Interpolation 2]({{ "/assets/images/NoBilinearInterpolation2.png" }})
After:
![With Bilinear Interpolation 2]({{ "/assets/images/WithBilinearInterpolation2.png" }})


