---
layout: default
title: "22 - Saving Results"
parent: English
nav_order: 22
---

# 22 - Saving Results (Excel and HDF5)

beamfeapy offers **two formats** for saving and reading back analysis results:

| Formato | Estensione | Uso tipico | Dipendenza |
|---------|-----------|------------|------------|
| **Excel** | `.xlsx` | Tabular reports, sharing, manual inspection | `pandas`, `openpyxl` |
| **HDF5** | `.h5` | Big data: many analyses, mode shapes and diagrams, fast access | `h5py` |

```bash
pip install beamfeapy[excel]   # per Excel
pip install beamfeapy[hdf5]    # per HDF5
pip install beamfeapy[all]     # entrambi + plot
```

Excel is ideal for **static results** in tabular form; HDF5 (a compressed
hierarchical format, WOBridge style) is designed to store static, modal and
buckling results in **a single file**, including the large mode-shape matrices
(`ndof × n_modi`) and the internal-action diagrams for each element.

---

## 1. Excel (.xlsx)

### Saving static results

```python
res = m.solve(cases="G")
res.to_excel("risultati.xlsx", n_diagram=21)
```

Generated sheets:

| Foglio | Contenuto |
|--------|-----------|
| `Displacements` | nodal displacements `[ux,uy,uz,rx,ry,rz]` |
| `Reactions` | support reactions `[Fx,Fy,Fz,Mx,My,Mz]` |
| `ElementEndForces` | local end forces (12 per element) |
| `InternalForces` | internal-action diagrams (if `n_diagram > 1`) |

### Reading back static results

```python
from beamfeapy import read_results_excel

data = read_results_excel("risultati.xlsx")
data["displacements"][3]      # array(6,) displacements node 3
data["reactions"][1]          # array(6,) reactions node 1
data["element_forces"][2]     # array(12,) forze elemento 2
data["internal_forces"]       # DataFrame (se presente il foglio)
```

> Excel saves **only the static results**. For modal and buckling analyses use HDF5.

---

## 2. HDF5 (.h5) — big data format

### Saving (everything in one file)

```python
res   = m.solve(cases="G")
modal = m.modal(n_modes=12, mass_source={"G": 1.0})
buck  = m.buckling(n_modes=6, cases="G")

# Metodo 1: dal Result, includendo modale e buckling
res.to_hdf5("out.h5", n_diagram=21, modal=modal, buckling=buck)

# Metodo 2: dal modulo
from beamfeapy import io_hdf5
io_hdf5.write_results(res, "out.h5", n_diagram=21,
                      modal=modal, buckling=buck)
```

Individual results also have their own `to_hdf5`:

```python
modal.to_hdf5("solo_modale.h5")
buck.to_hdf5("solo_buckling.h5")
```

### Reading back

```python
from beamfeapy import io_hdf5

# (a) array grezzi — non serve il modello
data = io_hdf5.read_results("out.h5")
data["modal"]["freq"]               # frequenze
data["static"]["U"]                 # spostamenti globali
data["static"]["internal_forces"]["Mz"]   # (n_elem, n_diagram)
data["buckling"]["load_factors"]

# (b) oggetti Result collegati al modello (con i metodi soliti)
out = io_hdf5.read_results("out.h5", model=m)
res2   = out["static"]      # Result
modal2 = out["modal"]       # ModalResult
buck2  = out["buckling"]    # BucklingResult

res2.displacement(13, "uy")
modal2.mass_participation()
buck2.mode_shape(0, node=13)
```

The function is also exposed as `beamfeapy.read_results_hdf5`.

### HDF5 file structure

```
/                       attrs: format, format_version, ndof, n_nodes, n_elements
  node_ids              (n_nodes,)
  element_ids           (n_elements,)

  /static               attrs: cases, section_group
    U                   (ndof,)            spostamenti globali
    R                   (ndof,)            reazioni globali
    element_forces      (n_elem, 12)       forze d'estremità locali
    /internal_forces                        [se n_diagram > 1]
      x, N, Vy, Vz, T, My, Mz   (n_elem, n_diagram)

  /modal                attrs: section_group, n_modes
    omega, freq, period (n_modi,)
    phi                 (ndof, n_modi)     forme modali
    eff_mass, part      (n_modi, 3)
    total_mass          (3,)

  /buckling             attrs: cases, section_group, n_modes
    load_factors        (n_modi,)
    phi                 (ndof, n_modi)     forme di instabilità
```

The `/static`, `/modal` and `/buckling` groups are **independent**: you can save
them together or separately. All datasets are compressed with gzip.

---

## 3. Excel vs HDF5: when to use which

| Criterio | Excel | HDF5 |
|----------|-------|------|
| Manual inspection | ✅ open with Excel/LibreOffice | ❌ binary format |
| Modal + buckling | ❌ static only | ✅ all in one file |
| Mode shapes (ndof × n_modi) | ❌ heavy/slow | ✅ compressed native arrays |
| Large models (thousands of DOFs) | ⚠️ slow | ✅ fast, compressed |
| Interoperability with other languages | medium | ✅ HDF5 standard (MATLAB, C++, Julia) |
| Dependency | pandas + openpyxl | h5py |

**Rule of thumb:** Excel for reports and static results to be shared; HDF5
to archive the entire analysis (static + modal + buckling) or for large models.

---

## 4. Complete example: 3D building

```python
from beamfeapy import Material, Model, Section, io_hdf5

# ... costruzione del palazzo 3D (vedi pagina 20) ...

res   = m.solve(cases="G")
modal = m.modal(n_modes=12, mass_source={"G": 1.0})
buck  = m.buckling(n_modes=6, cases="G")

# Salva tutto in un file HDF5 (statica + 12 modi + 6 modi buckling + diagrammi)
io_hdf5.write_results(res, "palazzo.h5", n_diagram=21,
                      modal=modal, buckling=buck)

# Rilettura completa
out = io_hdf5.read_results("palazzo.h5", model=m)
print("f1 =", out["modal"].freq[0], "Hz")
print("lambda_cr =", out["buckling"].load_factors[0])
```

For a building with 48 nodes / 87 elements / 288 DOFs, the HDF5 file with static
+ 12 modes + 6 buckling modes + 21-point diagrams weighs about **110 KB**.

---

*See also:*
[11 - Excel I/O](en-11-excel-io.html) |
[19 - Modal Analysis](en-19-modal-analysis.html) |
[25 - 3D Building](en-25-palazzo-3d.html)
