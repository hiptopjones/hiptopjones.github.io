---
layout: post
title:  "Layman's Teardown: Piecewise Clothoids"
date:   2022-03-29 18:33:00 -0700
categories: 
---
The paper we are going to teardown is the following:

* [Sketching Piecewise Clothoid Curves](http://www.dgp.toronto.edu/~mccrae/projects/clothoid/sbim2008mccrae.pdf) by J. McCrae and K. Singh

## 1. Introduction

The paper deals specifically with the concept of capturing hand sketches (e.g. from a drawing tablet) in a graphics program.  The intent is to turn the input (an ordered set of sampled points) into smooth, mathematically defined curves that can be manipulated intelligently within the program.

The paper suggests that "piecewise representations" are typically used for curves in graphics programs, where complex shapes are represented by stitching together simpler curves (curve primitives).  The paper doesn't address this, but presumably piecewise representations are used because approximating complex shapes with a single curve would be complex or impossible.

The paper states that most research on curve primitives has focused on so-called "parametric polynomial" curves like Bezier or NURBS.  The paper also states that "dense piecewise linear representations" are also increasingly popular.  This is referring to a curve described by a polyline, or basically leaving it as a set of sampled points.  However, this bag of points has no underlying structure and the curve it describes cannot be manipulated in meaningful ways.

This paper focuses on clothoids as a way to represent sketches, which have traditionally been out of reach due to computational complexity.  Increased computing power has overcome this.  The paper states that clothoids have fairness properties that make them more appropriate representations for sketching.

This section from [Clothoid Splines](https://thegraphicsblog.com/2018/04/30/clothoids-the-perfect-curve/) provides some additional clarity on what the problem is with parametric polynomial curves:

> **Why "Modern" Polynomial Splines Suck**
>
> It's funny.  A hundred years ago engineers had a perfectly good, mathematically robust means of drafting curves.  Curvature was easy to control, with specialized spline sets and curve templates (e.g. French curves) that made smooth curved shapes easy to make.
>
> And then Bezier came on the scene, followed by de Boor, and set engineering drafting back to the dark ages.  Modern computers can replicate the robustness and ease of traditional drafting tools, but this wasn't true at the dawn of CAD in the late 60s.  The need for fast, simple algorithms drove first Bezier, and then de Boor to embrace the idea of polynomial-based splines.
>
> Simple polynomial splines have an enormous number of problems. They can't be represented in arc length form.  It is extremely difficult to control their curvature, which is often "wavy."  But most of all it just doesn't make sense mathematically to split curves into independent x, y, and z functions.
>
> Traditional drafting understood this.  The wooden splines of old guaranteed curvature continuity in a way that's very difficult to do with simple polynomials today.  Curve templates were based on the "ideal elastica", which was alternatively called the clothoid, the Cornu spiral, the Euler spiro, and the French curve (in the art world).

### 1.1 Problem statement

Passing sketched data through traditional smoothing and spline-fitting techniques does not result in the cleanest output.  The paper claims to deliver better results using clothoids.

### 1.2 Overview of our approach

The paper's approach has two primary steps:

1. Fit a piecewise linear approximation to the curvature graph
 - Calculate the curvature at each sample point
 - Create a set of lines that approximates this curvature
 - Ensure that the approximation balances achieving a maximum fit with a minimum number of segments
 - Depending on the start and end curvature values, each line segment describes a line, a cirular arc, or a clothoid
 - Taken together, the curvature values form a repreesntation of the original input with G<sup>2</sup> continuity
2. Identify a transform to position the new curve properly
 - The approximation contains no position or orientation information
 - Position and orientation is achieved by using a fitting method (least squares optimization)
 - Because it's an approximation, the curves run close to the original points, but not necessarily through them
 - If running through the points (interpolation) is required, additional specialized segments can be added to the composite curve
 - Sharp corners can also be enabled by supporting independent segment rotations


### 1.3 Contributions

The paper claims to introduce a new way to efficiently describe an ordered set of points to clothoid primitives.

- The approach is applicable to other types of curve primitives
- The approach cuts through sketching noise, capturing underlying shapes without wiggles
- The approach can be adapted to support other curve features, such as intentionally sharp corners
- The approach is shown within the context of a real application for sketching road segments

## 2. Related work

Not repeating or interpreting anything in this section.

## 3. Clothoid terminology

This section discusses the mathematics of Clothoids and how to approximate them in a computationally efficient way.  Nothing to add.

## 4. Curve fitting using clothoids

The approach here is designed around an ordered set of points (e.g. captured from an input device), but it can be applied to anything representable that way.  For example, a Bezier curve could be sampled, and those samples could then be passed through this algorithm.

### 4.1 Discrete curvature estimation

This section discusses how to extract curvature from the input points.  Some different methods are discussed, but the formula provided is straightforward for any three consecutive points in the sampled data.

The output of this step is a new set of points in "curvature space".  Curvature space is a coordinate system where Y values trace curvature and X values trace arc length.  Positive curvature values result in turning to the right.

### 4.2 Piecewise linear curvature segmentation

This section identifies a set of linear segments that approximate the curvature values from the previous section.

The problem to be solved here here is known as segmented regression, and a more detailed explanation of the dynamic programming algorithm suggested can be found in Chapter 6 of the book "Algorithm Design" by Jon Kleinberg and Eva Tardos.  Specifically, section 6.3 "Segmented Least Squares".

This book appears to be a popular textbook for university courses, so there is lots of course material online.  Most slide decks seem to reproduce what's in the book, but [this document](https://www.cs.ubc.ca/~irmtraud/cs_320/lecture_notes/cpsc_320_chapter_7.pdf) from UBC contains some useful extra color.

Interestingly, the algorithm described in the textbook uses a array for M, whereas the paper uses a matrix.  Otherwise, the algorithm seems pretty identical.

In the paper, it assumes the solution to the problem is "a set of connected line segments in the curvature space", but that's missing a step.  The "Segmented Least Squares" algorithm finds lines of best fit, which results in line segments with endpoints that are not in the original points.  In other words, all line segments are likely disconnected from neighboring segments.  To connect the segments and achieve G<sup>0</sup> continuity on the segmented regression, the line segment endpoints need to be extrapolated and intersection points calculated with its neighbors.

> The source code from the paper's author is available on the [project page](http://www.dgp.toronto.edu/~mccrae/projects/clothoid/).

### 4.3 Segment parameterization

This section identifies how to use the start and end coordinates of the clothoid to determine the "parameterization" of the clothoid.  Basically, how to calculate the other points on the clothoid.

The start and end points only map to one clothoid.  This clothoid (like any clothoid) is composed of start and end parameters (t<sub>1</sub> and t<sub>2</sub>) and a scaling factor (B).

Using the equations provided in this section, we can work out what the values of t<sub>1</sub>, t<sub>2</sub> and B are for the clothoid that describes this part of the composite curve.

With these values in hand, we can use the equations provided in Section 3 to calculate any point along the clothoid.  This is of course necessary for drawing the curve, among other things.

### 4.4 2D rigid transformation

This section discusses how to translate and rotate the composite curve to best fit the input sample points.

Translation aligns the centroid of the original set of points with the corresponding arc-length parameterized points in the clothoid.

Rotation is implemented by minimizing the error between the two sets of points.  Formulas are provided.

## 5. Fitting extensions

### 5.1 Sharp corners (G<sup>1</sup> discontinuity)

This section discusses support for auto-detecting sharp corners in the sketch and preserving them, rather than ignoring them.

This auto-detection is performed by thresholding on high curvature / high variation of curvature.

Each detected corner forces a break in the composite curve, which affects downstream processes like the 2D rigid transformation step.

### 5.2 G<sup>3</sup> continuity

The default composite curve is G<sup>2</sup> continuous, but more continuity may be needed for the application.  Thissection shows how additional work can be added to support G<sup>3</sup> continuity.

### 5.3 Geometric interpolation

Piecewise clothoids as described here are an approximation of the underlying sample points.  They do not necessarily pass through any of them.

This sub-section talks about the case where you wish for the curve to pass through specific sample points, or interpolate those points.  An example application might be editing an existing curve, which this paper calls "over-sketching".

To support this interpolation, an additional curve primitive is added: Quintic Hermite splines.  There are trade-offs.

## 6. Sketching applications

This section discusses how the research has been implemented. It also calls out that the curvature segmentation is the most computationally challenging part, as it can be O(N<sup>2</sup>).

## 7. Conclusion

Nothing to add here.
