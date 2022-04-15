---
layout: post
title:  "Layman's Teardown: Procedural Generation of Roads"
date:   2022-03-30 19:32:00 -0700
categories: 
---
> I find research papers to often be difficult to grok as they are written for people already immersed in that domain.  However, the concepts are generally accessible to the layman if expressed with appropriate context.  I am calling this a "Layman's Teardown" in reference to teardowns of complex electronic assemblies.

The paper we are going to teardown is the following:

* [Procedural Generation of Roads](https://hal.archives-ouvertes.fr/hal-01381447/file/paths-author-version.pdf) by E. Galin, A. Peytavie, N. Marechal, E. Guerin

## Introduction

This paper describes a method of generating roads through a landscape with increased realism.

The paper's pathfinding algorithm is **anisotropic**, which [Merriam-Webster](https://www.merriam-webster.com/dictionary/anisotropic) defines as "exhibiting properties with different values when measured in different directions".  Specifically, while many pathfinding algorithms assign costs to a position, this anisotropic algorithm also accounts for the direction of travel (velocity), and even how much the path is curving at that point (curvature).

Furthermore, the paper explores how to reduce the "limit on direction" problem.  Most pathfinding algorithms operating on a uniform grid allow movement in 90 degree increments (4 directions) or 45 degree increments (8 directions).  This constrained movement is referred to by the authors as "limit on direction" and results in paths that are jagged and look unnatural.  This algorithm supports smaller angular increments to achieve a greater number of directions.  It also extends that method to enable selection of tunnels or bridges, where a path would be excessively long or not otherwise possible.

Finally, the paper presents ideas around representation of the path structure (piecewise clothoids), modifying a terrain mesh to support the calculated path (excavation or banking), and placement of bridges and tunnels.

The paper covers a lot of topics, which makes it a very dense read.

## Related Work

Not repeating or interpreting anything in this section.

## Discrete Anisotropic Shortest Path Algorithm

This section describes the ideas around the pathfinding algorithm.

First, the cost-weighting function is introduced.  This function is a weighted sum of several user-defined cost functions that value some characteristic of the point. The user-defined cost functions are provided with the position, velocity, and acceleration of the point.  Since they look at the velocity and acceleration, they are direction-aware, and therefore the algorithm is "anisotropic".

The cost of a path from a to b is the sum of the costs for each point along that path.  The shortest path between a and b is the path that minimizes that sum.  In the continuous domain, this sum is the line integral.  To simplify the problem, they discretize the region into a uniform grid, or a grid with regular (e.g. square) spacing.  This turns a continuous problem into a finite problem.

To allow close approximation of the optimal path, the path must have a high degree of freedom in the directions it can travel from any point.  Because the angle to neighboring points in the grid is limited to 45 degree increments at best, pathfinding on a uniform grid typically results in paths that are jagged and would seem unnatural for a road.  If a very fine pitched grid was used instead, the jaggedness of the path would be less offensive to our senses, but it would introduce significantly more computation.  Taken to the extreme, a fine pitched grid would return us to a continous domain.

Instead, the paper uses an extended definition of the neighbors of a point.  By treating points that are further afield as neighbors, directional resolution can be increased.  This also increases computation, but less than the use of a fine pitched grid.

The authors mention memory usage associated with storing these connections, which they avoid through the creation of "generic path segment masks" that "store the connectivty patterns between grid points".  I presume that when they refer to a mask, they are simply referring to an array of relative vector offsets that can be easily applied to any point to retrieve the appropriate neighbors.

This section discusses the A* implmentation, but if any review is necessary I would suggest reviewing the extensive information about the A* algorithm [here](https://theory.stanford.edu/~amitp/GameProgramming/index.html).

## Cost Functions

This section further breaks down the cost functions.

Here we can see a formal definition of the cost-weighted function.  It is simply the sum of the results from underlying characteristic functions.   Actually, it's the sum of transfer functions **composed** with characteristic functions.

Transfer functions interpret and alter the result of the corresponding characteristic function.  Users can modify the transfer functions to influence the shape of the path.  There are examples in the paper where the transfer function is non-linear.  For example, small curvatures have reduced impact, but large curvatures have amplified impact.  Furthermore, a threshold is present after which curvatures will return an infinite cost, making that path effectively impassable. 

The cost functions take the position, velocity, and acceleration.  It is not stated how these are determined, but I assume it is some form of numerical differentiation.  It is trivial to calculate curvature given velocity and acceleration, so I assume that's how the cost functions work.

The sub-sections here discuss surface roads as well as bridges and tunnels.

The way that bridges and tunnels are described, it seems that some cost functions are designed specifically for evaluating those options.  In other words, costs are calculated with some context in mind.  This makes sense, as typical pathfinding only navigates a cost field, and does not carry knowledge or make choices about the method used to transit from one point to another (surface road, bridge, tunnel).

As we will see later, the decision to use a bridge or tunnel vs. a surface road is related to the segment mask introduced earlier.  The main segment mask assumes we're simply extending a surface road.  However, there are other segment masks that return "neighbors" on the other side of a bridge or tunnel.  The cost of navigating those segments must be calculated differently than the traditional surface road segments.  Therefore, in addition to the cost calculations being direction-aware, they are also aware of the type of segment being costed.

## Segment Path Masks

This section explains how path segment masks work.

During execution, the A* algorithm retrieves all neighbors of the current node and evaluates the costs of expanding the path in that direction.  Most grid graph implementations only return the 4 or 8 nodes immediately surrounding the current node.  The paper increases the possible directions available to the algorithm by introducing path segment masks.  These masks point to other nodes in the graph that can be treated as neighbors of the current node.

The first mask (for surface roads) the authors discuss specifies points that are within distance k from the origin (current point) in either x or y.  The line segments formed between the origin and each of those points in the mask are spaced at regular angles around the origin.  The angular resolution of the path can be improved by allowing points even further from the origin.

The paper defines the mask as a set of points (segments) in [-k, k]<sup>2</sup> such that the greatest common divisor of their coordinates relative to the current point is 1.  The significance of GCD = 1 is that it indicates the numbers are **relatively prime**.  That is, they don't have any other common factors.  This ensures that no point is included that is on the same line as another point.  For example, (1, 1) and (2, 2) would be on the same line.  GCD = 1 will ensure that only one point on the line is included, since having both would be redundant.  The second point is considered redundant because it would not add any additional angular resolution, which is what the segment mask is generally intended for.

Bridges and tunnels are introduced to the algorithm by adding even more neighbor points.  These new neighbors are defined as a band of points at a certain distance from the current point.  The closest points in the band are at the end of a bridge or tunnel of minimum length.  The furthest points in the band are at the end of a bridge or tunnel of maximum length.  To reduce the number of points under consideration, the paper uses stochastic sampling, which simply selects points from the masks at random.

Normal, surface road paths are built incrementally, a bit at a time.  However, a bridge or tunnel is considered as a whole unit.  It either is present or not.  We either punch through the mountain or not.  Bridges or tunnels can be thought of like wormholes, in that they close the gap between two distant locations.

The costs of the segments in the bridge or tunnel masks are calculated differently than the segments in the surface road mask.  This is because the bridge or tunnel (wormhole) option should only become cost-effective when a surface road is impossible or would shortcut a much more circuitous route.

In order to account for curvature and avoid sharp turns when possible, the paper extends the 2D (X, Y) positional grid into third dimension.  This new dimension does not track position, but rather represents orientation.  The possible range of orientation angles [0, 2pi] is discretized into some number of intervals that are indexed by the Z value of this new dimension.  The path segment masks are also extended into this third dimension.

> This orientation extension is hard to wrap my mind around, and I am unclear on exactly how the authors used it.  The path example in the paper seems to put the orientation in the point it applies from.  However, the orientation at a point p<sub>k</sub> is dependent on the point after it p<sub>k+1</sub>, which can change in a future iteration.  I would argue that the orientation of the current point (for the purposes of cost calculation) should be orientation of the line from the previous point.
>
> Unfortunately, there is no source code available for this paper, so there I cannot cross-reference an implementation to resolve ambiguities in the text.

Path segment masks seem to be a very simple concept, but are a powerful way of thinking about the problem, and their extension to briges and tunnels is convenient.

## Procedural Generation of Road Models

This section describes the graphical realization of the road.

The path of the road that was computed previously is a discrete set of points.  If those points are used for the road, then the road would be quite jagged.

### Trajectory Computation

### Road Generation

## Results

Realism

Control

Efficiency

## Conclusion
