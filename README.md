# Cech Complex & Betti Number Calculator (Mathematica)

A small Mathematica library for constructing Čech complexes from finite point clouds and computing Betti numbers (homology over **Z₂**).
The project was written for computational topology exercises and contains functions to generate random point sets, build the Čech complex for a given radius, compute boundary matrices, calculate ranks (mod 2) and produce Betti numbers. Simple 2D/3D visualizations are included.

Project created as a final project for the course **‘Computational Topology’**.

---

## 🧠 Features

* Construction of a Čech complex from a finite set `A` of points and radius `r`.
* Computation of Betti numbers `β₀, β₁, β₂, ...` over **Z₂** using boundary matrices and rank computations.
* Utility to generate random point clouds inside an axis-aligned hyperrectangle.
* Basic 2D and 3D visualizers for the Čech complex (points, edges, polygons, and disks).
* Example inputs including classic test complexes (torus, Klein bottle) and interactive `Manipulate` examples.

---

## 🔧 Requirements

* Wolfram Mathematica (the code uses built-in functions such as `CircumscribedBall`, `ConvexHullMesh`, `Graphics`/`Graphics3D`, and `Manipulate`).
* Tested on Mathematica notebooks for small to medium point sets. Large point sets may be slow due to combinatorial enumeration of subsets.

---

## 🚀 Quick start

1. Open the provided Mathematica notebook (or copy the functions into a new notebook).
2. Define or generate a point set `A` and choose radius `r`.
3. Call `CechComplex[A, r]` to obtain the Čech complex (list of simplices represented as lists of vertices).
4. Call `calculateHomology[complex]` to obtain Betti numbers as ` {β0, β1, β2, ...}` (computed over `Z2`).

Example (Mathematica-style usage):

```mathematica
(* generate or provide A *)
A = {{1,1},{2,1},{1.5,2.2},{5,5},{6,5},{5.5,6.2}};
r = 0.662;

complex = CechComplex[A, r];
betti = calculateHomology[complex];  (* result: {β0, β1, β2, ...} *)
```

---

## 🧩 Main functions

Below is a summary of the key functions included in the project and their purpose.

* `GenerateRandomA[n_, lowerCorner_, upperCorner_, k_]`
  Generate `k` random points in `ℝ^n` inside the axis-aligned hyperrectangle given by `lowerCorner` and `upperCorner`. Performs basic validation of corners and dimensionality.

* `CechComplex[A_, r_]`
  Build the Čech complex for point set `A` and radius `r`. Simplices are represented as lists of points (the actual coordinates). For each candidate subset of vertices it checks whether the subset can be covered by a ball of radius `≤ r` using `CircumscribedBall`. For moderately sized `A` the routine builds higher-dimensional simplices iteratively by checking faces.

* `CountSimplices[cechComplex_]`
  Returns counts of simplices by dimension.

* `BoundaryMatrix[complex_, dim_]`
  Builds the boundary map matrix (over `Z2`) from `dim`-simplices to `(dim-1)`-simplices. The matrix uses `1` to indicate a face relation (subset).

* `computeRankOfMatrix[A_]`
  Computes the rank of a binary matrix `A` modulo 2. Implements a row-selection / column pivoting style algorithm over `Z2`.

* `calculateHomology[complex_]`
  Computes Betti numbers for all dimensions present in `complex`. Uses the boundary matrices and the formula
  `β_k = (number of k-simplices) - rank(∂_k) - rank(∂_{k+1})`
  where ranks are taken over `Z2`.

* `VisualizeCechComplex[A_, r_, k_, Betti_?BooleanQ]`
  Visual helper for 2D point clouds. Shows points, disks of radius `r` (as semi-transparent disks), edges and filled polygons for higher-dimensional faces (using convex hull polygons for visualization). Optionally displays Betti numbers computed by `calculateHomology`.

* `Draw3DPoints[data_]`
  Simple 3D visualization used for small 3D examples (points, edges, triangular faces).

---

## 🔍 Examples & tests

The notebook includes several example cases and interactive `Manipulate` tests:

* Small 2D examples (exercise/test inputs used during coursework).
* Random point clouds generated with `GenerateRandomA`.
* 3D tetrahedron example (four vertices forming a tetrahedron) to demonstrate `Draw3DPoints` and homology in 3D.
* Constructed simplicial complexes for classical topological spaces (torus and Klein bottle triangulations) with expected Betti numbers (e.g. `{1,2,1}` for a torus over `Z2`).

Sample output formats:

* A Čech complex is returned as a list of simplices, each simplex being a list of its vertex coordinates, e.g.

  ```
  {{{1,1}}, {{2,1}}, {{1,1},{2,1}}, {{1,1},{1,2},{2,1}}, ...}
  ```
* Betti numbers are returned as `{β0, β1, β2, ...}`, where `βi` is the dimension of `H_i(·; Z2)`.

---

## 🛠️ Implementation notes & limitations

* **Čech test using `CircumscribedBall`**: To decide whether a subset of vertices forms a simplex in the Čech complex we use `CircumscribedBall[subset]` and check whether the circumradius `≤ r`. Practically, this tests whether there exists a point common to all radius-`r` balls centered at the subset vertices. Using `RegionIntersection` directly on many balls was found to be unreliable for intersections of more than two regions in some Mathematica versions; `CircumscribedBall` is simpler and often sufficient for point clouds in general position.
* **Combinatorial explosion**: The naive enumeration of all subsets (`Subsets`) means the algorithm is exponential in the number of vertices. The implementation includes an iterative face-check strategy to build higher-dimensional simplices but still is best suited for small to medium `|A|` (e.g., up to a few dozen points depending on your machine).
* **Rank computation**: Rank is computed explicitly modulo 2 by a custom routine. For larger matrices, consider replacing it with a faster, optimized linear algebra routine specialized for GF(2) arithmetic (or porting to a library that supports sparse binary matrices).
* **Simplices representation**: Simplices are stored as lists of coordinate points. For better performance and more canonical indexing one could represent vertices by integer indices and store simplices as index lists.
* **Numerical stability**: Circumscribed ball computations and checks `<= r` may be sensitive to floating-point precision. Consider adding a small tolerance when comparing radii.

