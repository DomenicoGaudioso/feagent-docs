---
layout: default
title: "29 - Performance & Benchmarks"
parent: English
nav_order: 29
---

# 29 - Performance & Benchmarks

beamfeapy is written in Python/NumPy/SciPy, yet it uses vectorization and sparse
linear-algebra techniques that bring it close to compiled solvers (OpenSees, C++)
and make it markedly faster than other pure-Python solvers.

All comparisons use the same model: a **3D cantilever** of `N` beam elements
(total length 50 m), with a distributed load + tip force for statics, self-mass
for modal, and axial compression for buckling. Only the **analysis time**
(assembly + solution) is measured, excluding model construction. Measurements are
run in isolation (one benchmark at a time) on the same machine; charts are produced
by `benchmark/gen_benchmark_charts.py` from the scripts `benchmark/benchmark_multi.py`
(static) and `benchmark/benchmark_eigen.py` (modal/buckling).

Solvers compared: **beamfeapy**, **OpenSeesPy** (the C++ FEM reference), **PyNite**
(pure-Python 3D frame), **CALFEM** (`calfem-python`, 3D `beam3e` element, dense
assembly), **pystran** (pure-Python 3D frame, dense) and **anastruct** (2D frame).
All agree on the tip deflection (relative error ≤ ~3·10⁻⁵, usually to machine
precision), so the timing comparison is meaningful.

> OpenSeesPy imports on Python 3.10 (DLL issue on 3.14): OpenSees comparisons run
> on Python 3.10. The pure-Python solvers (beamfeapy, PyNite, CALFEM, pystran,
> anastruct) are compared on Python 3.14.

## Static analysis — beamfeapy vs OpenSees

![Static: beamfeapy vs OpenSees](images/bench_static_vs_opensees.png)

| DOFs | beamfeapy | OpenSeesPy (C++) | ratio |
|-----:|----------:|-----------------:|------:|
| 306 | 3.3 ms | 0.7 ms | 4.7× |
| 1,206 | 8.6 ms | 3.1 ms | 2.8× |
| 4,806 | 32 ms | 13 ms | 2.4× |
| 19,206 | 122 ms | 59 ms | **2.1×** |

Tip deflection matches OpenSees to machine precision on well-conditioned models.
The gap **shrinks as the problem grows** (Python overhead amortizes): at 19,000
DOFs beamfeapy is about **2× OpenSees**, a strong result for a pure-Python solver.

## Static analysis — pure-Python solvers

![Static: pure-Python solvers](images/bench_static_python.png)

| DOFs | beamfeapy | PyNite | CALFEM (3D) | pystran (3D) | anastruct (2D) |
|-----:|----------:|-------:|------------:|-------------:|---------------:|
| 306 | 2.7 ms | 20 ms | 12 ms | 23 ms | 25 ms |
| 1,206 | 6.9 ms | 84 ms | 73 ms | 117 ms | 264 ms |
| 4,806 | 28 ms | 461 ms | 775 ms | 1,087 ms | 4,765 ms |
| 19,206 | 116 ms | 3,773 ms | out of scale | out of scale | out of scale |

beamfeapy is by far the fastest pure-Python solver: **~16× faster than PyNite**,
**~28× than CALFEM**, **~39× than pystran** and **~170× than anastruct** at 4806
DOFs. CALFEM, pystran and anastruct use dense assembly/solve (O(n³) time, O(n²)
memory): beyond ~5000 DOFs they become impractical, while beamfeapy (sparse)
scales to tens of thousands of DOFs. All produce the same tip deflection
(relative error ≤ ~3·10⁻⁵).

## Modal analysis (6 modes)

![Modal: beamfeapy vs OpenSees](images/bench_modal.png)

Sparse ARPACK eigensolver (shift-invert at σ=0):

| DOFs | beamfeapy | OpenSeesPy | f₁ error |
|-----:|----------:|-----------:|---------:|
| 1,206 | 9.8 ms | 2.3 ms | 7·10⁻⁹ |
| 4,806 | 30 ms | 10 ms | 5·10⁻⁷ |
| 19,206 | 116 ms | 44 ms | 2·10⁻⁵ |

About **2.6–4× OpenSees**, with matching natural frequencies. Before the sparse
path, modal used a dense `eigh` with Guyan condensation, over 100× slower on large
models.

## Buckling analysis (4 critical multipliers)

![Buckling: sparse vs dense](images/bench_buckling.png)

Sparse generalized eigensolver `(−K_g) φ = μ K φ`, μ = 1/λ:

| DOFs | beamfeapy (sparse) | beamfeapy (dense, old `eig`) | speed-up |
|-----:|-------------------:|-----------------------------:|---------:|
| 1,206 | 25 ms | 286 ms | 11× |
| 4,806 | 92 ms | 14,676 ms | **160×** |
| 19,206 | 383 ms | minutes | — |

The first multiplier matches the Euler solution. Switching from the dense
non-symmetric `eig` to the sparse `eigsh` yields a **~160× speed-up** on large
models.

## What makes the solver fast

- **Closed-form** equivalent nodal forces for distributed loads (no Gauss
  quadrature).
- **Vectorized assembly** with batched `np.matmul` on `(ne, 12, 12)` arrays.
- **Geometry caches** (axis vector, `R`, `T`, local `K`) keyed on values.
- **Per-element load index** built once and reused.
- **Sparse eigensolvers** (ARPACK) for modal and buckling.
- **`solve_many`**: many combinations with a single factorization.
- Optional **pypardiso** (MKL) as the sparse solver (`pip install beamfeapy[fast]`).

## Reproducibility

```bash
# the extra solvers install with:
pip install calfem-python pystran anastruct PyNiteFEA

python   benchmark/benchmark_multi.py    # static: beamfeapy, PyNite, CALFEM, pystran, anastruct
py -3.10 benchmark/benchmark_multi.py    # adds OpenSeesPy (DLL on Python 3.10)
py -3.10 benchmark/benchmark_eigen.py    # modal + buckling vs OpenSeesPy
python   benchmark/gen_benchmark_charts.py   # regenerate this page's charts
```

Absolute timings are machine-dependent; the ratios are what matters.
