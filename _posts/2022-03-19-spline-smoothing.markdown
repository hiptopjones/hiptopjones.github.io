---
layout: post
title:  "Spline Smoothing"
date:   2022-03-19 11:05:00 -0700
categories: 
---
I could see that the path was roughly following the contours, but it was super jagged.

After some research, I decided to try smoothing it using Catmull-Rom splines.  **Habrador** had some ready-made code for this.

* [Everything about interpolation in Unity with C# code](https://www.habrador.com/tutorials/interpolation/1-catmull-rom-splines/)

Of course, this didn’t really relax the path.  It just made it less pointy.

![Contour Texture]({{ "/assets/images/SplineSmoothed.png" }})
