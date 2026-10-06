---
layout: default
title: "45 - Solid elements: case studies"
parent: English
nav_order: 45
---

# 45 - Solid elements: case studies

The **volumfeapy** case studies, redone with feagent's elements. Each case has
a closed-form comparison or an equilibrium check; numbers and figures come
from `validation/solidi/casi_studio.py`, which also rewrites the full table in
`validation/solidi/README.md`. Outcome: **32 of 32 comparisons within
tolerance**. The numbers published with volumfeapy were not reused: they came
from defective elements (see [chapter 44](en-44-solid-formulation.html)).

Steel E = 210 GPa, ν = 0.3, ρ = 7850 kg/m³ unless stated.

## CS01 Uniaxial tension

2 x 1 x 1 m Hex8 block, 10 MPa tension on face x = 2 m. Elongation and lateral
contraction equal σL/E and −νσb/E to round-off.

![CS01](images/solidi/cs01_trazione.png)
*Figure 1 — Displacement ux under uniform tension.*

## CS02 Hydrostatic pressure

Cube with 5 MPa on all faces: σxx = σyy = σzz = −5 MPa, zero von Mises
(2·10⁻¹⁴ MPa), exact volume change −p/K.

## CS03 Patch test

2 x 2 x 2 cell cube with a shifted centre node (MacNeal-Harder patch), linear
displacements imposed on the boundary: the inner node returns to the linear
field and stresses are constant in every element.

| Element | Elements in the patch | Max stress error |
|---|---|---|
| Hex8 | 8 | 4·10⁻¹⁴ % |
| Incompatible-mode Hex8 | 8 | 4·10⁻¹⁴ % |
| Tet4 | 48 | 1·10⁻¹³ % |
| Wedge6 | 16 | 6·10⁻¹⁴ % |
| Pyramid5 | 48 | 2·10⁻¹³ % |

## CS04 Cantilever: the six elements compared

2.0 x 0.2 x 0.4 m clamped cantilever, 10 kN at the tip as a traction on the
end face. Reference: Timoshenko beam (bending plus shear). Tet4, Wedge6 and
Pyramid5 meshes split the same hexahedra.

| Element | Mesh 10 x 1 x 2 | Mesh 20 x 2 x 4 |
|---|---|---|
| Hex8 | 0.859 | 0.951 |
| **Incompatible-mode Hex8** | **0.974** | **0.984** |
| Tet4 | 0.517 | 0.790 |
| Wedge6 | 0.859 | 0.949 |
| Pyramid5 | 0.791 | 0.928 |
| **Tet10 (Gmsh, size 0.1 / 0.05 m)** | **0.989** | **0.990** |

*Ratio of the mean tip deflection to the Timoshenko solution.* Linear elements
lock in bending; incompatible-mode Hex8 and Tet10 are the ones to use when the
solid bends. The remaining 1 % comes from the clamp, which in the solid also
restrains lateral contraction.

![CS04](images/solidi/cs04_mensola_sxx.png)
*Figure 2 — σxx in the 20 x 2 x 4 incompatible-mode Hex8 cantilever, amplified deformation.*

## CS05 Column under self-weight

0.6 x 0.6 x 6 m column supported at the base, automatic self-weight. Weight
and reaction 166.34 kN exact; top shortening 6.597 μm against γH²/(2E) =
6.601 μm (0.06 %); σzz in the first element exact.

![CS05](images/solidi/cs05_peso_proprio.png)
*Figure 3 — σzz linear with height.*

## CS06 Thermal actions

* Cube with all nodes fixed and ΔT = 25 K: σ = −Eα ΔT / (1 − 2ν) = −157.5 MPa exact.
* Free 3.0 x 0.3 x 0.5 m beam with 40 K between the faces
  (`add_solid_temperature_field`): tip deflection −4.32 mm exact and zero
  stresses (5·10⁻¹⁰ MPa) with incompatible-mode Hex8.

![CS06](images/solidi/cs06_termica.png)
*Figure 4 — Free thermal bending: the beam curves without stresses.*

## CS07 Modal analysis of a cantilever

4.0 x 0.2 x 0.4 m cantilever, 40 x 2 x 4 incompatible-mode Hex8, HRZ self-mass.
Against Euler-Bernoulli: first weak mode 10.48 Hz vs 10.44 (0.4 %), first
strong 20.82 vs 20.89 (0.3 %), second weak 64.9 vs 65.5 (0.8 %, the solid
includes shear and rotary inertia).

![CS07](images/solidi/cs07_modo1.png)
*Figure 5 — First bending mode.*

## CS08 Plate with a hole (Kirsch)

Quarter of a 2 x 2 m plate, 1 cm thick, hole radius 0.1 m, 10 MPa tension;
radial incompatible-mode Hex8 mesh graded towards the hole. Concentration
factor Kt = 3.10 against 3.03 for a finite-width plate with d/W = 0.1 (3.0 for
an infinite plate): 2.2 %.

![CS08](images/solidi/cs08_kirsch.png)
*Figure 6 — σxx around the hole.*

## CS09 Thick cylinder under pressure (Lamé)

Cylinder with radii 1 and 2 m, 50 MPa internal pressure, plane strain, quarter
section. Radial displacement at the bore 0.4537 mm vs 0.4540 (0.05 %); hoop
stress at the bore 84.7 MPa vs 83.3 (1.6 %, nodal value on a curved boundary
approximated by chords).

![CS09](images/solidi/cs09_lame.png)
*Figure 7 — von Mises in the Lamé cylinder.*

## CS10 Chimney under wind

60 m reinforced concrete chimney, mean radius from 3.0 to 2.05 m, 0.40 m
thick, leeward service opening at the base; wind pressure varying with height
and angle on the outer faces. The base reaction balances the wind thrust
exactly; at 30 m σzz on the windward fibre is 0.310 MPa against 0.306 MPa from
Navier's formula for the thin tube (1.2 %).

![CS10](images/solidi/cs10_ciminiera.png)
*Figure 8 — σzz in the chimney; the opening only disturbs its neighbourhood.*

## CS11 Thin-walled box girder

6 m cantilever box, 1.20 x 0.90 m, 6 cm walls with a single incompatible-mode
Hex8 through the thickness; 25 kN at the tip carried by the webs. Mean tip
deflection 0.999 of the Timoshenko beam with the web shear area; σxx in the top
slab at mid-span equal to Navier's. In the original branch webs and slabs did
not share nodes: the model was rebuilt with the walls welded on common nodes.

![CS11](images/solidi/cs11_cassone.png)
*Figure 9 — σxx in the box girder, amplified deformation.*

## Further comparisons

* **NAFEMS LE10** (thick plate under pressure): Tet10 −5.36 MPa vs −5.38 at D;
  report in `validation/nafems/LE10`.
* **NAFEMS LE11** (cylinder, taper and sphere under a temperature field):
  −104.5 MPa vs −105 at A; `validation/nafems/LE11`.
* **NAFEMS FV42** (thick hollow sphere, radial vibration): five modes within
  0.35 %; `validation/nafems/FV42`.
* **NAFEMS FV52** with the solid model: `validation/nafems/FV52`.
* **Pier cap on a beam column** (`examples/ex19_solid_pier_cap.py`): mixed
  model, emits the `SOLIDI_MISTI` warning (see chapter 43).
