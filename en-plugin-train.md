---
layout: default
title: "Train plugin: a train crossing a bridge"
parent: English
nav_order: 48
---

# Train plugin: a train crossing a bridge

The `train` plugin simulates a railway train crossing a simply supported beam over a range
of speeds and returns, at midspan, vertical acceleration, velocity and displacement, the
peak acceleration, the critical speed and the dynamic amplification factor (DAF). It
reworks the OPSTrainLoad tool (2D moving force, 2D vehicle-structure interaction, 3D deck
with 6 DOF per node and an eccentric track) but **all the computation is feagent's**: no
OpenSees, no dependency on the original repositories.

## Usage

```python
from feagent import plugins
p = plugins.get("train")
r = p.run("scansione_velocita", v_min=100, v_max=300, passo_v=20, modello_veicolo="tutti")
print(r.message)
r.tables, r.charts
```

CLI: `feagent plugin run train scansione_velocita --param v_min=100 --param v_max=300`.

## Actions

| Action | What it does |
|---|---|
| `scansione_velocita` | transit at every speed of the range; tables and curves of acceleration, displacement, DAF |
| `transito` | one transit, time histories of displacement, velocity, acceleration (and rotation in 3D) |
| `stima_analitica` | natural frequency, critical speed `2 L f1`, resonance speeds `f1 d / i` |
| `genera_ponte` | bridge model with the train at a given position (load case `TRENO`) |
| `relazione_word` | Word report of the scan in house style (fixed file name, overwritten) |

Main parameters: span, mesh size, area, inertia, E, nu, mass per unit length, damping;
scheme `2d` or `3d` (lateral inertia, J, track eccentricity, rotational inertia); train
(`OPS_default`, 7 axles of 170 kN; `LM71`; `uniforme`; `personalizzato` as an
axle-spacing/load table; or any vehicle of the feagent catalogue); speed range; vehicle
model (`forza_viaggiante`, `massa_viaggiante`, `oscillatore`, `tutti`); suspension
stiffness and damping per axle; time step and free-vibration time.

## Method

The bridge is a `feagent.Model` of Euler-Bernoulli beams (planar, or 6 DOF per node in 3D).
`K` and `M` are assembled by feagent, damping is Rayleigh (`RayleighDamping`) set on the
first and third vertical bending modes, and time integration is the average-acceleration
Newmark scheme of `feagent.dynamics`.

Each axle at `x(t) = v t - d` has a *contact vector* `c` built from the shape functions of
the element below it (plus the lever arm `e` for torsion): the downward displacement of the
contact point is `c u` and a load `P` is `P c` in nodal forces.

* **moving force**: only `P c`; matrices are constant;
* **moving mass**: `M(t) = M + sum m c c^T`, `m = P / g`;
* **oscillator**: mass, spring and dashpot per axle, integrated together with the bridge.
  The vehicle DOF are condensed on the Newmark step, leaving the bridge with the effective
  stiffness `K_eff + sum D c c^T`, `D = kk m a0 / (m a0 + kk)`, `kk = k + c_s a1`. As `k`
  tends to infinity this is the moving mass, as `m` tends to zero the moving force.

The DAF is the maximum dynamic displacement over the maximum static displacement of the
same train on the same beam (influence line of the model). The table also gives the ratio
to the first analysed speed, which is the original tool's definition.

## Ported and not ported

Ported: the three vehicle models, 2D and 3D schemes, the speed scan, the Word report
(rewritten; the original has no OMML equations, here formulas are indented italic text).
Not ported: the ballasted track with non-linear springs (rails, vertical and longitudinal
springs), the Streamlit pages, the AI server, the executable and the licence, as they are
outside the scope of a solver plugin.

## Differences from the original tool

* **Suspension sign.** In the original code the suspension force on the bridge has the
  opposite of the physical sign (the bridge sees a negative stiffness). The plugin uses the
  physical sign. With the sign fixed the original and the plugin agree within 0.4 % on
  displacements; with the original sign the gap reaches 2 % at 300 km/h.
* **Interpolation.** The original spreads VBI loads on nodes with linear interpolation and
  integrates the vehicle with explicit Euler; here Hermite shape functions and coupled
  Newmark.
* **Effective speed.** The original advances the axles by `Ltot / (n - 1)` per step with
  `n = int(Ltot / (v dt))`, slightly faster than `v` (over 1 %), and uses the position of
  the previous step. The plugin uses `v t`. `speed_eff_ratio` and `lag_steps` of
  `engine.run_transit` reproduce the original.
* **Rayleigh damping.** The original uses the first and third eigenvalue of the whole
  problem, which for a simply supported beam is the first axial mode (35 Hz), not the third
  bending mode (62.8 Hz). The default `modi_rayleigh="flessionali"` uses vertical bending
  modes; `"globali"` reproduces the original.
* **Range.** `v_max` is included.

## Verification

* slow single load: `P L^3 / (48 E I)` (static exact to 1e-5, 1 km/h transit within 0.2 %);
* undamped moving load against the modal series: under 1 % at 36 km/h and at 0.7, 0.9, 1.0,
  1.1, 1.4 times the critical speed `2 L f1`; the DAF curve follows the series point by
  point (maximum about 1.7 between 0.6 and 0.75 `v_cr`, about 1.57 at resonance);
* against feagent's `moving_load_dynamic_analysis`: 1e-9;
* against the original code re-run once (Python 3.10, OpenSees): moving-force time
  histories to 1e-9, peaks to 1e-5, 3D torsional rotation within 0.1 %;
* against the results stored in the original repository: displacements within 1.5 % at 10
  speeds (maximum 1.2 %, due to the quirks above), static 3.10779 mm and `f1` = 6.97559 Hz
  identical;
* limits: zero mass = moving force to 1e-9, rigid suspension = moving mass (1 % on the
  peak), symmetric 3D = 2D to 1e-8.

## Known limits

* The vehicle has vertical motion only; Coriolis and centripetal terms of the running mass
  are not included.
* Accelerations of the mass models depend on `dt`; the plugin warns when `dt` does not
  resolve the mass-bridge coupling frequency.
* Moving mass in 3D with an eccentric track: torsion is coupled to the mass and, with no
  rotational inertia (`massa_rotazionale = 0`) or a large `dt` (above about 0.0025 s), the
  solution can become numerically unstable. Give the deck a rotational inertia (about
  `m (Iy + Iz) / A`) and reduce `dt`. Moving force and oscillator are not affected.
* With an extreme suspension stiffness (above 1e12 N/m) conditioning degrades.
