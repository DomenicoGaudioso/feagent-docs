---
layout: default
title: "47 - Stresses in beams and shells, composite sections by phase"
parent: English
nav_order: 47
---

# 47 - Stresses in beams and shells, composite sections by phase

feagent computes normal stresses in beams fibre by fibre and shell stresses, and
shows them in the GUI along the beams, on the extruded view and in the cross
section. In steel-concrete composite sections each phase has its own modular
ratio `n`: the same fibre gets a different stress from each phase, and stresses
are summed phase by phase (EN 1994-2 5.4.2.2).

## Composite sections: parts and phases

A section is described by its **parts** (sheet `SectionPart`): `Section`, `Part`,
`Kind` (`steel`, `concrete` divided by n, `rebar`), rectangle `Y, Z, h, b` (h along
local y, b along local z) or polygon `Points` (`y:z; y:z; ...`), bar `Area`,
`Er` = E / E_steel. Local y is the section depth: with Z vertical (beams in the
X-Y plane, loads along Z) set `RefX, RefY, RefZ = 0, 0, 1`. A, Iy and Iz of a
composite section are left empty in the Section sheet and computed from the parts.

**Phases** go in the `StressPhase` sheet: `Phase`, `Group` (section group created
for the phase; empty = default section), `n` (empty = concrete not acting, wet
concrete) or `n0, phi, psiL` with `n = n0 (1 + psiL phi)`, `Cracked` (concrete
removed, bars kept) and `Cases` (load cases of the phase). The user decides which
phase each case belongs to; each case is solved with its own section and
combinations are computed **by superposition of phases**.

The catalogue example `composite_girder` is a 30 m girder with three phases; its
checks compare the stresses with a hand calculation (Steiner) to 1e-12.

## From the library

`CompositeSection(parts).section(Phase(n))` gives the homogenised section of a
phase; `Model.solve_staged(combos)` solves each case with its phase and sums;
`Result.beam_stresses(elem)` returns `sigma` (stations x fibres), `phases` and
`labels`. `Result.shell_element_stresses(id)` gives sigma x, sigma y, tau xy,
principal stresses and von Mises on the top, mid and bottom faces of a shell.

## In the GUI

Results > σ max / σ min along the beams (or a chosen fibre); the extruded view (E)
draws the real section coloured by stress; *Stresses in the section…* shows the
section with the centroid of each phase, the sigma(y) diagram with the jump at the
steel-concrete interface, phase contributions and a fibre table; shell maps of
von Mises and principal stresses. Colour maps use an equal-band legend with the
value at each boundary; the stress unit is chosen separately (MPa by default).

## Current limits

Primary effects of shrinkage and temperature (self-equilibrated stresses) are not
included yet; tapered beams use interpolated properties without phases; the
per-section detail is available for linear static analyses.
