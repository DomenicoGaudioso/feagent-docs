---
layout: default
title: "24 - External Software Export"
parent: English
nav_order: 24
---

# 24 - Export to external analysis software

beamfeapy can **export the model** to the main structural analysis
software packages, generating importable text files.

| Software | Funzione | Estensione | Stato |
|----------|----------|-----------|-------|
| **OpenSees** (TCL) | `to_opensees_tcl` | `.tcl` | complete |
| **OpenSeesPy** (Python) | `to_openseespy` | `.py` | complete |
| **SAP2000** | `to_sap2000_s2k` | `.s2k` | complete |
| **MIDAS** Civil/Gen | `to_midas_mct` | `.mct` | complete |
| **Robot** (RSA) | `to_robot_str` | `.str` | best-effort* |
| **Straus7** / Strand7 | `to_straus7_txt` | `.txt` | best-effort* |

> *Robot and Straus7: the structure follows the documented text format, but some
> keywords may vary with version/language. See §6.

---

## 1. Quick start

From the model (format inferred from the extension or explicit):

```python
m.export("modello.tcl")                    # OpenSees TCL (da estensione)
m.export("modello.py",  fmt="openseespy")
m.export("modello.s2k", fmt="sap2000")
m.export("modello.mct", fmt="midas")
m.export("modello.str", fmt="robot")
m.export("modello.txt", fmt="straus7")
```

From the `export` module:

```python
from beamfeapy import export

export.export(m, "modello.tcl", fmt="opensees_tcl")
export.to_openseespy(m, "modello.py")
export.to_sap2000_s2k(m, "modello.s2k")
```

### What gets exported

| Entità | OpenSees | SAP2000 | MIDAS | Robot | Straus7 |
|--------|:--------:|:-------:|:-----:|:-----:|:-------:|
| Nodes | ✅ | ✅ | ✅ | ✅ | ✅ |
| Materials | ✅ (inline) | ✅ | ✅ | ✅ | ✅ |
| Sections | ✅ (inline) | ✅ General | ✅ Value | ✅ | ✅ |
| Elements (local axes) | ✅ | ✅ | ✅ | ✅ | ✅ |
| Constraints | ✅ | ✅ | ✅ | ✅ | ✅ |
| Imposed settlements | — | — | — | — | ✅ |
| Releases (hinges) | note | ✅ | — | ✅ | ✅ |
| Nodal loads | ✅ | ✅ | ✅ | ✅ | ✅ |
| Distributed loads | ✅** | ✅ | ✅ | ✅ | ✅ |

> **OpenSees `eleLoad -beamUniform` is uniform: trapezoidal/partial loads
> are exported with the equivalent average value (with a note in the file).

---

## 2. OpenSees (TCL and OpenSeesPy)

The element used is **`elasticBeamColumn`**, a 3D Euler-Bernoulli beam with 12 DOF
— an **exact** match with beamfeapy.

### Generated TCL example

```tcl
wipe
model basic -ndm 3 -ndf 6

# --- nodi ---
node 1 0 0 0
node 2 0 4 0
...
# --- trasformazioni geometriche (vecxz = local_z) ---
geomTransf Linear 1 0 0 -1
# --- elementi: elasticBeamColumn eid ni nj A E G J Iy Iz transfTag ---
element elasticBeamColumn 1 1 2 0.012 2.1e+11 8.08e+10 0.0002 4e-05 8e-05 1
# --- carichi: case 'G' ---
timeSeries Linear 1
pattern Plain 1 1 {
    eleLoad -ele 2 -type -beamUniform -8000 0 0
}
```

### Local axes: exact match

The delicate point is the orientation of the section. For each element beamfeapy
exports the unit vector **`local_z` (ez)** as the `vecxz` vector of the `geomTransf`.
In OpenSees, `local_y = vecxz × local_x` holds; since in beamfeapy the system is
right-handed (`ez = ex × ey`, hence `ez × ex = ey`), the transformation reproduces
**exactly** the same local axes — whatever orientation is set
(`ref_vector`, `set_axes`, default). This is verified by the tests.

```python
m.export("modello.tcl")                # per OpenSees Tcl
m.export("modello.py", fmt="openseespy")  # per Python

# tipo di trasformazione geometrica
m.export("modello.tcl", transf="PDelta")        # o "Corotational"
```

The generated **OpenSeesPy** script is executable Python (it includes `system`,
`numberer`, `analysis` and `analyze(1)` for a linear static analysis).

---

## 3. SAP2000 (.s2k)

Table-based text file (`TABLE: "..."`), importable via
**File > Import > SAP2000 .s2k Text File**.

```
TABLE:  "JOINT COORDINATES"
   Joint=1   CoordSys="GLOBAL"   CoordType="Cartesian"   XorR="0"   Y="0"   Z="0"

TABLE:  "FRAME SECTION PROPERTIES 01 - GENERAL"
   SectionName="SEC1"   Material="MAT1"   Shape="General"
   Area="0.012"   TorsConst="0.0002"   I33="8e-05"   I22="4e-05"

TABLE:  "CONNECTIVITY - FRAME"
   Frame=1   JointI=1   JointJ=2
```

Sections are exported as **General** (direct numeric properties: area,
J, I22=Iy, I33=Iz). The orientation uses the local axis 2 angle.

---

## 4. MIDAS Civil/Gen (.mct)

A **MIDAS Command Text** command file, importable from the *MCT Command Shell*.

```
*NODE    ; iNO, X, Y, Z
   1, 0, 0, 0
   2, 0, 4, 0

*MATERIAL    ; iMAT, TYPE, MNAME, ...
   1, ELAST, MAT1, ..., 2.1e+11, 0.3, 0, 0

*SECTION    ; iSEC, TYPE, SNAME, ..., Area, Asy, Asz, Ixx, Iyy, Izz
   1, VALUE, SEC1, ..., 0.012, 0, 0, 0.0002, 4e-05, 8e-05

*ELEMENT    ; iEL, TYPE, iMAT, iPRO, iN1, iN2, ANGLE
   1, BEAM, 1, 1, 1, 2, 180
```

Sections use the **VALUE** type (numeric properties). The beta angle orients
the section about the member axis.

---

## 5. Exporting with all entities

The exporter includes nodes, materials, sections, elements (with local axes),
constraints, releases, nodal and distributed loads, organized by load case:

```python
m = Model()
# ... geometria, sezioni, vincoli ...
m.add_distributed_load(2, "fy", -8000., case="G")
m.add_nodal_load(2, Fx=5000., case="SX")
m.add_beam(3, 3, 4, mat, sec, releases_j=["rz"])   # cerniera

m.export("modello.s2k", fmt="sap2000")   # esporta tutto, per load case
```

The load cases become:
- OpenSees: distinct `pattern Plain` per case
- SAP2000: `LOAD PATTERN DEFINITIONS` + `JOINT/FRAME LOADS`
- MIDAS: `*STLDCASE` + `*CONLOAD`/`*BEAMLOAD`

---

## 6. Robot and Straus7 (best-effort)

Robot (`.STR`) and Straus7 (text) **do have** a textual import format, but the
exact syntax is described in proprietary documents distributed with the programs
(`R97mod01.doc` for Robot; the Strand7 reference). The exporters produce a
structure consistent with the documented format and include **all the data** of
the model, but they must be **validated against a real example file**.

```python
m.export("modello.str", fmt="robot")      # Robot RSA
m.export("modello.txt", fmt="straus7")    # Straus7 / Strand7
```

**To refine the mapping:** export a small model (2-3 members) from your
Robot (`.STR`) / Straus7 (Strand7 Text File) installation and compare the block
and field names — small adjustments are enough to align them 100%.

---

## 7. Known limitations

- **Thermal loads** and **prestressing** are not yet exported
  (geometry + mechanical loads are).
- Generic **thermal profiles** (`ThermalProfile`) are ignored.
- **Trapezoidal/partial** distributed loads in OpenSees are approximated
  with the uniform average value (the other formats support them natively).
- Identical materials and sections are automatically **deduplicated**.

---

*See also:*
[08 - Section Orientation](en-08-section-orientation.html) |
[15 - Testing and Validation](en-15-testing-validation.html) |
[11 - Excel I/O](en-11-excel-io.html)
