---
layout: default
title: "12 - Sparse Solver"
parent: English
nav_order: 12
---

# 12 - Sparse Solver

For models with many degrees of freedom, the sparse solver (COO→CSR + SuperLU) is much more efficient than the dense solver.

## When to use

| DOFs | Dense | Sparse |
|------|-------|--------|
| < 500 | OK | overhead |
| 500–5000 | slow | recommended |
| > 5000 | memory exhaustion | required |

## Usage

```python
res = m.solve(sparse=True)
```

Results are **identical** to the dense solver (up to numerical precision).

## Verification

```python
rd = m.solve(sparse=False)
rs = m.solve(sparse=True)
import numpy as np
assert np.allclose(rd.U, rs.U, atol=1e-10)
```

## Implementation

Sparse assembly follows the standard approach (Cuvelier, Japhet & Scarella, BIT 2016):
1. For each element, generate triplets `(row, col, value)` from the 12×12 matrix
2. Concatenate all element triplets into a `scipy.sparse.coo_matrix`
3. Conversion to CSR automatically sums contributions at the same indices

This avoids both the allocation of the dense `ndof × ndof` matrix and the element-by-element scatter.

## Solver optimizations

beamfeapy uses several techniques to approach the speed of compiled solvers
(see [29 - Performance & Benchmarks](en-29-performance.html)):

- **Closed-form distributed loads**: equivalent nodal forces of uniform/trapezoidal
  loads spanning the whole element are computed with exact Hermite formulas,
  with no Gauss quadrature (identical result).
- **Batched vectorized assembly**: the `Kᵍ = Tᵀ K T` rotations of all elements are
  done with two `np.matmul` calls on `(ne, 12, 12)` arrays.
- **Geometry caches** (axis vector, `R`/`T` matrices, local `K`) keyed on values:
  they self-invalidate when geometry or section change.
- **Many combinations, one factorization** — `Model.solve_many`:

  ```python
  results = m.solve_many({"ULS": {"G": 1.35, "Q": 1.5}, "SLS": {"G": 1, "Q": 1}})
  ```

- **Optional pypardiso** (MKL Pardiso): if installed (`pip install beamfeapy[fast]`)
  it is used automatically as the sparse solver instead of SuperLU.

## Sparse modal and buckling analysis

Modal and buckling also have a sparse path (ARPACK, on by default for large
models) that extracts only the requested modes without factorizing dense
matrices:

```python
modal = m.modal(n_modes=6, mass_source={"G": 1.0}, sparse=True)   # eigsh shift-invert
buck  = m.buckling(n_modes=4, cases="G", sparse=True)             # generalized eigsh
```

Results match the dense path; modal is over 25× faster and buckling up to ~160×
(the old path used a dense non-symmetric `eig`). All comparison tables and charts
are on the [29 - Performance & Benchmarks](en-29-performance.html) page.
