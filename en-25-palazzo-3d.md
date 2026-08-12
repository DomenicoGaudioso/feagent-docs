---
layout: default
title: "25 - Palazzo 3D Case Study"
parent: "14 - Usage Examples"
grand_parent: English
nav_order: 2
---

# 25 - Case study: 3D Building

Complete analysis of a **multi-storey steel building** with space frames in both
horizontal directions.  
This is the most elaborate example in the library: it combines 3D geometry, multi-case
static analysis, modal analysis, linear buckling analysis and 3D visualisation of
deformed shapes, internal force diagrams and local axes.

---

## Geometry and materials

| Parametro | Valore |
|-----------|--------|
| No. of storeys | 3 |
| X bays | 3 (5 m spacing) |
| Z bays | 2 (4 m spacing) |
| Storey height | 3.5 m |
| Material | Steel S275 — E = 210 GPa, ν = 0.3 |
| Columns | HEA 240 |
| X beams | IPE 300 |
| Z beams | IPE 240 |
| Total nodes | 48 |
| Elements | 87 (36 col + 27 bx + 24 bz) |
| Total DOF | 288 |

The visualised 3D structure:

![3D structure — steel building](images/palazzo_struttura.png)

Colours: **dark grey** = HEA 240 columns · **blue** = IPE 300 X beams · **red** = IPE 240 Z beams.

---

## Building the model

```python
from beamfeapy import Material, Model, Section

NX, NZ, NF = 4, 3, 3         # nodi in X, Z; n. piani
LX, LZ, H  = 5.0, 4.0, 3.5   # interassi [m]; altezza piano [m]

mat = Material(210e9, 0.3)

# Convenzione SAP2000: Iz = asse forte, Iy = asse debole
sec_col = Section(A=76.8e-4, Iy=2769e-8, Iz=7763e-8, J=41.5e-8)  # HEA 240
sec_bx  = Section(A=53.8e-4, Iy=604e-8,  Iz=8356e-8, J=20.1e-8)  # IPE 300
sec_bz  = Section(A=39.1e-4, Iy=284e-8,  Iz=3892e-8, J=12.9e-8)  # IPE 240

m = Model()

def nid(f, ix, iz):
    return f * NX * NZ + iz * NX + ix + 1

# Nodi: X globale = longitudinale, Y = verticale, Z = trasversale
for f in range(NF + 1):
    for iz in range(NZ):
        for ix in range(NX):
            m.add_node(nid(f, ix, iz), ix*LX, f*H, iz*LZ)

# Colonne verticali: ref=(1,0,0) → local_y = X globale
for f in range(NF):
    for iz in range(NZ):
        for ix in range(NX):
            m.add_beam(eid, nid(f,ix,iz), nid(f+1,ix,iz),
                       mat, sec_col, ref_vector=(1.,0.,0.))

# Travi orizzontali: default ref → local_y = Y globale per X e Z
for f in range(1, NF+1):
    for iz in range(NZ):
        for ix in range(NX-1):
            m.add_beam(eid, nid(f,ix,iz), nid(f,ix+1,iz), mat, sec_bx)
    for ix in range(NX):
        for iz in range(NZ-1):
            m.add_beam(eid, nid(f,ix,iz), nid(f,ix,iz+1), mat, sec_bz)

# Incastri alla base
for iz in range(NZ):
    for ix in range(NX):
        m.fix(nid(0, ix, iz))
```

### Element local axes

The `plot_local_axes()` function allows you to visually verify the section
orientation before the analysis.

```python
from beamfeapy import plot_local_axes
fig = plot_local_axes(m, scale=0.65)
fig.show()
```

![Local axes triads — storey 1](images/palazzo_assi_locali.png)

**Colours:** red = `local_x` (beam axis) · green = `local_y` (strong axis / gravity) · blue = `local_z`.

For the horizontal beams the green points **upward** (global Y), confirming
that `'fy'` is the gravitational load.

---

## Loads

```python
# Gravitazionali: 'fy' in locale = Y globale (con default ref → local_y = Y)
for e in bx_eids:
    m.add_distributed_load(e, 'fy', -20_000., case='G')   # 20 kN/m su travi X
for e in bz_eids:
    m.add_distributed_load(e, 'fy', -15_000., case='G')   # 15 kN/m su travi Z

# Sismici: forze nodali con distribuzione triangolare sull'altezza (V = 400 kN)
heights = [H*(f+1) for f in range(NF)]
for f in range(NF):
    Fnode = 400e3 * heights[f] / sum(heights) / (NX * NZ)
    for iz in range(NZ):
        for ix in range(NX):
            m.add_nodal_load(nid(f+1,ix,iz), Fx=Fnode, case='SX')
            m.add_nodal_load(nid(f+1,ix,iz), Fz=Fnode, case='SZ')
```

Load cases:

| Caso | Descrizione |
|------|-------------|
| `G` | Permanent gravitational |
| `SX` | Earthquake in X direction |
| `SZ` | Earthquake in Z direction |
| `G + 0.3·SX + 0.3·SZ` | Eurocode-like combination |

---

## Static analysis

```python
res_G    = m.solve(cases='G')
res_SX   = m.solve(cases='SX')
res_SZ   = m.solve(cases='SZ')
res_comb = m.solve(cases={'G': 1.0, 'SX': 0.3, 'SZ': 0.3})
```

### Deformed shapes in the 4 cases (amplified scale)

![Static deformed shapes in the 4 load cases](images/palazzo_deformate.png)

| Caso | Spostamento max al 3° piano |
|------|-----------------------------|
| G | ux ≈ 0.09 mm, uz ≈ 0.03 mm — almost zero (columns very stiff axially) |
| **SX** | **ux = 38.6 mm** — longitudinal sway |
| **SZ** | **uz = 91.8 mm** — transverse sway (**2.4× more flexible** in Z) |
| Combo | Oblique combination of the above |

> The building is **2.4× more flexible in Z** (2 bays) than in X (3 bays).
> The transverse earthquake governs the design.

### Maximum internal forces per combination

![Maximum internal force table](images/palazzo_tabella_sollecitazioni.png)

| Caso | N max [kN] | Mz max [kNm] | My max [kNm] | Vy max [kN] | Vz max [kN] |
|------|-----------|--------------|--------------|-------------|-------------|
| **G** | **501** | 10.8 | 45.6 | 5.5 | 53.6 |
| SX | 56 | ≈ 0 | **77.5** | ≈ 0 | 37.6 |
| SZ | 49 | **84.2** | ≈ 0 | 37.1 | ≈ 0 |
| Combo | 511 | 37.5 | 60.3 | 16.2 | 70.4 |

> The seismic moment **My_SX = 77.5 kNm** exceeds the gravitational moment
> **My_G = 45.6 kNm**: the earthquake governs the column check.

### Internal force diagrams — case G

![N, Mz, My, Vy diagrams for the gravitational case](images/palazzo_sollecitazioni_G.png)

| Diagramma | Osservazione |
|-----------|-------------|
| **N** (top left) | Compression increasing toward the base; corner columns less loaded |
| **Mz** (top right) | Small moments from beam–column interaction |
| **My** (bottom left) | Large bending in the X beams (5 m span, 20 kN/m); `Iz` governs |
| **Vy** (bottom right) | Shear proportional to the My gradient |

### Seismic bending moments — SX vs SZ

![Comparison of Mz and My under earthquake X and Z](images/palazzo_sollecitazioni_sismiche.png)

- **SX — Mz (top left)**: earthquake X activates bending in Z (almost zero for the columns oriented with strong axis in X)
- **SX — My (bottom left)**: earthquake X generates large My on the columns (sway in the X-Y plane)
- **SZ — Mz (top right)**: earthquake Z generates large Mz (sway in the Z-Y plane)
- **SZ — My (bottom right)**: My almost zero for SZ (orthogonal to the Z-Y plane)

The rotational symmetry of the two maps confirms the correct orientation of the sections.

---

## Modal analysis

```python
modal = m.modal(n_modes=12, mass_source={'G': 1.0}, g=9.81)
mp    = modal.mass_participation()
```

### Modal participation table (12 modes)

![Complete modal table — 12 modes with participation](images/palazzo_tabella_modale.png)

| Modo | f [Hz] | T [s] | Dir | Mx [%] | My [%] | Mz [%] | ΣMx | ΣMz |
|------|--------|-------|-----|--------|--------|--------|-----|-----|
| **1** | 0.596 | 1.678 | **Z (81%)** | 0 | 0 | **81** | 0 | 81 |
| 2 | 0.696 | 1.438 | Tors | 0 | 0 | 0 | 0 | 81 |
| 3 | 0.834 | 1.200 | Z (6%) | 0 | 0 | 6 | 0 | 87 |
| **4** | 0.940 | 1.064 | **X (83%)** | **83** | 0 | 0 | 83 | 87 |
| 5–6 | — | — | — | 83 | 0 | 87 | … | … |
| 7 | 1.199 | 0.834 | X (3%) | 86 | 0 | 87 | … | … |
| 8 | 1.761 | 0.568 | Z (7%) | 86 | 0 | **94** | … | … |
| 12 | 2.868 | 0.349 | X (6%) | **92** | 0 | 98 | ← | ← |

**Reading:**

- **Mode 1** (T = 1.68 s, Z 81%) — dominant transverse sway.
  With 81% of mass in the first mode the structure behaves almost like a single
  oscillator in Z. It is the main seismic mode in Z.
- **Mode 2** — torsional (translational participation ~0%).
  Present because the structure is asymmetric (NX ≠ NZ).
- **Mode 4** (T = 1.06 s, X 83%) — longitudinal sway.
  T₄ < T₁ because the structure is stiffer in X (3 bays versus 2).
- With 12 modes you cover: **SMx ≈ 92%, SMz ≈ 98%** → sufficient for seismic analysis.

### Mode shapes (first 6)

```python
from beamfeapy.plotting import plot_mode
fig = plot_mode(modal, mode=0)   # 1° modo
fig.show()
```

![First 6 mode shapes with frequency and participation](images/palazzo_modi_modali.png)

The shapes clearly show:
- **Mode 1** (purple, T=1.68s): global sway in Z — the entire structure moves together
- **Mode 2** (blue, T=1.44s): overall rotation (torsional)
- **Mode 4** (orange, T=1.06s): global sway in X
- **Modes 3,5,6**: storey modes (relative distortion between adjacent storeys)

---

## Linear buckling analysis

```python
buck = m.buckling(n_modes=6, cases='G')
# load_factors = dimensionless multipliers of load G:
# the building is unstable if load G is amplified by lambda_cr
```

### Critical load factors

| Modo | λ_cr | Direzione | Interpretazione |
|------|------|-----------|-----------------|
| **1** | **9.32 × G** | Z | Out-of-plane sidesway — **most critical** |
| 2 | 12.22 × G | Z | Z sway at 2nd storey |
| 3 | 12.58 × G | Z | Combined Z sway |
| 4 | 13.80 × G | Z | Z sway 3rd storey |
| 5 | 16.67 × G | Z | Local beam mode |
| 6 | 19.69 × G | Z | Higher local mode |

> **λ_cr = 9.32** means the structure withstands **9.3× the design load**
> before buckling: an excellent safety margin.
>
> All 6 modes are in **direction Z** because it is the direction with fewer bays
> (2 vs 3) and therefore less stiff against lateral sidesway.

### Buckling shapes (6 modes)

```python
from beamfeapy.plotting import plot_buckling_mode
fig = plot_buckling_mode(buck, mode=0)
fig.show()
```

![6 buckling shapes with lambda_cr](images/palazzo_modi_buckling.png)

The progression of the modes shows:
- **Mode 1**: sidesway of the entire building in Z — global instability
- **Modes 2–4**: combinations of storeys swaying at different phases
- **Modes 5–6**: local instability of individual elements (long beams)

---

## Local axes convention and sections

The library uses the **SAP2000 / Przemieniecki convention**:

```
local_x = beam axis (from node_i a node_j)
local_y = proiezione di ref_vector ⊥ local_x   ← definisce il piano x-y
local_z = local_x × local_y                      ← sistema destrorso
```

| Elemento | `ref_vector` | `local_y` | Carico gravità | Inerzia forte |
|----------|-------------|-----------|---------------|--------------|
| X or Z beam (default) | `None` → `(0,1,0)` | global Y ↑ | `'fy'` | `Iz` |
| Vertical column | `(1,0,0)` | global X → | — | `Iz` (X sway) |

Sections (Iz = strong, Iy = weak):

```python
# IPE 300 — trave orizzontale
sec_bx = Section(A=53.8e-4, Iy=604e-8,  Iz=8356e-8, J=20.1e-8)
#                             ^debole     ^forte

# HEA 240 — colonna con ref=(1,0,0), forte in sway X
sec_col = Section(A=76.8e-4, Iy=2769e-8, Iz=7763e-8, J=41.5e-8)
#                             ^sway Z     ^sway X
```

To modify the orientation of an element after construction:

```python
# Via modello
m.set_element_axes(eid, ey=(0, 1, 0))   # imposta local_y
m.set_element_axes(eid, ez=(0, 0, 1))   # imposta local_z
m.set_element_axes(eid, R=matrice_3x3)  # matrice completa

# Via elemento
el.set_axes(ey=(0, 1, 0))
el.reset_axes()   # ripristina default
```

---

## Reference files

| File | Contenuto |
|------|-----------|
| `_palazzo_3d.py` | Base model + modal + buckling |
| `_palazzo_verifiche.py` | Complete analysis G + SX + SZ + combo + 6 plots |

```bash
python _palazzo_3d.py
python _palazzo_verifiche.py
# Output: demo_plots/
```

---

*See also:*
[08 - Section Orientation](en-08-section-orientation.html) |
[19 - Modal Analysis](en-19-modal-analysis.html) |
[16 - Examples Gallery](en-16-case-studies-gallery.html)
