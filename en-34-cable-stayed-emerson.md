---
layout: default
title: "34 - Bill Emerson Cable-Stayed Bridge"
parent: "14 - Usage Examples"
grand_parent: English
nav_order: 9
---

# 34 - Bill Emerson Cable-Stayed Bridge (Cape Girardeau)

3D reconstruction and self-weight analysis of the **Bill Emerson Memorial
Bridge** (Cape Girardeau, MO), the ASCE benchmark for structural control of
cable-stayed bridges (Dyke, Caicedo et al., 2003). The model uses the
[cable elements](en-33-cables-cable-stayed-suspension.html) (stays with Ernst
modulus) and the non-linear solver `solve_nonlinear`.

> **Note on the data.** The ASCE benchmark section properties are not public. The
> **real geometry** is therefore used with representative composite-deck and
> concrete-tower sections, **calibrating** the deck stiffness and mass so the 1st
> vertical mode matches the benchmark/identified one.

## 1. Real geometry

| Quantity | Value |
|----------|------:|
| Main span | 1150 ft = 350.6 m |
| Side spans | 468 ft = 142.7 m |
| Towers (H-shaped, RC) | 356 ft ≈ 108 m |
| Stays | 128, semi-fan (two planes) |
| Deck width | ~26.7 m |

## 2. FEM model

The deck is modelled as a spine on **two planes** of girders (composite deck)
connected by floor beams. The **towers are H-shaped**: two RC legs joined by
struts, based at the pier (below the deck), with the stays anchored in a
**semi-fan** along the upper part of the legs. Panel (c) shows the H shape of the
tower with its struts.

![Cable-stayed bridge FEM model](images/ex34_emerson_model.png)

## 3. Loads

Load = deck **self-weight** (distributed on the girders) + stay self-weight
(automatic). No live load.

![Loads on the cable-stayed bridge](images/ex34_emerson_loads.png)

## 4. Static analysis

Non-linear solution (Newton-Raphson), converging in a few iterations.

![Deformed shape of the cable-stayed bridge](images/ex34_emerson_deformed.png)

![Internal forces of the cable-stayed bridge](images/ex34_emerson_forces.png)

**Validation:** the **global vertical equilibrium** is satisfied exactly (ΣR =
total load). Stay tensions grow from the shortest (near the towers) to the longer
back-stays (≈ 1,000 → 4,200 kN). The self-weight deflection is the *as-built* one,
without the construction camber.

## 5. Modal analysis

Cable self-weight is included and the stays are linearized about equilibrium. The
computed vertical frequencies are compared with the experimentally **identified**
ones (NExT/ERA) and with the **benchmark FE model**:

| Vertical mode | beamfeapy | identified | FE benchmark |
|---------------|----------:|-----------:|-------------:|
| 1st | **0.319 Hz** | 0.323 Hz | 0.290 Hz |
| 2nd | 0.870 Hz | 0.414 Hz | 0.370 Hz |
| 3rd | 1.024 Hz | 0.573 Hz | 0.581 Hz |

The **1st vertical mode** (0.319 Hz) matches the experimentally identified value
(0.323 Hz, **−1%**). At higher modes the real spectrum is denser (7 vertical
modes below 1 Hz, with antisymmetric modes interleaved); the stiffer spine model
does not reproduce all of them.

![Vertical mode shapes (elevation)](images/ex34_emerson_modes.png)

**Mode shapes in 3D.** Releasing the out-of-plane DOFs makes the model 3D, also
yielding the lateral and torsional modes:

| 3D mode | beamfeapy | reference |
|---------|----------:|----------:|
| 1st vertical | 0.319 Hz | 0.323 Hz (identified) |
| 1st lateral | 0.515 Hz | 0.649 Hz (benchmark, lat-tors.) |
| 1st torsional | 0.558 Hz | — |

![3D mode shapes of the cable-stayed bridge](images/ex34_emerson_modes_3d.png)

## 6. References

- S.J. Dyke, J.M. Caicedo, G. Turan, L.A. Bergman, S. Hague, *Phase I benchmark
  control problem for seismic response of cable-stayed bridges*, J. Struct. Eng.
  129(7), 2003, 857-872.
- Y. Zhang, J.M. Caicedo, *Modal identification of the Bill Emerson Bridge*,
  14WCEE, 2008 (identified frequencies).
- H.J. Ernst, *Der E-Modul von Seilen…*, Der Bauingenieur 40, 1965.

## 7. How to regenerate

```bash
python scripts/generate_real_bridges_figures.py   # model, loads, deformed, forces, modes (2D and 3D)
python scripts/modal_real_bridges.py              # modal comparison table
```

See also the [Vincent Thomas suspension bridge](en-35-suspension-vincent-thomas.html)
and the [cable-element theory](en-33-cables-cable-stayed-suspension.html).
