---
layout: default
title: "30 - IABSE Grillage Bridge"
parent: "14 - Usage Examples"
grand_parent: English
nav_order: 5
---

# 30 - IABSE Grillage Bridge

This example shows how to generate a Word report, also suitable for opening in
Pages, for a real bridge studied with a grillage model.

The selected reference is the IABSE-JSCE 2020 paper by **Lu, Barker and Judd**
on the **Evanston I-80** bridge in Wyoming. The bridge geometry and grillage
layout are taken from the published case. The truck loads reported in the
paper are not simulated here because beamfeapy does not yet expose a moving
load API.

Source: [Experimental and Numerical Studies on Post-Facture Behavior of Simple-Span Steel Girder Bridges](https://iabse-bd.org/2020/pdf/50.pdf)

## Model Layout

- I-80 highway bridge near Evanston, Wyoming
- 4 spans: 25.6 + 36.6 + 36.6 + 25.6 m
- concrete deck on steel girders
- equivalent grillage with 5 longitudinal girders and transverse slab members
- 3D view with Z kept vertical

![Bridge model preview](images/ex30_ponte_graticcio_iabse_report_preview.png)

## Load Cases

| Case | Description |
|------|-------------|
| DEAD | Nominal grillage self-weight |
| SDL | Superimposed dead loads distributed to the girders |
| LANE_EQ | Static equivalent distributed lane load |
| THERM | Uniform temperature change plus vertical gradient |
| SHR | Shrinkage as an imposed strain (axial + $N\cdot e$ curvature), applied only to the uncracked segments |

> Further reading: the theory of shrinkage in composite sections (primary
> isostatic effect $N$, $N\cdot e$ and secondary hyperstatic effect) and its
> modelling are covered in
> [31 - Shrinkage in composite sections](en-31-shrinkage-composite-sections.html).

The model layout is shown in plan view. Internal forces and reactions are
generated in 3D, so diagrams remain readable for a planar grillage.

## Analysed Combinations

- Permanent loads: `DEAD + SDL`
- Service: `DEAD + SDL + LANE_EQ`
- Thermal and shrinkage: `THERM + SHR`
- Rare combination: `DEAD + SDL + LANE_EQ + THERM + SHR`

The report shows internal forces and reactions separately for each load case
and for each combination.

## Units in the Diagrams

All report figures use the engineering units common in structural practice,
obtained by converting the solver's internal SI values:

| Quantity | Unit |
|----------|------|
| Bending and torsional moments (Mz, My, T) | kN·m |
| Shear and axial forces (Vy, Vz, N) | kN |
| Displacements (deformed shape) | mm |

The deformed shape is drawn in a 3D view even for the planar grillage, so the
dominant vertical displacement stays readable; the peak is annotated in
millimetres. In the internal-force diagrams the out-of-plane axis no longer
shows the Z coordinate but the internal-force value itself (kN·m or kN), so the
diagram amplitude is readable in engineering units.

![Mz bending moment on the bridge model](images/ex30_ponte_graticcio_iabse_Mz_preview.png)

## Word Report

[Download the IABSE bridge Word report](assets/ex30_ponte_graticcio_iabse_report.docx)

To regenerate the document:

```bash
python scripts/generate_composite_bridge_word_report.py
```

Core workflow:

```python
from scripts.generate_composite_bridge_word_report import (
    build_iabse_evanston_grillage_model,
    solve_bridge_cases,
)
from beamfeapy.reporting import create_word_report

model, groups = build_iabse_evanston_grillage_model()
results, combinations = solve_bridge_cases(model)

report = create_word_report(
    model,
    results,
    options={
        "title": "IABSE Evanston I-80 grillage bridge - FEM report",
        "load_cases": ["DEAD", "SDL", "LANE_EQ", "THERM", "SHR"],
        "combinations": combinations,
        "force_components": ["Mz", "Vy", "N"],
    },
)
```
