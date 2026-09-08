---
layout: default
title: "19 - Modal Analysis"
parent: English
nav_order: 19
---

# 19 - Modal Analysis and Combinations with Coefficients

## Combinations with multiplicative coefficients

`Model.solve` accepts a **dictionary `{case: coefficient}`** as `cases` to
combine load patterns with factors (e.g. ULS per NTC/Eurocode):

```python
res = m.solve(cases={"G": 1.35, "Q": 1.5})        # 1.35·G + 1.5·Q
res = m.solve(cases={"G": 1.0, "Q": 0.3, "N": 1.0})  # any combination
```

Displacements, reactions and **internal forces** respect the coefficients
(linearity verified). `cases` remains usable as string or list (coeff 1).

## Masses from loads

The user decides **which load cases to convert to mass** and with what
coefficient, via a *mass source* `{case: coefficient}`. Mass is derived from
forces: `mass = coeff · |force| / g`, assigned to the 3 translational DOFs of
each node (lumped mass). Both **distributed loads** (distributed to nodes via
equivalent nodal forces) and **concentrated loads** (nodal and in-span) are
converted to mass. Include gravitational load cases in the source.

```python
M = m.assemble_mass({"G": 1.0, "Q": 0.3})   # mass vector (diagonal)
```

## Modal analysis

```python
mr = m.modal(n_modes=6, mass_source={"G": 1.0, "Q": 0.3}, g=9.81)

for i in range(len(mr.freq)):
    print(mr.freq[i], "Hz", mr.period[i], "s")
mp = mr.mass_participation()     # participating mass ratios (n_modes x 3: X,Y,Z)
```

`modal()` solves `K φ = ω² M φ` on free DOFs; free DOFs **without mass**
(rotational and unloaded translational) are eliminated by **static
condensation**, avoiding spurious modes. The result is a `ModalResult` with
`omega`, `freq` [Hz], `period` [s], `phi` (mass-normalized mode shapes),
`eff_mass` and `mass_participation()` per direction.

> Cross-validation: frequencies match an independent external FEM solver to machine
> precision (see `validation/validate_modal_ext.py`).

### Illustrated example (2-storey plane frame)

Masses from gravitational loads on beams; first three modes (out-of-plane DOFs
restrained for 2D analysis):

| Loads (mass source) | Mode 1 — sway | 
|---|---|
| ![](images/modal_loads.png) | ![](images/modal_mode1.png) |

| Mode 2 | Mode 3 |
|---|---|
| ![](images/modal_mode2.png) | ![](images/modal_mode3.png) |

Visualizing mode shapes:

```python
from feagent.plotting import plot_mode
plot_mode(mr, 0).show()     # 1st mode (index 0); amplitude auto-scaled
```

## Linear time-history (Newmark and modal superposition)

Step-by-step integration of the equation of motion `M ü + C u̇ + K u = F(t)`
(Newmark 1959, average acceleration; Chopra, *Dynamics of Structures*,
5th ed.):

```python
from feagent import RayleighDamping
damp = RayleighDamping.from_frequencies(1.0, 0.05, 8.0, 0.05)

# nodal forcing: functions f(t) or histories sampled at dt
res = m.solve_time_history({(5, "uy"): lambda t: 1e3*np.sin(20*t)},
                           dt=0.005, t_end=10.0,
                           mass_source={"G": 1.0}, damping=damp)

# base accelerogram: effective load -M·ι·üg(t), relative displacements
res = m.solve_time_history(("x", ug), dt=0.005, t_end=20.0,
                           mass_source={"G": 1.0}, damping=damp)

res.displacement_history(5, "uy")          # u(t), v(t), a(t) histories
res.max_displacement(5, "uy")              # extremes
res.reaction_history(1, "uy")              # reactions
res.element_end_force_history(3)           # element force histories
res.absolute_acceleration_history(5, "ux") # absolute acceleration (seismic)
```

The **modal variant** integrates the uncoupled modal equations with the
piecewise-exact solution (linear interpolation of the excitation, Chopra
§5.2 — exact and stable for any dt) or with per-mode Newmark:

```python
res = m.solve_time_history_modal(exc, dt=0.005, t_end=10.0, n_modes=12,
                                 mass_source={"G": 1.0}, damping=0.05)
```

`damping` accepts a scalar ξ ratio, a per-mode array or a `DampingModel`
projected onto the modes. With classical (Rayleigh) damping and all modes,
the modal variant with `method="newmark"` matches direct integration to
machine precision.

### Consistent mass matrix

Modal analysis (and time-history) accept `mass="consistent"`: instead of the
diagonal lumped mass (default `"lumped"`), the consistent mass matrix
(Przemieniecki 1968) is used, which includes beam rotational inertia and
converges to the continuum frequencies much faster for a given mesh.

```python
mc = m.modal(n_modes=6, mass_source={"G": 1.0}, mass="consistent")
```

Shell self-mass (ρ·t plus any surcharge) is also included automatically in
modal and dynamic analysis, as for beams and trusses.

### Kinematic constraints in P-Delta and dynamics

Kinematic constraints (rigid links, equalDOF, diaphragms, inclined supports)
compose with the second-order `solve_pdelta` and with time-history (direct
and modal) as well: the master–slave transformation `u = W u_r` reduces `K`,
`M` and the geometric stiffness `K_g`, and full displacements are
reconstructed with `u = W u_r`. Only dynamic moving loads
(`moving_load_dynamic_analysis`) remain excluded.

## Steady-state harmonic response

`solve_harmonic` solves the steady-state response `(K - Om^2 M + i Om C) U = F`
at one or more forcing frequencies (transfer function):

```python
Om = np.linspace(0.1, 3.0, 200) * omega0
hr = m.solve_harmonic({(5, "uy"): 1e3}, Om, mass_source={"G": 1.0},
                      damping=damp)
hr.amplitude(5, "uy")   # |U(Om)|   (steady-state amplitude)
hr.phase(5, "uy")       # arg U(Om) (phase lag)
hr.peak(5, "uy")        # (Om, amplitude) of the resonance peak
```

The forcing may be `{(node, dof): amplitude}` (complex amplitude for phase) or
`(direction, a0)` for a harmonic base acceleration. It reuses the massless-DOF
condensation, kinematic constraints and `mass="consistent"`.
