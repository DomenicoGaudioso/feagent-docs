---
layout: default
title: "65 - Examples from Bhavikatti"
parent: English
nav_order: 65
---

# 65 - Examples from Bhavikatti

This page collects **all the worked examples** of the book *Finite Element Analysis* (S.S. Bhavikatti, New Age International, 2005) redone with feagent. For each example: reference in the book, feagent model, numerical comparison between the book value and feagent, notes (missing data, book misprints corrected, modelling differences). Texts are summarised in our own words; numerical data are given to redo the computation. The code of every example is in `validation/books/bhavikatti/`; to run them again:

```bash
python -m validation.books.run_books bhavikatti
python scripts/_gen_book_docs.py
```

**Outcome**: ✅ reproduced and compared; 🟡 partly reproduced (book data or results incomplete); 📐 derivation of formulas checked numerically; ⛔ not reproducible (reason in the notes). The *error* column is the largest relative error of the comparisons; *ref.* tells what the comparison is made against: book, exact solution or corrected value of a misprint.

**58 examples**: 21 reproduced, 2 partly, 35 derivations checked, 0 not reproducible; 361 numerical checks, 361 within tolerance.

## Summary

| Example | Title | Outcome | Checks | Max error |
|---|---|---|---|---|
| [Example 4.1](#bhav-ex-4-1) | Integrals of natural line coordinates | 📐 | 2/2 | 0.00 % |
| [Example 4.2](#bhav-ex-4-2) | Integrals of area coordinates over a triangle | 📐 | 4/4 | 0.00 % |
| [Example 4.3](#bhav-ex-4-3) | Integrals of volume coordinates over a tetrahedron | 📐 | 2/2 | 0.00 % |
| [Example 5.4](#bhav-ex-5-4) | Two-node bar from generalized coordinates | 📐 | 4/4 | 0.00 % |
| [Example 5.5](#bhav-ex-5-5) | Two-node beam: cubic Hermite functions | 📐 | 3/3 | 0.00 % |
| [Example 5.6](#bhav-ex-5-6) | CST triangle from generalized coordinates | 📐 | 2/2 | 0.00 % |
| [Example 5.7](#bhav-ex-5-7) | Two-node bar in natural coordinates L1, L2 | 📐 | 3/3 | 0.00 % |
| [Example 5.8](#bhav-ex-5-8) | Two-node bar with xi in [-1, 1] | 📐 | 2/2 | 0.00 % |
| [Example 5.9](#bhav-ex-5-9) | Three-node bar in natural coordinates | 📐 | 4/4 | 0.00 % |
| [Example 5.10](#bhav-ex-5-10) | CST in area coordinates | 📐 | 3/3 | 0.00 % |
| [Example 5.11](#bhav-ex-5-11) | Six-node LST triangle | 📐 | 4/4 | 0.00 % |
| [Example 5.12](#bhav-ex-5-12) | Four-node rectangle in natural coordinates | 📐 | 4/4 | 0.00 % |
| [Example 5.13](#bhav-ex-5-13) | Eight-node quadratic rectangle from the polynomial | 📐 | 5/5 | 0.00 % |
| [Example 5.14](#bhav-ex-5-14) | Four-node tetrahedron in volume coordinates | 📐 | 3/3 | 0.00 % |
| [Example 5.15](#bhav-ex-5-15) | Eight-node hexahedron from the trilinear polynomial | 📐 | 3/3 | 0.00 % |
| [Example 5.16](#bhav-ex-5-16) | Lagrange functions for 2, 3 and 5-node bars | 📐 | 5/5 | 0.00 % |
| [Example 5.17](#bhav-ex-5-17) | Nine-node Lagrangian rectangle | 📐 | 5/5 | 0.00 % |
| [Example 5.18](#bhav-ex-5-18) | Eight-node hexahedron with Lagrange functions | 📐 | 3/3 | 0.00 % |
| [Example 5.19](#bhav-ex-5-19) | Four-node rectangle by the serendipity approach | 📐 | 2/2 | 0.00 % |
| [Example 5.20](#bhav-ex-5-20) | Eight-node quadratic serendipity rectangle | 📐 | 3/3 | 0.00 % |
| [Example 5.21](#bhav-ex-5-21) | Twelve-node cubic serendipity rectangle | 📐 | 4/4 | 0.00 % |
| [Example 5.22](#bhav-ex-5-22) | Seven-node element by degrading the Q8 | 📐 | 5/5 | 0.00 % |
| [Example 8.1](#bhav-ex-8-1) | Three concurrent bars by virtual work | ✅ | 6/6 | 0.00 % |
| [Example 9.1](#bhav-ex-9-1) | Beam equation from the stationarity of the potential energy | 📐 | 5/5 | 0.00 % |
| [Example 9.2](#bhav-ex-9-2) | Three springs in series: direct and variational assembly | 📐 | 4/4 | 0.00 % |
| [Example 9.3](#bhav-ex-9-3) | Spring system by minimum potential energy | ✅ | 2/2 | 0.03 % |
| [Example 9.4](#bhav-ex-9-4) | Three concurrent bars by minimum potential energy | ✅ | 6/6 | 0.00 % |
| [Example 9.5](#bhav-ex-9-5) | Rayleigh-Ritz with a sine series on a simply supported beam | 🟡 | 6/6 | 0.00 % |
| [Example 9.6](#bhav-ex-9-6) | Rayleigh-Ritz on a fixed-fixed bar with a midspan force | ✅ | 6/6 | 0.00 % |
| [Example 9.7](#bhav-ex-9-7) | Rayleigh-Ritz on a hanging bar under self-weight | ✅ | 6/6 | 0.00 % |
| [Example 11.1](#bhav-ex-11-1) | Hanging stepped plate: self-weight and point load | ✅ | 5/5 | 0.00 % |
| [Example 11.2](#bhav-ex-11-2) | Tapered bar: stiffness and self-weight loads | 📐 | 4/4 | 0.00 % |
| [Example 11.3](#bhav-ex-11-3) | Tapered bar with self-weight and tip force | ✅ | 4/4 | 0.01 % |
| [Example 11.4 (bar)](#bhav-ex-11-4-bar-thermal) | Fixed-fixed aluminium-steel bar with force and temperature change | ✅ | 5/5 | 0.01 % |
| [Example 11.4 (truss)](#bhav-ex-11-4-truss3) | Three-bar triangular truss with apex load | ✅ | 9/9 | 0.04 % |
| [Example 11.5 (settlement)](#bhav-ex-11-5-settlement) | Truss of Ex. 11.4 with a support settlement | ✅ | 7/7 | 0.01 % |
| [Example 11.5 (braced frame)](#bhav-ex-11-5-frame6) | Braced rectangular truss: temperature and lack of fit | ✅ | 20/20 | 0.00 % |
| [Example 11.6](#bhav-ex-11-6) | Space tripod under a vertical load | ✅ | 6/6 | 0.33 % |
| [Chapter 11 Question 7](#bhav-prob-11-7) | Fixed-fixed stepped bar with an intermediate force | ✅ | 7/7 | 0.01 % |
| [Chapter 11 Question 8](#bhav-prob-11-8) | Right-angled triangular truss with two forces | ✅ | 6/6 | 0.00 % |
| [Chapter 11 Question 9](#bhav-prob-11-9) | Rectangular truss with one diagonal | ✅ | 6/6 | 0.00 % |
| [Example 12.1](#bhav-ex-12-1) | Rectangular panel with two CSTs and a horizontal force | ✅ | 15/15 | 0.25 % |
| [Example 12.2](#bhav-ex-12-2) | Consistent self-weight load on a CST | 📐 | 4/4 | 0.00 % |
| [Example 12.3](#bhav-ex-12-3) | Consistent loads of side pressures on a CST | 📐 | 6/6 | 0.00 % |
| [Example 13.1](#bhav-ex-13-1) | Jacobian and B matrix of a parallelogram at the Gauss point | 📐 | 13/13 | 0.01 % |
| [Example 13.2](#bhav-ex-13-2) | Cartesian coordinates of a point given in natural coordinates | 📐 | 2/2 | 0.00 % |
| [Example 13.3](#bhav-ex-13-3) | Nodal forces equivalent to an interior point force | 📐 | 14/14 | 0.00 % |
| [Example 13.4](#bhav-ex-13-4) | Nodal forces of a uniform traction on a Q4 side | 📐 | 6/6 | 0.00 % |
| [Chapter 13 Question 7](#bhav-prob-13-7) | Direct and inverse mapping in a Q4 | 📐 | 5/5 | 0.00 % |
| [Chapter 13 Question 8](#bhav-prob-13-8) | Jacobian and B of a rectangle at the Gauss point | 📐 | 7/7 | 0.01 % |
| [Example 14.1](#bhav-ex-14-1) | Continuous beam fixed-propped-propped with different loads | ✅ | 14/14 | 0.01 % |
| [Example 14.2](#bhav-ex-14-2) | Fixed-propped beam with a midspan force | ✅ | 10/10 | 0.01 % |
| [Example 14.3](#bhav-ex-14-3) | Consistent trapezoidal load on a beam element | 📐 | 4/4 | 0.00 % |
| [Example 14.4](#bhav-ex-14-4) | Consistent load vector of a two-span beam | 📐 | 6/6 | 0.00 % |
| [Example 14.5](#bhav-ex-14-5) | Plane portal frame: element matrices and solution | ✅ | 24/24 | 0.10 % |
| [Chapter 14 Question 2](#bhav-prob-14-2) | Continuous beam on three supports with uniform load | ✅ | 4/4 | 0.00 % |
| [Chapter 14 Question 4](#bhav-prob-14-4) | Global matrix of a frame element inclined at 30 degrees | ✅ | 13/13 | 0.08 % |
| [Section 14.5](#bhav-sec-14-5-timoshenko-locking) | Two-node Timoshenko beam: shear locking and reduced integration | 🟡 | 19/19 | 0.02 % |

## Chapter 4

<a id="bhav-ex-4-1"></a>

### Example 4.1 - Integrals of natural line coordinates

📐 · PDF page 56 · other

Use of the closed formula int L1^p L2^q dx = p! q! l/(p+q+1)! on two cases: L1^2 (result l/3) and L1^3 L2 (result l/20).

**Model**: feagent 2-node bar (L2): its shape functions are the line coordinates L1, L2; numerical integral with 4-point Gauss-Legendre and |dx/ds|.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| (i) int L1^2 dx = l/3 | book | 1.2333 | 1.2333 | 0.00 % | ✅ |
| (ii) int L1^3 L2 dx = l/20 | book | 0.185 | 0.185 | 0.00 % | ✅ |

**Notes**: Purely mathematical: the formula is checked by numerically integrating the shape functions of the feagent bar over a bar of arbitrary length (l = 3.7).

*Code*: `validation/books/bhavikatti/ch04.py`

<a id="bhav-ex-4-2"></a>

### Example 4.2 - Integrals of area coordinates over a triangle

📐 · PDF page 62 · other

Formula int L1^p L2^q L3^r dA = p! q! r! 2A/(p+q+r+2)! applied to L1 L2 L3, L1^3 L2 and L1^2 L2^2 L3^3.

**Model**: feagent T3 triangle (PlaneT3): shape functions = area coordinates; quadrature feagent.solid.gauss_tri(6) and element area.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| (i) L1 L2 L3: A/60 | book | 0.092167 | 0.092167 | 0.00 % | ✅ |
| (ii) L1^3 L2: A/60 | book | 0.092167 | 0.092167 | 0.00 % | ✅ |
| (iii) closed formula = A/7560 (book: A/7960) | corrected | 7.3148e-04 | 7.3148e-04 | 0.00 % | ✅ |
| (iii) numerical integral | corrected | 7.3148e-04 | 7.3148e-04 | 0.00 % | ✅ |

**Notes**: Case (iii): the book prints A/7960, a misprint; the correct value is 2! 2! 3! 2A/9! = A/7560, confirmed by numerical integration (source = corrected).

*Code*: `validation/books/bhavikatti/ch04.py`

<a id="bhav-ex-4-3"></a>

### Example 4.3 - Integrals of volume coordinates over a tetrahedron

📐 · PDF page 65 · other

Formula int L1^p L2^q L3^r L4^s dV = p! q! r! s! 6V/(p+q+r+s+3)! applied to L1 L2 L3 L4 (V/840) and L1^2 L2 L4 (V/420).

**Model**: feagent Tet4 tetrahedron: shape functions = volume coordinates; quadrature feagent.solid.gauss_tet(4) and element volume.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| (i) L1 L2 L3 L4: V/840 | book | 0.0029244 | 0.0029244 | 0.00 % | ✅ |
| (ii) L1^2 L2 L4: V/420 | book | 0.0058488 | 0.0058488 | 0.00 % | ✅ |

**Notes**: Numerical check on an irregular tetrahedron.

*Code*: `validation/books/bhavikatti/ch04.py`

## Chapter 5

<a id="bhav-ex-5-4"></a>

### Example 5.4 - Two-node bar from generalized coordinates

📐 · PDF page 75 · bar

From the polynomial u = a1 + a2 x imposed at the two nodes the book obtains N1 = (x2 - x)/l and N2 = (x - x1)/l.

**Model**: feagent 2-node BarElement between x1 = 1.5 and x2 = 4.2; comparison at 25 points.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |N_book - N_feagent| | book | 0 | 2.2204e-16 | 0.00 % | ✅ |
| max |[1 x] A^-1 - N_feagent| | book | 0 | 1.5266e-16 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 0 | 0.00 % | ✅ |

**Notes**: The inversion of the generalized-coordinate matrix is also repeated with numpy.

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-5"></a>

### Example 5.5 - Two-node beam: cubic Hermite functions

📐 · PDF page 76 · beam

From w = a1 + a2 x + a3 x^2 + a4 x^3 with w and dw/dx imposed at both nodes the book obtains the four cubic Hermite functions.

**Model**: feagent BeamElement3D of length 2.5 (BeamElement3D.shape_functions, uy row: columns uy_i, rz_i, uy_j, rz_j).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |N_book - N_feagent| | book | 0 | 0 | 0.00 % | ✅ |
| max |[1 x x^2 x^3] A^-1 - N_feagent| | book | 0 | 4.4409e-16 | 0.00 % | ✅ |
| w and dw/dx at the nodes = identity matrix | book | 0 | 0 | 0.00 % | ✅ |

**Notes**: Checked: the book formulas, the inversion of the 4x4 coefficient matrix and the nodal conditions on values and slopes.

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-6"></a>

### Example 5.6 - CST triangle from generalized coordinates

📐 · PDF page 78 · plane_stress

From u = a1 + a2 x + a3 y the book obtains N_i = (a_i + b_i x + c_i y)/(2A) with a_i = x_j y_k - x_k y_j, b_i = y_j - y_k, c_i = x_k - x_j.

**Model**: feagent PlaneT3 on an arbitrary triangle; the test points are mapped to Cartesian coordinates with the shape functions.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |N_book - N_feagent| | book | 0 | 3.3307e-16 | 0.00 % | ✅ |
| 2A from the determinant = 2 x feagent area | exact | 16.9 | 16.9 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-7"></a>

### Example 5.7 - Two-node bar in natural coordinates L1, L2

📐 · PDF page 81 · bar

With line coordinates L1 = (x2 - x)/l, L2 = (x - x1)/l (sum 1) the shape functions are N1 = L1 and N2 = L2.

**Model**: feagent 2-node BarElement between x = 2 and x = 7.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |L_i - feagent N_i| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 0 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-8"></a>

### Example 5.8 - Two-node bar with xi in [-1, 1]

📐 · PDF page 81 · bar

From u = a1 + a2 xi with xi = -1 and +1 at the nodes: N1 = (1 - xi)/2, N2 = (1 + xi)/2.

**Model**: feagent 2-node BarElement (natural coordinate s = xi).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |N_book - N_feagent| | book | 0 | 0 | 0.00 % | ✅ |
| printed A^-1 = numpy A^-1 | book | 0 | 0 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-9"></a>

### Example 5.9 - Three-node bar in natural coordinates

📐 · PDF page 82 · bar

From u = a1 + a2 xi + a3 xi^2 with nodes 1, 2, 3 at xi = -1, 1, 0: N1 = xi(xi - 1)/2, N2 = xi(xi + 1)/2, N3 = 1 - xi^2.

**Model**: feagent 3-node BarElement (L3: end nodes, then the mid node, as in the book).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |N_book - N_feagent| | book | 0 | 0 | 0.00 % | ✅ |
| printed A^-1 = numpy A^-1 | book | 0 | 0 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |

**Notes**: The printed 3x3 inverse (1/2)[[0,0,2],[-1,1,0],[1,1,-2]] is also checked.

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-10"></a>

### Example 5.10 - CST in area coordinates

📐 · PDF page 84 · plane_stress

With u = a1 L1 + a2 L2 + a3 L3 and L_i = 1 at node i the book concludes N_i = L_i.

**Model**: feagent PlaneT3; the area coordinates are computed as ratios of sub-triangle areas at the mapped Cartesian point.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |L_i (areas) - feagent N_i| | book | 0 | 2.2204e-16 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 0 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-11"></a>

### Example 5.11 - Six-node LST triangle

📐 · PDF page 85 · plane_stress

With the complete quadratic polynomial in L1, L2, L3 and the matrix A of nodal values the book obtains N_i = L_i(2L_i - 1) at the vertices and 4 L_i L_j at the mid-side nodes.

**Model**: feagent PlaneT6 (nodes 4, 5, 6 on sides 1-2, 2-3, 3-1, as in the book).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |N_book - N_feagent| | book | 0 | 0 | 0.00 % | ✅ |
| printed A^-1 = numpy A^-1 | book | 0 | 0 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |

**Notes**: The printed A^-1 is also compared with the numerical inverse.

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-12"></a>

### Example 5.12 - Four-node rectangle in natural coordinates

📐 · PDF page 88 · plane_stress

From u = a1 + a2 xi + a3 eta + a4 xi eta the book obtains N_i = (1 + xi xi_i)(1 + eta eta_i)/4.

**Model**: feagent PlaneQ4 (counter-clockwise nodes from (-1, -1), as in the book).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |N_book - N_feagent| | book | 0 | 0 | 0.00 % | ✅ |
| max |[1 xi eta xi eta] A^-1 - N_feagent| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |

**Notes**: The inversion of the 4x4 coefficient matrix is also repeated.

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-13"></a>

### Example 5.13 - Eight-node quadratic rectangle from the polynomial

📐 · PDF page 90 · plane_stress

Eight-term polynomial (no xi^3 and eta^3), 8x8 matrix A of nodal values, inversion: the Q8 serendipity functions follow.

**Model**: feagent PlaneQ8 (corners, then side nodes 1-2, 2-3, 3-4, 4-1: same numbering as Fig. 5.14).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| printed matrix A = A from the coordinates | book | 0 | 0 | 0.00 % | ✅ |
| max |[1 xi ... xi eta^2] A^-1 - N_feagent| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |
| max |N_book (closed form) - N_feagent| | book | 0 | 0 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 2.2204e-16 | 0.00 % | ✅ |

**Notes**: The printed matrix A matches the one built from the nodal coordinates; [1 xi eta ...] A^-1 matches the feagent shape functions.

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-14"></a>

### Example 5.14 - Four-node tetrahedron in volume coordinates

📐 · PDF page 92 · solid

With u = a1 L1 + a2 L2 + a3 L3 + a4 L4 the book concludes N_i = L_i.

**Model**: feagent Tet4 on an irregular tetrahedron; volume coordinates computed as ratios of sub-tetrahedron volumes.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |L_i (volumes) - feagent N_i| | book | 0 | 8.3267e-17 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 0 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-15"></a>

### Example 5.15 - Eight-node hexahedron from the trilinear polynomial

📐 · PDF page 93 · solid

Eight-term polynomial 1, xi, eta, zeta, xi eta, eta zeta, zeta xi, xi eta zeta; 8x8 matrix A and inversion: N_i = (1 + xi xi_i)(1 + eta eta_i)(1 + zeta zeta_i)/8.

**Model**: feagent Hex8; the book nodes (Fig. 5.16, different numbering) are matched by natural coordinates.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| printed matrix A = A from the coordinates | book | 0 | 0 | 0.00 % | ✅ |
| max |[1 xi ... xi eta zeta] A^-1 - N_feagent| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |
| max |N_book - N_feagent| | book | 0 | 0 | 0.00 % | ✅ |

**Notes**: The printed matrix A matches the one from the coordinates of Fig. 5.16.

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-16"></a>

### Example 5.16 - Lagrange functions for 2, 3 and 5-node bars

📐 · PDF page 95 · bar

Lagrange formula N_k = prod_{j != k}(x - x_j)/(x_k - x_j) for 2, 3 and 5 equally spaced nodes (nodes 1 and 2 at the ends, then the interior ones).

**Model**: feagent 2 and 3-node BarElement on a bar between x = 1 and x = 5; the 5-node bar does not exist in feagent and is checked with numpy.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| 2 nodes: max |N_Lagrange - N_feagent| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |
| 3 nodes: max |N_Lagrange - N_feagent| | book | 0 | 7.2164e-16 | 0.00 % | ✅ |
| 5 nodes (numpy): Kronecker delta | book | 0 | 0 | 0.00 % | ✅ |
| 5 nodes (numpy): sum N_i = 1 | book | 0 | 2.2204e-16 | 0.00 % | ✅ |
| 5 nodes (numpy): sum N_i x_i^4 = x^4 | book | 0 | 2.8422e-14 | 0.00 % | ✅ |

**Notes**: 5-node bar: feagent has no such element; Kronecker delta, partition of unity and exact reproduction of x^4 (fourth-degree completeness) are checked with numpy.

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-17"></a>

### Example 5.17 - Nine-node Lagrangian rectangle

📐 · PDF page 98 · plane_stress

Product of quadratic Lagrange functions in xi and eta: the book writes the nine functions and the compact forms (5.41) for the corners, xi eta (xi + xi_i)(eta + eta_i)/4, and for the side nodes.

**Model**: feagent PlaneQ9; the book nodes (Fig. 5.19, numbered row by row) are matched by natural coordinates.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |N_book - N_feagent| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |
| max |L_i(xi) L_j(eta) - N_feagent| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |
| corners, form (5.41) | book | 0 | 1.1102e-16 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 2.2204e-16 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-18"></a>

### Example 5.18 - Eight-node hexahedron with Lagrange functions

📐 · PDF page 101 · solid

Product of linear Lagrange functions in the three directions: N_i = (1 + xi xi_i)(1 + eta eta_i)(1 + zeta zeta_i)/8 with the book's node numbering.

**Model**: feagent Hex8 with the book nodes matched by natural coordinates.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |L(xi) L(eta) L(zeta) - N_feagent| | book | 0 | 0 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 1.1102e-16 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-19"></a>

### Example 5.19 - Four-node rectangle by the serendipity approach

📐 · PDF page 103 · plane_stress

N_1 is built as the product of the lines through the nodes where it must vanish, C(1 - xi)(1 - eta), with C = 1/4 from N_1 = 1 at node 1.

**Model**: feagent PlaneQ4.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| constant C of node 1 | book | 0.25 | 0.25 | 0.00 % | ✅ |
| max |N_book - N_feagent| | book | 0 | 0 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-20"></a>

### Example 5.20 - Eight-node quadratic serendipity rectangle

📐 · PDF page 104 · plane_stress

With the vanishing-lines approach (corners: (1 - xi)(1 - eta)(-xi - eta - 1)/4; sides: (1 - xi^2)(1 + eta eta_i)/2 and the like) the functions of Ex. 5.13 are recovered.

**Model**: feagent PlaneQ8 on a distorted quadrilateral (the mapping does not affect the shape functions in natural coordinates).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |N_book - N_feagent| | book | 0 | 0 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 2.2204e-16 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-21"></a>

### Example 5.21 - Twelve-node cubic serendipity rectangle

📐 · PDF page 106 · plane_stress

Corners: (1 + xi xi_i)(1 + eta eta_i)[9(xi^2 + eta^2) - 10]/32, where the circle xi^2 + eta^2 = 10/9 passes through the eight side nodes; side nodes at xi_i = +-1/3: 9(1 - xi^2)(1 + 9 xi xi_i)(1 + eta eta_i)/32.

**Model**: No feagent element (the Q12 does not exist): numpy check of the printed functions.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |N_i(node j) - delta_ij| (numpy) | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| (numpy) | book | 0 | 3.3307e-16 | 0.00 % | ✅ |
| max error on the 12 serendipity monomials (numpy) | book | 0 | 3.3307e-16 | 0.00 % | ✅ |
| xi^2 + eta^2 of side nodes = 10/9 (text: 10/3) | corrected | 1.1111 | 1.1111 | 0.00 % | ✅ |

**Notes**: feagent has no Q12: the twelve book functions are checked with numpy (Kronecker delta on the 12 nodes, partition of unity, exact reproduction of the 12 monomials of the cubic serendipity basis 1, xi, eta, xi^2, xi eta, eta^2, xi^3, xi^2 eta, xi eta^2, eta^3, xi^3 eta, xi eta^3). The book writes the circle as xi^2 + eta^2 = 10/3 (the squared radius is 10/9): harmless misprint, the final formula uses 10/9.

*Code*: `validation/books/bhavikatti/ch05.py`

<a id="bhav-ex-5-22"></a>

### Example 5.22 - Seven-node element by degrading the Q8

📐 · PDF page 113 · plane_stress

Removing node 8 (side 4-1) with u8 = (u1 + u4)/2 gives N'_1 = N1 + N8/2 = -xi(1 - xi)(1 - eta)/4, N'_4 = N4 + N8/2 = -xi(1 - xi)(1 + eta)/4, the others unchanged.

**Model**: feagent PlaneQ8 shape functions combined with numpy (the transition Q7 does not exist in feagent).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| N'_1: book - (degraded feagent Q8) | book | 0 | 5.5511e-17 | 0.00 % | ✅ |
| N'_4: book - (degraded feagent Q8) | book | 0 | 1.1102e-16 | 0.00 % | ✅ |
| side 4-1: N'_1, N'_4 linear | book | 0 | 2.7756e-17 | 0.00 % | ✅ |
| max |N_i(node j) - delta_ij| | book | 0 | 0 | 0.00 % | ✅ |
| max |sum N_i - 1| | book | 0 | 2.2204e-16 | 0.00 % | ✅ |

**Notes**: feagent has no 7-node transition element: the degraded functions are built from the feagent Q8 ones and checked with numpy (book closed form, Kronecker delta on the 7 nodes, partition of unity, linear variation along side 4-1).

*Code*: `validation/books/bhavikatti/ch05.py`

## Chapter 8

<a id="bhav-ex-8-1"></a>

### Example 8.1 - Three concurrent bars by virtual work

✅ · PDF page 137 · truss

Three bars of equal EA meet at D = (2, 0) m from three wall hinges, inclined at 30, 0 and 60 degrees; 20 kN vertical load at D. The book writes the virtual work equations for du = 1 and dv = 1 and obtains displacements and forces.

**Model**: Three trusses (add_truss) from the wall nodes A, B, C to joint D, EA = 1 (results in units of 1/EA); A, B, C fixed, D free in the X-Y plane.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u_D x EA (book: 5.898) [kN] | corrected | -6.188 | -6.188 | 0.00 % | ✅ |
| v_D x EA (book: -66.048) [kN] | corrected | -69.282 | -69.282 | 0.00 % | ✅ |
| F1 (book: 26.045) [kN] | corrected | 12.679 | 12.679 | 0.00 % | ✅ |
| F2 (book: 2.949) [kN] | corrected | -3.094 | -3.094 | 0.00 % | ✅ |
| F3 (book: -13.562) [kN] | corrected | -15.774 | -15.774 | 0.00 % | ✅ |
| sum of vertical reactions = 20 kN [kN] | exact | 20 | 20 | 0.00 % | ✅ |

**Notes**: The book makes a sign error in the virtual displacement equation du = 1 (it writes 0.88726u - 0.07924v instead of 0.88726u + 0.07924v) and prints u = 5.898/EA, v = 66.048/EA (downwards), F1 = 26.045, F2 = 2.949, F3 = -13.562 kN: these forces do not balance joint D (vertical resultant 24.8 kN against 20 kN). feagent is compared with the correct solution u = -6.188/EA, v = -69.282/EA, F1 = 12.679, F2 = -3.094, F3 = -15.774 kN (source = corrected), which satisfies equilibrium.

*Code*: `validation/books/bhavikatti/ch08.py`

## Chapter 9

<a id="bhav-ex-9-1"></a>

### Example 9.1 - Beam equation from the stationarity of the potential energy

📐 · PDF page 144 · beam

Setting the first variation of the potential energy to zero the book derives the equilibrium equation EI w'''' = q and the natural boundary conditions (zero moment at the supports, zero moment and shear at the free end of the cantilever).

**Model**: feagent Hermite beams (4 elements) for the two cases of the book: simply supported beam and cantilever under uniform load; comparison with the exact solution of EI w'''' = q with the boundary conditions derived.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| simply supported: midspan deflection | exact | 0.0084375 | 0.0084375 | 0.00 % | ✅ |
| simply supported: midspan moment | exact | 45 | 45 | 0.00 % | ✅ |
| cantilever: tip deflection | exact | 0.081 | 0.081 | 0.00 % | ✅ |
| cantilever: fixed-end moment | exact | 180 | 180 | 0.00 % | ✅ |
| cantilever: shear at free end | exact | 0 | 0 | 0.00 % | ✅ |

**Notes**: Symbolic example only: the solutions of the derived boundary-value problem (midspan deflection 5ql^4/384EI and moment ql^2/8; cantilever: tip deflection ql^4/8EI, fixed-end moment ql^2/2, zero tip shear) are checked against the feagent beams (l = 6, q = 10, EI = 2e4).

*Code*: `validation/books/bhavikatti/ch09.py`

<a id="bhav-ex-9-2"></a>

### Example 9.2 - Three springs in series: direct and variational assembly

📐 · PDF page 150 · spring

The book writes the stiffness matrix of the system wall - k1 - 1 - k2 - 2 - k3 - 3 both by direct assembly and by minimizing the potential energy: [[k1+k2, -k2, 0], [-k2, k2+k3, -k3], [0, -k3, k3]].

**Model**: Three axial springs (add_spring) in series between the wall and nodes 1, 2, 3 along X; k1 = 100, k2 = 200, k3 = 300.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| max |K feagent - K book| | book | 0 | 0 | 0.00 % | ✅ |
| u1 = K^-1 F (book matrix) | exact | 0.6 | 0.6 | 0.00 % | ✅ |
| u2 = K^-1 F (book matrix) | exact | 0.85 | 0.85 | 0.00 % | ✅ |
| u3 = K^-1 F (book matrix) | exact | 0.95 | 0.95 | 0.00 % | ✅ |

**Notes**: Symbolic example, redone with arbitrary numerical values; the solution for F = (10, 20, 30) is also compared.

*Code*: `validation/books/bhavikatti/ch09.py`

<a id="bhav-ex-9-3"></a>

### Example 9.3 - Spring system by minimum potential energy

✅ · PDF page 152 · spring

Two parallel springs connect the wall to node 1, a third spring connects nodes 1 and 2; the displacements are found with the minimum potential energy principle.

**Model**: Axial springs: k1 = 60 and k2 = 75 N/m in parallel between wall and node 1, k3 = 100 N/m between node 1 and node 2; nodal forces 100 and 80 N.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u1 [m] | book | 1.333 | 1.3333 | 0.03 % | ✅ |
| u2 [m] | book | 2.133 | 2.1333 | 0.02 % | ✅ |

*Code*: `validation/books/bhavikatti/ch09.py`

<a id="bhav-ex-9-4"></a>

### Example 9.4 - Three concurrent bars by minimum potential energy

✅ · PDF page 153 · truss

The truss of Ex. 8.1 is solved by minimizing the total potential energy with respect to the displacements u, v of joint D.

**Model**: Same model as Ex. 8.1: three trusses with EA = 1 meeting at D.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u_D x EA (book: 5.898) [kN] | corrected | -6.188 | -6.188 | 0.00 % | ✅ |
| v_D x EA (book: -66.048) [kN] | corrected | -69.282 | -69.282 | 0.00 % | ✅ |
| F1 (book: 26.045) [kN] | corrected | 12.679 | 12.679 | 0.00 % | ✅ |
| F2 (book: 2.949) [kN] | corrected | -3.094 | -3.094 | 0.00 % | ✅ |
| F3 (book: -13.562) [kN] | corrected | -15.774 | -15.774 | 0.00 % | ✅ |
| sum of vertical reactions = 20 kN [kN] | exact | 20 | 20 | 0.00 % | ✅ |

**Notes**: The book repeats the sign error of Ex. 8.1 (it writes u = 0.0893 v instead of u = -0.0893 v) and reports the same results. The book makes a sign error in the virtual displacement equation du = 1 (it writes 0.88726u - 0.07924v instead of 0.88726u + 0.07924v) and prints u = 5.898/EA, v = 66.048/EA (downwards), F1 = 26.045, F2 = 2.949, F3 = -13.562 kN: these forces do not balance joint D (vertical resultant 24.8 kN against 20 kN). feagent is compared with the correct solution u = -6.188/EA, v = -69.282/EA, F1 = 12.679, F2 = -3.094, F3 = -15.774 kN (source = corrected), which satisfies equilibrium.

*Code*: `validation/books/bhavikatti/ch09.py`

<a id="bhav-ex-9-5"></a>

### Example 9.5 - Rayleigh-Ritz with a sine series on a simply supported beam

🟡 · PDF page 154 · beam

Simply supported beam under uniform load approximated by y = a1 sin(pi x/l) + a2 sin(3 pi x/l): the book finds a1 = 4wl^4/(EI pi^5), a2 = 4wl^4/(243 EI pi^5), maximum deflection wl^4/(76.82 EI) and midspan moment wl^2/8.05.

**Model**: Ritz functional with two terms sin(pi x/l), sin(3 pi x/l) integrated with numpy (20-point Gauss); feagent beam of 4 Hermite elements for the exact solution. l = 6, w = 10, EI = 2e4.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| a1 = 4wl^4/(EI pi^5) (Ritz, numpy) | book | 0.00847 | 0.00847 | 0.00 % | ✅ |
| a2 = 4wl^4/(243 EI pi^5) (Ritz, numpy) | book | 3.4856e-05 | 3.4856e-05 | 0.00 % | ✅ |
| Ritz maximum deflection = wl^4/(76.82 EI) | book | 0.0084353 | 0.0084352 | 0.00 % | ✅ |
| Ritz midspan moment = wl^2/8.05 | book | 44.72 | 44.722 | 0.00 % | ✅ |
| feagent: exact deflection 5wl^4/384EI | exact | 0.0084375 | 0.0084375 | 0.00 % | ✅ |
| feagent: exact moment wl^2/8 | exact | 45 | 45 | 0.00 % | ✅ |

**Notes**: The sine-series Ritz approximation is not a feagent element: the Ritz system is solved with numpy (pure algebra) and compared with the book coefficients; the feagent Hermite beams give instead the exact solution 5wl^4/384EI = wl^4/76.8 EI and wl^2/8, from which the Ritz series deviates by 0.03 % and 0.6 %.

*Code*: `validation/books/bhavikatti/ch09.py`

<a id="bhav-ex-9-6"></a>

### Example 9.6 - Rayleigh-Ritz on a fixed-fixed bar with a midspan force

✅ · PDF page 156 · bar

Bar fixed at both ends with an axial force P at midspan; the three-term polynomial with u(0) = u(l) = 0 reduces to u = a x(l - x). The book finds u(l/2) = 3Pl/(16EA) and sigma = 3P/(4A), 0, -3P/(4A) at x = 0, l/2, l (exact: Pl/(4EA), +-P/(2A)).

**Model**: One feagent 3-node bar (L3, mid node at midspan): with fixed ends its field is u = a x(l - x), exactly the book's Ritz field. Plus two L2 bars for the exact solution. l = 2, EA = 1e3, P = 50.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| L3: u(l/2) = 3Pl/(16EA) | book | 0.01875 | 0.01875 | 0.00 % | ✅ |
| L3: sigma(0) = 3P/(4A) | book | 37.5 | 37.5 | 0.00 % | ✅ |
| L3: sigma(l) = -3P/(4A) | book | -37.5 | -37.5 | 0.00 % | ✅ |
| L3: sigma(l/2) = 0 | book | 0 | 0 | 0.00 % | ✅ |
| 2 x L2: u(l/2) = Pl/(4EA) | exact | 0.025 | 0.025 | 0.00 % | ✅ |
| 2 x L2: sigma part 1 = P/(2A) | exact | 25 | 25 | 0.00 % | ✅ |

**Notes**: A single quadratic element reproduces the Ritz approximation exactly; two linear elements give the exact solution.

*Code*: `validation/books/bhavikatti/ch09.py`

<a id="bhav-ex-9-7"></a>

### Example 9.7 - Rayleigh-Ritz on a hanging bar under self-weight

✅ · PDF page 159 · bar

Vertical bar hanging from its top end under its own weight. With two terms the book finds u = rho l x/(2E), elongation rho l^2/(2E) and constant stress rho l/2; with three terms u = rho(2lx - x^2)/(2E) and sigma = rho(l - x), the exact solution.

**Model**: feagent bar hanging at x = 0 under self-weight (add_solid_body_force): one L2 element equals the 2-term Ritz (u = a x), one L3 element the 3-term Ritz (u = a x + b x^2). l = 1000 mm, E = 2e5 N/mm2, unit weight 7.85e-5 N/mm3.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| 2 terms (L2): elongation rho l^2/(2E) [mm] | book | 1.9625e-04 | 1.9625e-04 | 0.00 % | ✅ |
| 2 terms (L2): constant sigma rho l/2 [N/mm2] | book | 0.03925 | 0.03925 | 0.00 % | ✅ |
| 3 terms (L3): u(l/2) = rho(2l x - x^2)/(2E) [mm] | book | 1.4719e-04 | 1.4719e-04 | 0.00 % | ✅ |
| 3 terms (L3): sigma(0) = rho l [N/mm2] | book | 0.0785 | 0.0785 | 0.00 % | ✅ |
| 3 terms (L3): sigma(l/2) = rho l/2 [N/mm2] | book | 0.03925 | 0.03925 | 0.00 % | ✅ |
| 3 terms (L3): sigma(l) = 0 [N/mm2] | book | 0 | -2.1176e-17 | 0.00 % | ✅ |

**Notes**: x axis pointing down, body force +rho along x.

*Code*: `validation/books/bhavikatti/ch09.py`

## Chapter 11

<a id="bhav-ex-11-1"></a>

### Example 11.1 - Hanging stepped plate: self-weight and point load

✅ · PDF page 182 · bar

20 mm thick steel plate hanging from the top, with two parts 125 and 100 mm wide, 250 mm long each, loaded by self-weight and 400 N at mid-height. The book computes displacements, stresses in the two parts and the support reaction.

**Model**: Two feagent L2 bars along X (axis pointing down): A = 2500 and 2000 mm2, l = 250 mm, E = 2e5 N/mm2; self-weight with add_solid_body_force (0.8e-4 N/mm3), 400 N at node 2, node 1 fixed.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| delta2 [mm] | book | 2.3250e-04 | 2.3250e-04 | 0.00 % | ✅ |
| delta3 [mm] | book | 2.4500e-04 | 2.4500e-04 | 0.00 % | ✅ |
| sigma1 [N/mm2] | book | 0.186 | 0.186 | 0.00 % | ✅ |
| sigma2 [N/mm2] | book | 0.01 | 0.01 | 0.00 % | ✅ |
| R1 (upwards) [N] | book | 490 | 490 | 0.00 % | ✅ |

**Notes**: The book gives the unit weight in N/mm2: it is N/mm3. The self-weight becomes a consistent nodal load (25 + 25 N and 20 + 20 N), the same feagent obtains from add_solid_body_force.

*Code*: `validation/books/bhavikatti/ch11.py`

<a id="bhav-ex-11-2"></a>

### Example 11.2 - Tapered bar: stiffness and self-weight loads

📐 · PDF page 184 · bar

With A = N1 A1 + N2 A2 the book derives the stiffness E A_mean/l [[1, -1], [-1, 1]] with A_mean = (A1 + A2)/2 and the self-weight load rho l/6 {2A1 + A2, A1 + 2A2}.

**Model**: feagent L2 bar with linearly varying area (one value per node), data of Ex. 11.3: A1 = 3000, A2 = 1500 mm2, l = 600 mm, E = 2e5, weight 0.8e-4 N/mm3.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| k11 = E A_media/l [N/mm] | book | 7.5e+05 | 7.5e+05 | 0.00 % | ✅ |
| k12 = -E A_media/l [N/mm] | book | -7.5e+05 | -7.5e+05 | 0.00 % | ✅ |
| F1 = rho l (2A1 + A2)/6 [N] | book | 60 | 60 | 0.00 % | ✅ |
| F2 = rho l (A1 + 2A2)/6 [N] | book | 48 | 48 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch11.py`

<a id="bhav-ex-11-3"></a>

### Example 11.3 - Tapered bar with self-weight and tip force

✅ · PDF page 187 · bar

20 mm thick trapezoidal plate, 150 mm wide at the support and 75 mm at the free end, 600 mm long, with self-weight and 400 N at the tip: with one element the book finds the consistent loads {60, 48} N and the elongation 5.973e-4 mm.

**Model**: One tapered feagent L2 bar (A1 = 3000, A2 = 1500 mm2), as in the book; plus 64 bars for the exact solution.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| carico consistente nodo 1 [N] | book | 60 | 60 | 0.00 % | ✅ |
| carico consistente nodo 2 [N] | book | 48 | 48 | 0.00 % | ✅ |
| elongation, 1 element [mm] | book | 5.9730e-04 | 5.9733e-04 | 0.01 % | ✅ |
| elongation, 64 elements [mm] | exact | 6.1261e-04 | 6.1261e-04 | 0.00 % | ✅ |

**Notes**: The exact solution (integral of N(x)/(E A(x))) is 6.016e-4 mm: the single element is 0.7 % too stiff, which the book does not discuss.

*Code*: `validation/books/bhavikatti/ch11.py`

<a id="bhav-ex-11-4-bar-thermal"></a>

### Example 11.4 (bar) - Fixed-fixed aluminium-steel bar with force and temperature change

✅ · PDF page 191 · bar

Bar made of an aluminium part and a steel part, fixed at both ends, with an axial force of 400 kN at the middle node and a temperature rise of 30 degrees: middle node displacement, stresses and reactions.

**Model**: Two feagent L2 bars: aluminium (A = 2400 mm2, l = 300 mm, E = 0.7e5, alpha = 22e-6) and steel (A = 1200 mm2, l = 400 mm, E = 2e5, alpha = 12e-6); add_solid_thermal +30 degrees on both, 400 kN at node 2, ends fixed.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| delta2 [mm] | book | 0.36593 | 0.36593 | 0.00 % | ✅ |
| sigma1 [N/mm2] | book | 39.18 | 39.184 | 0.01 % | ✅ |
| sigma2 (book: 50.965) [N/mm2] | corrected | -254.97 | -254.97 | 0.00 % | ✅ |
| R1 [N] | book | -94041 | -94041 | 0.00 % | ✅ |
| R3 [N] | book | -3.0596e+05 | -3.0596e+05 | 0.00 % | ✅ |

**Notes**: The book has two examples numbered 11.4. Here the printed steel stress, 50.965 N/mm2, is wrong: sigma2 = 2e5 (0 - 0.36593)/400 - 2e5 x 12e-6 x 30 = -254.97 N/mm2, consistent with the book's own reaction R3 = -305959 N (source = corrected). In the computation of R1 the book writes 110080 instead of 110880, but the result is right.

*Code*: `validation/books/bhavikatti/ch11.py`

<a id="bhav-ex-11-4-truss3"></a>

### Example 11.4 (truss) - Three-bar triangular truss with apex load

✅ · PDF page 198 · truss

Triangle with an 800 mm base and apex at (400, 400) mm, 1500 mm2 tie and 2000 mm2 struts: nodal displacements, member forces and reactions.

**Model**: Three feagent trusses (add_truss) in the X-Y plane, E = 200 kN/mm2; node 1 pinned, node 2 on rollers, 150 kN downwards at node 3.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2 [mm] | book | 0.2 | 0.2 | 0.00 % | ✅ |
| u3 [mm] | book | 0.1 | 0.1 | 0.00 % | ✅ |
| v3 [mm] | book | -0.312 | -0.31213 | 0.04 % | ✅ |
| P1 tie (book: -75) [kN] | corrected | 75 | 75 | 0.00 % | ✅ |
| P2 strut (book: +106) [kN] | corrected | -106.07 | -106.07 | 0.00 % | ✅ |
| P3 strut (book: +106) [kN] | corrected | -106.07 | -106.07 | 0.00 % | ✅ |
| R1y [kN] | book | 75 | 75 | 0.00 % | ✅ |
| R2y [kN] | book | 75 | 75 | 0.00 % | ✅ |
| R1x [kN] | book | 0 | -2.8422e-14 | 0.00 % | ✅ |

**Notes**: The book's displacements are confirmed, but the force signs are reversed: the tie is in tension (+75 kN, the book prints -75) and the two struts in compression (-106.07 kN, the book prints +106). The correct values are compared (source = corrected).

*Code*: `validation/books/bhavikatti/ch11.py`

<a id="bhav-ex-11-5-settlement"></a>

### Example 11.5 (settlement) - Truss of Ex. 11.4 with a support settlement

✅ · PDF page 202 · truss

The same truss with support 2 settling 0.1 mm downwards.

**Model**: Model of Ex. 11.4 (truss) with add_settlement of -0.1 mm on the uy DOF of node 2.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2 (book: 0.18056) [mm] | corrected | 0.2 | 0.2 | 0.00 % | ✅ |
| u3 = u3 without settlement + rigid motion (book: 0.04028) [mm] | corrected | 0.15 | 0.15 | 0.00 % | ✅ |
| v3 (book: -0.2318) [mm] | corrected | -0.36213 | -0.36213 | 0.00 % | ✅ |
| v3 = -0.3621 mm [mm] | corrected | -0.3621 | -0.36213 | 0.01 % | ✅ |
| P1 (book: 67.71) [kN] | corrected | 75 | 75 | 0.00 % | ✅ |
| P2 (book: 4.24) [kN] | corrected | -106.07 | -106.07 | 0.00 % | ✅ |
| P3 (book: -95.76) [kN] | corrected | -106.07 | -106.07 | 0.00 % | ✅ |

**Notes**: BOOK RESULTS WRONG. The truss is statically determinate, so the settlement only produces a rigid motion (rotation of -0.1/800 about node 1) and leaves the forces unchanged. The book inserts the settlement column incorrectly in the reduced system and prints u2 = 0.18056, u3 = 0.04028, v3 = -0.2318 mm and forces 67.71, 4.24, -95.76 kN. Correct values: u2 = 0.2, u3 = 0.15, v3 = -0.3621 mm, forces unchanged 75, -106.07, -106.07 kN (source = corrected).

*Code*: `validation/books/bhavikatti/ch11.py`

<a id="bhav-ex-11-5-frame6"></a>

### Example 11.5 (braced frame) - Braced rectangular truss: temperature and lack of fit

✅ · PDF page 206 · truss

4000 x 3000 mm panel with both diagonals, internally indeterminate: the book assembles the 8x8 stiffness matrix, the load vectors for heating the diagonal 1-3 and for the same diagonal being 0.2 mm too long, and stops at the boundary conditions.

**Model**: Six feagent L2 bars (add_bar) in the X-Y plane: sides 1000 mm2, diagonals 2000 mm2, E = 200 kN/mm2; node 1 pinned, node 2 on rollers; case (ii) add_solid_thermal +25 degrees on bar 1-3 (alpha = 12e-6); case (iii) lack of fit of +0.2 mm as an equivalent imposed strain (alpha dT = 0.2/5000).

![bhav-ex-11.5-frame6](images/books/bhav-ex-11.5-frame6.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| K(1,1) [kN/mm] | book | 101.2 | 101.2 | 0.00 % | ✅ |
| K(2,2) [kN/mm] | book | 95.47 | 95.467 | 0.00 % | ✅ |
| K(1,2) [kN/mm] | book | 38.4 | 38.4 | 0.00 % | ✅ |
| K(1,5) [kN/mm] | book | -51.2 | -51.2 | 0.00 % | ✅ |
| K(2,8) [kN/mm] | book | -66.67 | -66.667 | 0.00 % | ✅ |
| K(7,3) (book: -51.3) [kN/mm] | corrected | -51.2 | -51.2 | 0.00 % | ✅ |
| thermal load DOF 1 [kN] | book | -96 | -96 | 0.00 % | ✅ |
| thermal load DOF 2 [kN] | book | -72 | -72 | 0.00 % | ✅ |
| thermal load DOF 5 [kN] | book | 96 | 96 | 0.00 % | ✅ |
| thermal load DOF 6 [kN] | book | 72 | 72 | 0.00 % | ✅ |
| lack of fit DOF 1 (book: -128) [kN] | corrected | -12.8 | -12.8 | 0.00 % | ✅ |
| lack of fit DOF 2 (book: -96) [kN] | corrected | -9.6 | -9.6 | 0.00 % | ✅ |
| lack of fit DOF 5 (book: 128) [kN] | corrected | 12.8 | 12.8 | 0.00 % | ✅ |
| lack of fit DOF 6 (book: 96) [kN] | corrected | 9.6 | 9.6 | 0.00 % | ✅ |
| temperature: force in diagonal 1-3 [kN] | exact | -24.43 | -24.43 | 0.00 % | ✅ |
| temperature: force in side 1-2 [kN] | exact | 19.544 | 19.544 | 0.00 % | ✅ |
| temperature: zero reactions [kN] | exact | 0 | 4.6185e-14 | 0.00 % | ✅ |
| lack of fit: force in diagonal 1-3 [kN] | exact | -3.2573 | -3.2573 | 0.00 % | ✅ |
| lack of fit: force in side 1-2 [kN] | exact | 2.6059 | 2.6059 | 0.00 % | ✅ |
| lack of fit: zero reactions [kN] | exact | 0 | 3.5527e-15 | 0.00 % | ✅ |

**Notes**: Matrix and thermal load match the book (51.3 instead of 51.2 appears in the printed matrix, a misprint). The book's lack-of-fit load, {-128, -96, 128, 96} kN, is 10 times too large: the book uses l = 500 mm instead of 5000 mm; the right value is EA dl/l = 16 kN, i.e. {-12.8, -9.6, 12.8, 9.6} kN (source = corrected). The book does not solve the system: feagent completes it and the force in diagonal 1-3 is compared with the force method, X = -e0 l5 / sum(f_i^2 l_i/(E A_i)), with f = -0.8 on the horizontal sides, -0.6 on the vertical ones and 1 on the diagonals (source = exact).

*Code*: `validation/books/bhavikatti/ch11.py`

<a id="bhav-ex-11-6"></a>

### Example 11.6 - Space tripod under a vertical load

✅ · PDF page 212 · truss

Tripod with feet at (-3000, 0, 0), (2000, 0, 2000), (2000, 0, -2000) mm and apex at (0, 5000, 0) mm: apex displacements and leg forces.

**Model**: Three feagent 3D trusses (add_truss) from the ground nodes 1, 2, 3 to node 4, E = 200 kN/mm2, A = 2000 mm2; vertical Y axis as in the book, 10 kN along -Y at node 4.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u4 (book: -0.02273) [mm] | corrected | -0.02243 | -0.02243 | 0.00 % | ✅ |
| v4 [mm] | book | -0.06597 | -0.065843 | 0.19 % | ✅ |
| w4 [mm] | book | 0 | 0 | 0.00 % | ✅ |
| F1 [kN] | book | -4.68 | -4.6648 | 0.33 % | ✅ |
| F2 [kN] | book | -3.445 | -3.4467 | 0.05 % | ✅ |
| F3 [kN] | book | -3.445 | -3.4467 | 0.05 % | ✅ |

**Notes**: The book rounds the direction cosines and prints u4 = -0.02273 mm, v4 = -0.06597 mm, F = {-4.680, -3.445, -3.445} kN. The recalculation without rounding gives u4 = -0.02243, v4 = -0.06584 mm, F = {-4.665, -3.447, -3.447} kN: v4 and the forces match the book within 0.3 %, whereas u4, being small, is affected by the rounding by 1.3 % and is compared with the recalculated value (source = corrected).

*Code*: `validation/books/bhavikatti/ch11.py`

<a id="bhav-prob-11-7"></a>

### Chapter 11 Question 7 - Fixed-fixed stepped bar with an intermediate force

✅ · PDF page 215 · bar

Exercise with answer: two-section bar fixed at both ends with a 30 kN axial force 150 mm from the left end; displacements, stresses and reactions.

**Model**: Three feagent L2 bars: 150 mm and 150 mm with A = 250 mm2, 300 mm with A = 400 mm2, E = 200 kN/mm2; 30 kN at node 2, nodes 1 and 4 fixed.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| Delta2 [mm] | book | 0.062307 | 0.062308 | 0.00 % | ✅ |
| Delta3 [mm] | book | 0.034615 | 0.034615 | 0.00 % | ✅ |
| sigma1 (tension) [N/mm2] | book | 83.08 | 83.077 | 0.00 % | ✅ |
| |sigma2| (compression) [N/mm2] | book | 36.92 | 36.923 | 0.01 % | ✅ |
| |sigma3| (compression) [N/mm2] | book | 23.08 | 23.077 | 0.01 % | ✅ |
| R1 [kN] | book | -20.77 | -20.769 | 0.00 % | ✅ |
| R4 [kN] | book | -9.23 | -9.2308 | 0.01 % | ✅ |

**Notes**: The book gives the stresses as magnitudes: sigma2 and sigma3 are compressive. Magnitudes are compared.

*Code*: `validation/books/bhavikatti/ch11.py`

<a id="bhav-prob-11-8"></a>

### Chapter 11 Question 8 - Right-angled triangular truss with two forces

✅ · PDF page 215 · truss

Exercise with answer: triangle with nodes (0, 0), (4000, 0), (4000, 3000) mm; member forces and reactions.

**Model**: Three feagent trusses in the X-Y plane, E = 200 kN/mm2, A = 2000 mm2; node 1 pinned, node 2 on rollers; at node 3 20 kN downwards and 15 kN to the right.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| F1 [kN] | book | 0 | 0 | 0.00 % | ✅ |
| F2 (book: -37.25) [kN] | corrected | -31.25 | -31.25 | 0.00 % | ✅ |
| F3 [kN] | book | 18.75 | 18.75 | 0.00 % | ✅ |
| reazione verticale in 1 (R1) [kN] | book | -11.25 | -11.25 | 0.00 % | ✅ |
| reazione orizzontale in 1 (R2) [kN] | book | -15 | -15 | 0.00 % | ✅ |
| reazione verticale in 2 (R4) [kN] | book | 31.25 | 31.25 | 0.00 % | ✅ |

**Notes**: The printed force in the vertical member 2-3, -37.25 kN, is a misprint: equilibrium of node 3 gives -31.25 kN (source = corrected). The book labels the reactions with inconsistent DOF numbers: -15 kN is the horizontal reaction at 1 (printed as R2), -11.25 kN the vertical one at 1 (printed as R1), 31.25 kN the vertical one at 2.

*Code*: `validation/books/bhavikatti/ch11.py`

<a id="bhav-prob-11-9"></a>

### Chapter 11 Question 9 - Rectangular truss with one diagonal

✅ · PDF page 216 · truss

Exercise with answer: 4 x 3 m panel with the diagonal 1-3 and one redundant support; forces in the five members.

**Model**: Five feagent trusses in the X-Y plane, E = 200 GPa, A = 2000 mm2 (units kN, m); node 1 pinned, node 2 on rollers (v = 0), node 4 on a wall roller (u = 0) as in Fig. 11.22; 30 kN downwards at node 3.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| F1 (book: 20) [kN] | corrected | 0 | 0 | 0.00 % | ✅ |
| F2 [kN] | book | -26.25 | -26.25 | 0.00 % | ✅ |
| F3 (book: 4.961) [kN] | corrected | 5 | 5 | 0.00 % | ✅ |
| F4 [kN] | book | 0 | 0 | 0.00 % | ✅ |
| F5 (book: -6.2752) [kN] | corrected | -6.25 | -6.25 | 0.00 % | ✅ |
| F5 = -6.25 kN [kN] | corrected | -6.25 | -6.25 | 0.00 % | ✅ |

**Notes**: With the supports of the figure, equilibrium of nodes 2 and 4 gives F1 = F4 = 0; the book prints F1 = 20 kN, which is incompatible (source = corrected). Node 3 gives F2 = -30 - 0.6 F5 and F3 = -0.8 F5; the stiffness solution yields F5 = -6.25, F3 = 5.0, F2 = -26.25 kN. The book prints F2 = -26.25 (confirmed), F3 = 4.961 and F5 = -6.2752, which violate equilibrium of node 3 (0.06 kN residual): the correct values, also obtained with the force method on the redundant support, are compared (source = corrected).

*Code*: `validation/books/bhavikatti/ch11.py`

## Chapter 12

<a id="bhav-ex-12-1"></a>

### Example 12.1 - Rectangular panel with two CSTs and a horizontal force

✅ · PDF page 220 · plane_stress, plane_strain

Short 750 x 500 mm cantilever restrained on the left side and propped at the bottom right, divided into two constant-strain triangles, with 50 kN horizontal at the top right corner: nodal displacements and element stresses.

**Model**: Two feagent PlaneT3 (elements 1-3-4 and 1-2-3), E = 2e5 N/mm2, nu = 0.25, t = 15 mm, in plane stress as stated in the text and in plane strain as in the book's calculation; nodes 1 and 4 pinned, node 2 on rollers, 50 kN horizontal at node 3.

![bhav-ex-12.1](images/books/bhav-ex-12.1.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u2, plane stress [mm] | corrected | 0.01528 | 0.015278 | 0.01 % | ✅ |
| u3, plane stress [mm] | corrected | 0.03242 | 0.032415 | 0.01 % | ✅ |
| v3, plane stress [mm] | corrected | -0.00218 | -0.0021826 | 0.12 % | ✅ |
| u2, plane strain [mm] | corrected | 0.01341 | 0.013408 | 0.01 % | ✅ |
| u3, plane strain [mm] | corrected | 0.02956 | 0.029556 | 0.01 % | ✅ |
| v3, plane strain [mm] | corrected | -0.0026 | -0.0025952 | 0.19 % | ✅ |
| sxx element 1, plane strain [N/mm2] | corrected | 9.458 | 9.4579 | 0.00 % | ✅ |
| syy element 1, plane strain [N/mm2] | corrected | 3.153 | 3.1526 | 0.01 % | ✅ |
| txy element 1, plane strain [N/mm2] | corrected | -0.277 | -0.27682 | 0.07 % | ✅ |
| sxx element 2, plane strain [N/mm2] | corrected | 3.875 | 3.8754 | 0.01 % | ✅ |
| syy element 2, plane strain [N/mm2] | corrected | 0.185 | 0.18454 | 0.25 % | ✅ |
| txy element 2, plane strain [N/mm2] | corrected | 2.584 | 2.5836 | 0.01 % | ✅ |
| printed u2 / feagent plane strain | corrected | 4 | 4.0021 | 0.05 % | ✅ |
| printed u3 / feagent plane strain | corrected | 4 | 4.0004 | 0.01 % | ✅ |
| horizontal reactions = -50 kN [N] | exact | -50000 | -50000 | 0.00 % | ✅ |

**Notes**: The text states plane stress but the book uses the plane-strain D matrix, with a wrong factor on top: it writes 0.2e5 [[3, 1, 0], [1, 3, 0], [0, 0, 1]] instead of 0.8e5 [...]. The printed displacements, u2 = 0.053661, u3 = 0.118236, v3 = -0.010459 mm, are therefore about 4 times the plane-strain ones (ratio 4.00 on u2 and u3; 4.03 on v3, because of the rounding of the hand calculation) and the printed stresses, {-5.584, -2.977, 5.000} and {9.877, 4.408, 5.008} N/mm2, cannot be reproduced. feagent in plane stress is compared with the independent recalculation u2 = 0.01528, u3 = 0.03242, v3 = -0.00218 mm and feagent in plane strain with u2 = 0.01341, u3 = 0.02956, v3 = -0.00260 mm and stresses {9.458, 3.153, -0.277}, {3.875, 0.185, 2.584} N/mm2 (source = corrected).

*Code*: `validation/books/bhavikatti/ch12.py`

<a id="bhav-ex-12-2"></a>

### Example 12.2 - Consistent self-weight load on a CST

📐 · PDF page 226 · plane_stress

With N = L and the area-coordinate integral formula the book finds that the self-weight of a CST is shared equally by the three nodes: -rho h A/3 on each v_i and nothing on the u_i.

**Model**: feagent PlaneT3 (t = 12) on an arbitrary triangle; body force -rho along Y (equivalent_body_force), rho = 7.85e-5.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| F v1 = -rho h A/3 [N] | book | -9.7497 | -9.7497 | 0.00 % | ✅ |
| F v2 = -rho h A/3 [N] | book | -9.7497 | -9.7497 | 0.00 % | ✅ |
| F v3 = -rho h A/3 [N] | book | -9.7497 | -9.7497 | 0.00 % | ✅ |
| F u_i = 0 [N] | book | 0 | 0 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch12.py`

<a id="bhav-ex-12-3"></a>

### Example 12.3 - Consistent loads of side pressures on a CST

📐 · PDF page 227 · plane_stress

Integrating N^T over the sides the book finds that each uniform load on a side of length l_i is shared equally, t l_i p/2, by the two nodes of the side.

**Model**: feagent PlaneT3 (t = 12); uniform tractions (px, py) on the three sides with equivalent_face_load; book side i = side opposite node i.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| u1: (t/2)(l_j p_j + l_k p_k) [N] | corrected | 3049.1 | 3049.1 | 0.00 % | ✅ |
| v1: (t/2)(l_j p_j + l_k p_k) [N] | corrected | 10498 | 10498 | 0.00 % | ✅ |
| u2: (t/2)(l_j p_j + l_k p_k) [N] | corrected | 11516 | 11516 | 0.00 % | ✅ |
| v2: (t/2)(l_j p_j + l_k p_k) [N] | corrected | -1198.1 | -1198.1 | 0.00 % | ✅ |
| u3: (t/2)(l_j p_j + l_k p_k) [N] | corrected | 2416.4 | 2416.4 | 0.00 % | ✅ |
| v3: (t/2)(l_j p_j + l_k p_k) [N] | corrected | 4439.8 | 4439.8 | 0.00 % | ✅ |

**Notes**: The book writes the side-1 term correctly, t l1/2 {0, 0, px1, py1, px1, py1}, but drops the factor t/2 for sides 2 and 3 and in the final vector: the correct vector is (t/2) {l2 px2 + l3 px3, ...}, the one feagent is compared with (source = corrected).

*Code*: `validation/books/bhavikatti/ch12.py`

## Chapter 13

<a id="bhav-ex-13-1"></a>

### Example 13.1 - Jacobian and B matrix of a parallelogram at the Gauss point

📐 · PDF page 245 · plane_stress

At the Gauss point (1/sqrt3, 1/sqrt3) the book computes the natural derivatives of the shape functions, the Jacobian matrix, its inverse and the 3x8 B matrix.

**Model**: feagent PlaneQ4 with nodes (0, 0), (60, 0), (65.7735, 10), (5.7735, 10) mm; PlaneQ4.jacobian, dshape and B at the point (0.57735, 0.57735).

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| J11 | book | 30 | 30 | 0.00 % | ✅ |
| J12 | book | 0 | 0 | 0.00 % | ✅ |
| J21 | book | 2.887 | 2.8867 | 0.01 % | ✅ |
| J22 | book | 5 | 5 | 0.00 % | ✅ |
| dN1/dxi | book | -0.10566 | -0.10566 | 0.00 % | ✅ |
| dN3/dxi | book | 0.39438 | 0.39434 | 0.01 % | ✅ |
| dN1/deta | book | -0.10566 | -0.10566 | 0.00 % | ✅ |
| dN2/deta | book | -0.39438 | -0.39434 | 0.01 % | ✅ |
| J^-1(1,1) (book: 0.033333) | corrected | 0.033333 | 0.033333 | 0.00 % | ✅ |
| J^-1(1,2) (book: -0.019246) | corrected | 0 | 0 | 0.00 % | ✅ |
| J^-1(2,1) (book: 0.0) | corrected | -0.019245 | -0.019245 | 0.00 % | ✅ |
| J^-1(2,2) (book: 0.166667) | corrected | 0.2 | 0.2 | 0.00 % | ✅ |
| max |feagent B - numpy B with correct J^-1| | corrected | 0 | 1.3878e-17 | 0.00 % | ✅ |

**Notes**: Jacobian and natural derivatives match the book. The inverse is wrong: the book writes (1/150)[[5, -2.887], [0, 30]] (wrong adjugate) and gives [[0.033333, -0.019246], [0, 0.166667]]; the right one is (1/150)[[5, 0], [-2.887, 30]] = [[0.033333, 0], [-0.019245, 0.2]] (source = corrected). The printed B is affected too: the feagent B is compared with the one recomputed with numpy using the correct inverse.

*Code*: `validation/books/bhavikatti/ch13.py`

<a id="bhav-ex-13-2"></a>

### Example 13.2 - Cartesian coordinates of a point given in natural coordinates

📐 · PDF page 248 · plane_stress

Direct isoparametric mapping: the point (xi, eta) = (0.5, 0.6) corresponds to (x, y) = (6.1, 5.7).

**Model**: feagent PlaneQ4 with nodes (2, 1), (8, 3), (7, 7), (3, 5); x = N(xi, eta) X.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| x | book | 6.1 | 6.1 | 0.00 % | ✅ |
| y | book | 5.7 | 5.7 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch13.py`

<a id="bhav-ex-13-3"></a>

### Example 13.3 - Nodal forces equivalent to an interior point force

📐 · PDF page 248 · plane_stress

A force Fx = 8 kN, Fy = 12 kN acts at the point (6, 5) of the element: the book finds the natural coordinates (0.42443, 0.28779), the shape functions at the point and the equivalent nodal forces.

**Model**: feagent PlaneQ4 of Ex. 13.2; inverse mapping with Newton (feagent shape and jacobian), then F_i = N_i(xi, eta) F.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| xi | book | 0.42443 | 0.42443 | 0.00 % | ✅ |
| eta | book | 0.28779 | 0.28779 | 0.00 % | ✅ |
| N1 | book | 0.10248 | 0.10248 | 0.00 % | ✅ |
| Fx1 [kN] | book | 0.81984 | 0.81986 | 0.00 % | ✅ |
| Fy1 [kN] | book | 1.2298 | 1.2298 | 0.00 % | ✅ |
| N2 | book | 0.25362 | 0.25362 | 0.00 % | ✅ |
| Fx2 [kN] | book | 2.029 | 2.029 | 0.00 % | ✅ |
| Fy2 [kN] | book | 3.0434 | 3.0435 | 0.00 % | ✅ |
| N3 | book | 0.45859 | 0.45859 | 0.00 % | ✅ |
| Fx3 [kN] | book | 3.6687 | 3.6687 | 0.00 % | ✅ |
| Fy3 [kN] | book | 5.5031 | 5.5031 | 0.00 % | ✅ |
| N4 | book | 0.1853 | 0.1853 | 0.00 % | ✅ |
| Fx4 [kN] | book | 1.4824 | 1.4824 | 0.00 % | ✅ |
| Fy4 [kN] | book | 2.2236 | 2.2236 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch13.py`

<a id="bhav-ex-13-4"></a>

### Example 13.4 - Nodal forces of a uniform traction on a Q4 side

📐 · PDF page 250 · plane_stress

Uniform traction Tx = 10, Ty = 15 N/mm2 on side 3-4, 447.21 mm long: the book finds 44721.36 N in x and 67082.04 N in y on each of nodes 3 and 4.

**Model**: feagent PlaneQ4 (t = 20 mm) with nodes (200, 100), (800, 300), (700, 700), (300, 500) mm; equivalent_face_load on side 3-4 with traction (10, 15) N/mm2.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| l34 [mm] | book | 447.21 | 447.21 | 0.00 % | ✅ |
| Fx3 [N] | book | 44721 | 44721 | 0.00 % | ✅ |
| Fx4 [N] | book | 44721 | 44721 | 0.00 % | ✅ |
| Fy3 [N] | book | 67082 | 67082 | 0.00 % | ✅ |
| Fy4 [N] | book | 67082 | 67082 | 0.00 % | ✅ |
| nodes 1 and 2 unloaded [N] | book | 0 | 0 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch13.py`

<a id="bhav-prob-13-7"></a>

### Chapter 13 Question 7 - Direct and inverse mapping in a Q4

📐 · PDF page 253 · plane_stress

Exercise with answer: (a) Cartesian coordinates of the point (0.57735, 0.57735); (b) natural coordinates of the point Q = (7, 4).

**Model**: feagent PlaneQ4 with nodes (3, 1), (6, 1), (8, 6), (2, 5) (Fig. 13.12); direct mapping with shape, inverse with Newton.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| (a) x (book: 6.36603) | corrected | 6.4434 | 6.4434 | 0.00 % | ✅ |
| (a) y (book: 4.75088) | corrected | 4.7767 | 4.7767 | 0.00 % | ✅ |
| (a) x = 6,44338 | corrected | 6.4434 | 6.4434 | 0.00 % | ✅ |
| (b) xi | book | 0.91255 | 0.91254 | 0.00 % | ✅ |
| (b) eta | book | 0.21059 | 0.21059 | 0.00 % | ✅ |

**Notes**: (b) matches the book. (a) does not match the coordinates of Fig. 13.12: the book prints x = 6.36603, y = 4.75088, whereas the bilinear map gives x = 6.44338, y = 4.77671 (also computed by hand with N1 = (1 - a)^2/4, N2 = N4 = (1 - a^2)/4, N3 = (1 + a)^2/4; source = corrected).

*Code*: `validation/books/bhavikatti/ch13.py`

<a id="bhav-prob-13-8"></a>

### Chapter 13 Question 8 - Jacobian and B of a rectangle at the Gauss point

📐 · PDF page 253 · plane_stress

Exercise with answer: Jacobian and B matrix at the point (0.57735, 0.57735).

**Model**: feagent PlaneQ4 with the nodes of Fig. 13.13, (0, 10), (30, 10), (30, 40), (0, 40); plus the variant 40 high (y from 0 to 40) that matches the book's answer.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| J11 | book | 15.001 | 15 | 0.01 % | ✅ |
| J22 of the figure (book: 20.0016) | corrected | 15 | 15 | 0.00 % | ✅ |
| J22, 40-high variant | book | 20.002 | 20 | 0.01 % | ✅ |
| B(1,1) = dN1/dx | book | -0.007044 | -0.0070442 | 0.00 % | ✅ |
| B(1,5) = dN3/dx | book | 0.026292 | 0.026289 | 0.01 % | ✅ |
| B(2,2) = dN1/dy, 40 high | book | -0.005283 | -0.0052831 | 0.00 % | ✅ |
| B(2,6) = dN3/dy, 40 high | book | 0.019719 | 0.019717 | 0.01 % | ✅ |

**Notes**: With the figure (30 x 30 rectangle) J = diag(15, 15). The book's answer, J = diag(15.0012, 20.0016), and the y derivatives of B (-5.283e-3 and 0.019719) correspond to a rectangle 40 high: feagent is compared with J22 = 15 for the figure (source = corrected) and with the book's answer for the 40-high variant. In the third row of the printed B some entries are swapped; rows 1 and 2 are compared.

*Code*: `validation/books/bhavikatti/ch13.py`

## Chapter 14

<a id="bhav-ex-14-1"></a>

### Example 14.1 - Continuous beam fixed-propped-propped with different loads

✅ · PDF page 261 · beam

Two-span beam (5 m spans), fixed at the left, with 12 kN/m on the first span and 24 kN/m on the second: nodal rotations, end forces and midspan deflections.

**Model**: Two feagent Hermite beams of 5 m (EI = 1000 kN m2) along X; fixed at node 1, supported at nodes 2 and 3; 12 and 24 kN/m. Plus a 4-element model for the exact midspan deflection.

![bhav-ex-14.1](images/books/bhav-ex-14.1.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| theta2 [rad] | book | -0.035714 | -0.035714 | 0.00 % | ✅ |
| theta3 [rad] | book | 0.080357 | 0.080357 | 0.00 % | ✅ |
| R1 el.1 [kN, kNm] | book | 21.429 | 21.429 | 0.00 % | ✅ |
| R2 el.1 [kN, kNm] | book | 10.714 | 10.714 | 0.00 % | ✅ |
| R3 el.1 (book: -38.571) [kN, kNm] | corrected | 38.571 | 38.571 | 0.00 % | ✅ |
| R4 el.1 [kN, kNm] | book | -53.571 | -53.571 | 0.00 % | ✅ |
| R3 el.2 [kN, kNm] | book | 70.714 | 70.714 | 0.00 % | ✅ |
| R4 el.2 [kN, kNm] | book | 53.571 | 53.571 | 0.00 % | ✅ |
| R5 el.2 [kN, kNm] | book | 49.286 | 49.286 | 0.00 % | ✅ |
| R6 el.2 [kNm] | book | 0 | 0 | 0.00 % | ✅ |
| span 1: Hermite interpolation at midspan [mm] | book | 22.32 | 22.321 | 0.01 % | ✅ |
| span 2: Hermite interpolation (book: -27.9) [mm] | corrected | -72.54 | -72.545 | 0.01 % | ✅ |
| span 1: true midspan deflection (4 elements) [m] | exact | 0.0027902 | 0.0027902 | 0.00 % | ✅ |
| span 2: true midspan deflection (4 elements) [m] | exact | -0.11161 | -0.11161 | 0.00 % | ✅ |

**Notes**: Rotations and end forces match the book, except for the sign of the third component of the element-1 vector: the right-end shear is +38.571 kN (the middle support reaction is 38.571 + 70.714 = 109.29 kN), the book prints -38.571 (source = corrected). The book's midspan deflections are only the Hermite interpolation of the nodal displacements: 22.32 mm (upwards) in the first span matches; in the second the book gets two signs wrong and prints -27.9 mm, the right interpolated value is -72.54 mm (source = corrected). The true deflection adds the interpolation and the fixed-fixed span deflection, -wl^4/384EI: +2.79 mm and -111.6 mm, confirmed by the 4-element model (source = exact).

*Code*: `validation/books/bhavikatti/ch14.py`

<a id="bhav-ex-14-2"></a>

### Example 14.2 - Fixed-propped beam with a midspan force

✅ · PDF page 266 · beam

10 m beam fixed at one end and simply supported at the other with 20 kN at midspan: deflection and rotation under the load, midspan shear and moment, reactions.

**Model**: Two feagent Hermite beams of 5 m, EI = 200 GPa x 24e-6 m4 = 4800 kN m2; fixed at the left, roller at the right, 20 kN at the middle node.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| v2 [m] | book | -0.03798 | -0.037977 | 0.01 % | ✅ |
| freccia = 7PL^3/(768EI) [m] | exact | 0.037977 | 0.037977 | 0.00 % | ✅ |
| rotation under the load (book: -3.2552e-6) [rad] | corrected | -0.0032552 | -0.0032552 | 0.00 % | ✅ |
| theta3 [rad] | book | 0.01302 | 0.013021 | 0.01 % | ✅ |
| M mezzeria [kNm] | book | 31.25 | 31.25 | 0.00 % | ✅ |
| element 1 shear (book: 31.75) [kN] | corrected | 13.75 | 13.75 | 0.00 % | ✅ |
| element 2 shear (magnitude) [kN] | book | 6.25 | 6.25 | 0.00 % | ✅ |
| R1 [kN] | book | 13.75 | 13.75 | 0.00 % | ✅ |
| M1 [kNm] | book | 37.5 | 37.5 | 0.00 % | ✅ |
| R3 [kN] | book | 6.25 | 6.25 | 0.00 % | ✅ |

**Notes**: Two misprints in the book: the rotation under the load is -3.2552e-3 rad (printed -3.2552e-6) and the shear in element 1 is 13.75 kN (printed 31.75); the correct values are compared (source = corrected). The deflection matches the exact solution 7PL^3/(768EI).

*Code*: `validation/books/bhavikatti/ch14.py`

<a id="bhav-ex-14-3"></a>

### Example 14.3 - Consistent trapezoidal load on a beam element

📐 · PDF page 269 · beam

Integrating the Hermite cubics for a load varying linearly from p1 to p2 the book finds F = p1 le/60 {21, 3le, 9, -2le} + p2 le/60 {9, 2le, 21, -3le}.

**Model**: One feagent Hermite beam of length 4 with a trapezoidal add_distributed_load (p1 = 10, p2 = 30 downwards); load vector from assemble_loads.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| F v1 [kN] | book | -32 | -32 | 0.00 % | ✅ |
| F theta1 [kNm] | book | -24 | -24 | 0.00 % | ✅ |
| F v2 [kN] | book | -48 | -48 | 0.00 % | ✅ |
| F theta2 [kNm] | book | 29.333 | 29.333 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch14.py`

<a id="bhav-ex-14-4"></a>

### Example 14.4 - Consistent load vector of a two-span beam

📐 · PDF page 271 · beam

Assembly of the consistent nodal loads: {-40, -26.667, -200, -33.333, -60, 60} (kN, kNm) on the DOFs v1, theta1, v2, theta2, v3, theta3.

**Model**: Two feagent Hermite beams of 4 and 6 m with 20 kN/m and 100 kN at the middle node; assemble_loads.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| F v1 [kN, kNm] | book | -40 | -40 | 0.00 % | ✅ |
| F theta1 [kN, kNm] | book | -26.667 | -26.667 | 0.00 % | ✅ |
| F v2 [kN, kNm] | book | -200 | -200 | 0.00 % | ✅ |
| F theta2 [kN, kNm] | book | -33.333 | -33.333 | 0.00 % | ✅ |
| F v3 [kN, kNm] | book | -60 | -60 | 0.00 % | ✅ |
| F theta3 [kN, kNm] | book | 60 | 60 | 0.00 % | ✅ |

*Code*: `validation/books/bhavikatti/ch14.py`

<a id="bhav-ex-14-5"></a>

### Example 14.5 - Plane portal frame: element matrices and solution

✅ · PDF page 275 · frame

Single-storey portal with fixed feet: the book writes the element stiffness matrices in global coordinates and the reduced load vector, but does not solve the system.

**Model**: Three feagent beams in the X-Y plane: columns 1-2 and 4-3, 3 m high (I0 = 40e-6 m4), beam 2-3, 4 m long (2 I0), A = 4e-3 m2, E = 200 GPa; fixed feet; 20 kN/m on the beam and 30 kN horizontal at node 2.

![bhav-ex-14.5](images/books/bhav-ex-14.5.png)

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| column: 12EI/l^3 (book: 0.444 x 800) [kN, m] | corrected | 3552 | 3555.6 | 0.10 % | ✅ |
| column: -6EI/l^2 (book: -0.667 x 800) [kN, m] | corrected | -5336 | -5333.3 | 0.05 % | ✅ |
| column: EA/l (333.333 x 800) [kN, m] | book | 2.6667e+05 | 2.6667e+05 | 0.00 % | ✅ |
| column: 4EI/l (book: 1.333 x 800) [kN, m] | corrected | 10664 | 10667 | 0.03 % | ✅ |
| column: 2EI/l (book: 0.667 x 800) [kN, m] | corrected | 5336 | 5333.3 | 0.05 % | ✅ |
| beam: EA/l (250.0 x 800) [kN, m] | book | 2e+05 | 2e+05 | 0.00 % | ✅ |
| beam: 12EI/l^3 (book: 0.375 x 800) [kN, m] | corrected | 3000 | 3000 | 0.00 % | ✅ |
| beam: 6EI/l^2 (book: 0.75 x 800) [kN, m] | corrected | 6000 | 6000 | 0.00 % | ✅ |
| beam: 4EI/l (book: 2.0 x 800) [kN, m] | corrected | 16000 | 16000 | 0.00 % | ✅ |
| reduced load DOF 4 [kN, kNm] | book | 30 | 30 | 0.00 % | ✅ |
| reduced load DOF 5 [kN, kNm] | book | -40 | -40 | 0.00 % | ✅ |
| reduced load DOF 6 [kN, kNm] | book | -26.667 | -26.667 | 0.00 % | ✅ |
| reduced load DOF 7 [kN, kNm] | book | 0 | 0 | 0.00 % | ✅ |
| reduced load DOF 8 [kN, kNm] | book | -40 | -40 | 0.00 % | ✅ |
| reduced load DOF 9 [kN, kNm] | book | 26.667 | 26.667 | 0.00 % | ✅ |
| sum of horizontal reactions = -30 kN [kN] | exact | -30 | -30 | 0.00 % | ✅ |
| sum of vertical reactions = 80 kN [kN] | exact | 80 | 80 | 0.00 % | ✅ |
| moment equilibrium about node 1 [kNm] | exact | 0 | -1.4211e-13 | 0.00 % | ✅ |
| u2 (not printed by the book) [mm] | book | - | 5.5662 | - | ✅ |
| u3 (not printed by the book) [mm] | book | - | 5.4537 | - | ✅ |
| theta2 (not printed by the book) [rad] | book | - | -0.0023054 | - | ✅ |
| theta3 (not printed by the book) [rad] | book | - | 5.8385e-04 | - | ✅ |
| M base 1 (not printed by the book) [kNm] | book | - | 17.391 | - | ✅ |
| M base 4 (not printed by the book) [kNm] | book | - | 32.2 | - | ✅ |

**Notes**: The book computes EI = 200e9 x 40e-6 = 800 kN m2, but the product is 8e6 N m2 = 8000 kN m2: all bending terms of the printed matrices (common factor 800) are 10 times too small, whereas the axial ones (EA = 800e3 kN) are right. The bending terms are compared with 10 times the printed value (source = corrected) and the axial ones with the book. The load vector matches the book. feagent completes the solution; with no printed reference, displacements and moments are checked with global equilibrium (sums of reactions and moments, source = exact).

*Code*: `validation/books/bhavikatti/ch14.py`

<a id="bhav-prob-14-2"></a>

### Chapter 14 Question 2 - Continuous beam on three supports with uniform load

✅ · PDF page 291 · beam

Exercise with answer: support rotations of a two-span continuous beam (6 + 4 m) with a uniform load of 20 kN/m.

**Model**: Two feagent Hermite beams of 6 and 4 m, EI = 200 GPa x 4e6 mm4 = 800 kN m2, 20 kN/m over the whole beam, supports at A, B, C.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| theta_A (book: 0.3) [rad] | corrected | -0.1375 | -0.1375 | 0.00 % | ✅ |
| theta_B (book: -0.375) [rad] | corrected | 0.05 | 0.05 | 0.00 % | ✅ |
| theta_C (book: 0.1625) [rad] | corrected | 0.0083333 | 0.0083333 | 0.00 % | ✅ |
| moment at support B (three moments) [kNm] | exact | -70 | -70 | 0.00 % | ✅ |

**Notes**: The book's answer (theta_A = 0.3, theta_B = -0.375, theta_C = 0.1625 rad) cannot be reproduced with the data of the figure, not even by changing EI (the ratios between the three rotations differ). The three-moment equation gives M_B = -w(l1^3 + l2^3)/(8(l1 + l2)) = -70 kNm and theta_A = -(wl1^3/24 + M_B l1/6)/EI = -0.1375, theta_B = 0.05, theta_C = 0.00833 rad: feagent matches these values (source = corrected).

*Code*: `validation/books/bhavikatti/ch14.py`

<a id="bhav-prob-14-4"></a>

### Chapter 14 Question 4 - Global matrix of a frame element inclined at 30 degrees

✅ · PDF page 291 · frame

Exercise with answer: 6x6 global stiffness matrix of a plane frame element inclined at 30 degrees to the x axis.

**Model**: One feagent beam in the X-Y plane inclined at 30 degrees, E = 200 GPa, I = 4e-6 m4, A = 4e-3 m2; stiffness_global on the DOFs ux, uy, rz; length 5 m (figure) and 4 m.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| l = 5 m: k(1,1) [x1000] | corrected | 120.02 | 120.02 | 0.00 % | ✅ |
| l = 5 m: k(1,2) [x1000] | corrected | 69.249 | 69.249 | 0.00 % | ✅ |
| l = 5 m: k(1,3) [x1000] | corrected | -0.096 | -0.096 | 0.00 % | ✅ |
| l = 5 m: k(2,2) [x1000] | corrected | 40.058 | 40.058 | 0.00 % | ✅ |
| l = 5 m: k(2,3) [x1000] | corrected | 0.16628 | 0.16628 | 0.00 % | ✅ |
| l = 5 m: k(3,3) [x1000] | corrected | 0.64 | 0.64 | 0.00 % | ✅ |
| l = 5 m: k(3,6) [x1000] | corrected | 0.32 | 0.32 | 0.00 % | ✅ |
| l = 5 m: k(1,1) = 120.019 [x1000] | corrected | 120.02 | 120.02 | 0.00 % | ✅ |
| l = 4 m: k(1,1) [x1000] | book | 150.03 | 150.04 | 0.01 % | ✅ |
| l = 4 m: k(1,2) [x1000] | book | 86.558 | 86.538 | 0.02 % | ✅ |
| l = 4 m: k(2,2) [x1000] | book | 50.07 | 50.112 | 0.08 % | ✅ |
| l = 4 m: k(3,3) [x1000] | book | 0.8 | 0.8 | 0.00 % | ✅ |
| l = 4 m: k(3,6) [x1000] | book | 0.4 | 0.4 | 0.00 % | ✅ |

**Notes**: The figure gives l = 5 m, but the printed answer (x 1000 kN/m) corresponds to l = 4 m: 150.025, 86.558, 50.070, 0.8 and 0.4 match the 4 m element within 0.1 %. The coupling terms -0.12 and 0.208 match neither length (with 4 m they are -0.15 and 0.260). The first row and the diagonal of the element of the figure, l = 5 m, are compared: 120.019, 69.249, -0.096, ... (source = corrected), and the book entries with the 4 m element.

*Code*: `validation/books/bhavikatti/ch14.py`

<a id="bhav-sec-14-5-timoshenko-locking"></a>

### Section 14.5 - Two-node Timoshenko beam: shear locking and reduced integration

🟡 · PDF page 286 · beam

The book builds the two-node Timoshenko element with linear interpolation of w and theta, splits its bending and shear stiffness and shows on a one-element cantilever that with exact integration (2 points) the deflection of thin beams depends on the shear term (locking), whereas with 1 point it depends on bending only.

**Model**: The book's linear Timoshenko element (w and theta interpolated linearly) built with numpy, shear integrated with 1 and 2 Gauss points; one-element cantilever with P and M at the tip, b = 1, alpha = 5/6, nu = 0. Reference: feagent Timoshenko beam (add_beam with shear=True, Asy = alpha A), exact with one element.

| Quantity | Ref. | Reference | feagent | Error | |
|---|---|---|---|---|---|
| shear, 2 points = (14.51) | book | 0 | 1.8190e-12 | 0.00 % | ✅ |
| shear, 1 point = (14.52) with the sign corrected | corrected | 0 | 0 | 0.00 % | ✅ |
| thick, 1 point: w2 = corrected (14.54) | corrected | 0.001488 | 0.001488 | 0.00 % | ✅ |
| thick, 1 point: theta2 = (14.55) | book | 0.00144 | 0.00144 | 0.00 % | ✅ |
| thick, 2 points: book w2 | book | 2.3583e-04 | 2.3583e-04 | 0.00 % | ✅ |
| thick, 2 points: book theta2 | book | 1.8783e-04 | 1.8783e-04 | 0.00 % | ✅ |
| thick: feagent deflection = Pl^3/3EI + Pl/(alpha G A) + Ml^2/2EI | exact | 0.001808 | 0.001808 | 0.00 % | ✅ |
| thick: linear element 2 points / exact | book | - | 0.13043 | - | ✅ |
| thick: linear element 1 point / exact | book | - | 0.82301 | - | ✅ |
| thin, 1 point: w2 = corrected (14.54) | corrected | 22.501 | 22.501 | 0.00 % | ✅ |
| thin, 1 point: theta2 = (14.55) | book | 22.5 | 22.5 | 0.00 % | ✅ |
| thin, 2 points: book w2 | book | 0.0065987 | 0.0065987 | 0.00 % | ✅ |
| thin, 2 points: book theta2 | book | 0.0053987 | 0.0053987 | 0.00 % | ✅ |
| thin, 1 point: corrected (14.56), bending only | corrected | 22.5 | 22.501 | 0.01 % | ✅ |
| thin, 2 points: (14.57), deflection governed by beta (locking) | book | 0.0066 | 0.0065987 | 0.02 % | ✅ |
| thin, 2 points: (14.58) | book | 0.0054 | 0.0053987 | 0.02 % | ✅ |
| thin: feagent deflection = Pl^3/3EI + Pl/(alpha G A) + Ml^2/2EI | exact | 27.501 | 27.501 | 0.00 % | ✅ |
| thin: linear element 2 points / exact | book | - | 2.3994e-04 | - | ✅ |
| thin: linear element 1 point / exact | book | - | 0.81819 | - | ✅ |

**Notes**: feagent has no linear Timoshenko element (its Timoshenko beam uses the exact two-node formulation, free of locking): the book's element is built with numpy and compared with the book's closed forms. Misprints found: in (14.52) entry (4,3) needs a minus sign (the matrix is symmetric); (14.50) is the bending part but is labelled [k]_se; in (14.54) the moment term is l M/(2 psi), not M/(2 psi), and accordingly (14.56) becomes w2 = (l/(2 psi))(lP/2 + M). With l = 2 the misprints would change the numbers: the corrected forms are compared (source = corrected).

*Code*: `validation/books/bhavikatti/ch14.py`
