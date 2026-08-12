---
layout: default
title: "23 - HDF5 Results Format"
parent: English
nav_order: 23
---

# 23 - The HDF5 Results Format

This page describes in detail the **HDF5 binary format** (`.h5`) used by
beamfeapy to store analysis results, how to inspect it, and how to
read it back from other tools (pure Python, MATLAB, Julia, C++).

For **practical usage** (saving/reading back from the library) see
[22 - Saving Results](en-22-saving-results.html). This page is
complementary and focuses on the **format itself**.

---

## 1. Why HDF5

**HDF5** (Hierarchical Data Format v5) is the de facto standard for "big data"
scientific data. It was chosen for structural results because:

- **Hierarchical**: it organizes data into groups and datasets like an internal
  filesystem (`/static`, `/modal`, `/buckling`).
- **Native compressed arrays**: mode shapes $(n_{dof} \times n_{modi})$ and the
  internal action diagrams $(n_{elem} \times n_{punti})$ are stored as
  binary arrays with gzip compression — much more efficient than Excel tables.
- **Metadata (attributes)**: each group carries its own information
  (load case, section group, number of modes).
- **Interoperable**: readable from Python, MATLAB, Julia, R, C/C++, Java without
  depending on beamfeapy.
- **Partial access**: you can read a single dataset without loading the entire
  file into memory (useful for archives of many analyses).

---

## 2. File structure

A file written by `io_hdf5.write_results(res, "demo.h5", n_diagram=11,
modal=modal, buckling=buck)` has this structure (real dump, 4-node /
3-element / 24-DOF model, 4 modes, 3 buckling modes):

```
/                                    [root]
├── attrs: format = "beamfeapy-results"
│          format_version = 1
│          created = "2026-06-02T09:32:35+00:00"
│          ndof = 24, n_nodes = 4, n_elements = 3
│
├── node_ids            (4,)      int64      [1, 2, 3, 4]
├── element_ids         (3,)      int64      [1, 2, 3]
│
├── static/                       attrs: cases='{"G": 1.0}', section_group=''
│   ├── U               (24,)     float64    spostamenti globali
│   ├── R               (24,)     float64    reazioni globali
│   ├── element_forces  (3, 12)   float64    attrs: columns="FxI,FyI,...,MzJ"
│   └── internal_forces/          attrs: n_diagram=11
│       ├── x           (3, 11)   float64    ascissa locale
│       ├── N           (3, 11)   float64    sforzo normale
│       ├── Vy          (3, 11)   float64
│       ├── Vz          (3, 11)   float64
│       ├── T           (3, 11)   float64
│       ├── My          (3, 11)   float64
│       └── Mz          (3, 11)   float64
│
├── modal/                        attrs: n_modes=4, section_group=''
│   ├── omega           (4,)      float64    pulsazioni [rad/s]
│   ├── freq            (4,)      float64    frequenze [Hz]
│   ├── period          (4,)      float64    periodi [s]
│   ├── phi             (24, 4)   float64    forme modali (ndof × n_modi)
│   ├── eff_mass        (4, 3)    float64    massa efficace per direzione X,Y,Z
│   ├── part            (4, 3)    float64    fattori di partecipazione
│   └── total_mass      (3,)      float64    massa totale traslazionale
│
└── buckling/                     attrs: cases='{"G": 1.0}', n_modes=3, section_group=''
    ├── load_factors    (3,)      float64    moltiplicatori critici λ
    └── phi             (24, 3)   float64    forme di instabilità (ndof × n_modi)
```

The three groups `/static`, `/modal`, `/buckling` are **independent**: a file can
contain one, two, or all three of them.

### Data conventions

| Element | Convention |
|----------|-------------|
| `U`, `R` | global vectors of length `ndof`, DOF order `[ux,uy,uz,rx,ry,rz]` per node |
| node order | `node_ids` sorted ascending; the global DOF of node $p$ is `[6p … 6p+5]` |
| `element_forces` | row = element (same order as `element_ids`), 12 local end forces |
| `phi` | column = mode, row = global DOF |
| `cases` | JSON string of the dict `{case: coefficient}` (empty = None) |

---

## 3. Inspecting an HDF5 file

### From the command line (HDF5 tools)

If you have installed the HDF5 tools (`apt install hdf5-tools` / `brew install hdf5`):

```bash
h5ls -r demo.h5            # elenco ricorsivo di gruppi e dataset
h5dump -a / demo.h5        # root attributes
h5dump -d /modal/freq demo.h5   # contenuto di un singolo dataset
```

### From Python with h5py (without beamfeapy)

```python
import h5py

with h5py.File("demo.h5", "r") as h5:
    # metadati globali
    print(dict(h5.attrs))                 # format, ndof, n_nodes, ...

    # naviga la gerarchia
    h5.visititems(lambda name, obj: print(name, obj))

    # leggi un dataset specifico
    freq = h5["/modal/freq"][()]          # array NumPy
    phi  = h5["/modal/phi"][()]           # (ndof, n_modi)

    # attributi di un gruppo
    cases = h5["/static"].attrs["cases"]  # '{"G": 1.0}'
```

### From beamfeapy (structured reading)

```python
from beamfeapy import io_hdf5
data = io_hdf5.read_results("demo.h5")    # dict di array
out  = io_hdf5.read_results("demo.h5", model=m)   # oggetti Result
```

---

## 4. Compression

All datasets use **gzip level 4** (`compression="gzip",
compression_opts=4`), a good compromise between compression ratio and speed.

For FEM data (many zeros in the mode shapes and diagrams) the gain is
significant. Example on a 3D building (288 DOF, 12 modes + 6 buckling + diagrams):

| Content | Size |
|-----------|-----------|
| Compressed HDF5 (gzip-4) | ~110 KB |
| Same content in Excel (static only) | ~99 KB |

> The HDF5 contains **much more data** than the Excel (modal + buckling + all mode
> shapes) at the same size, thanks to compression and binary storage.

---

## 5. Interoperability with other languages

The format is readable from any language with HDF5 support, **without
beamfeapy**. The dataset paths are stable.

### MATLAB

```matlab
% Leggi frequenze e forme modali
freq = h5read('demo.h5', '/modal/freq');
phi  = h5read('demo.h5', '/modal/phi');      % (n_modi × ndof in MATLAB, trasposto)
U    = h5read('demo.h5', '/static/U');

% Attributi
ndof = h5readatt('demo.h5', '/', 'ndof');
cases = h5readatt('demo.h5', '/static', 'cases');   % '{"G": 1.0}'
```

> **Note:** MATLAB reads HDF5 arrays in column-major order, so `phi`
> comes out transposed relative to NumPy: `(n_modi × ndof)`. Use `phi'` to realign.

### Julia

```julia
using HDF5

h5open("demo.h5", "r") do f
    freq = read(f, "modal/freq")
    phi  = read(f, "modal/phi")
    ndof = read_attribute(f, "ndof")
end
```

### Pure Python (NumPy + h5py)

See section 3. No dependency on beamfeapy: `h5py` and `numpy` are all you need.

---

## 6. Format versioning

The root attribute `format_version` (currently **1**) allows the format to
evolve while maintaining backward compatibility. A reader can check it:

```python
with h5py.File(path, "r") as h5:
    assert h5.attrs["format"] == "beamfeapy-results"
    if h5.attrs["format_version"] > 1:
        print("Attenzione: file scritto da una versione più recente.")
```

---

## 7. Useful patterns

### Saving multiple analyses in the same file

The `mode` parameter controls how the file is opened (`"w"` overwrites, `"a"`
appends/updates):

```python
res.to_hdf5("archivio.h5", mode="w")              # crea con la statica
modal.to_hdf5("archivio.h5", mode="a")            # aggiunge /modal
buck.to_hdf5("archivio.h5", mode="a")             # aggiunge /buckling
```

> Equivalent to `io_hdf5.write_results(res, "archivio.h5", modal=modal,
> buckling=buck)` in a single call.

### Reading only what you need (large files)

```python
import h5py
with h5py.File("archivio.h5", "r") as h5:
    # carica solo le frequenze, non le forme modali (potenzialmente enormi)
    freq = h5["/modal/freq"][()]
    # carica una sola colonna (un modo) di phi
    modo0 = h5["/modal/phi"][:, 0]
```

### Extracting the diagrams of an element

```python
data = io_hdf5.read_results("demo.h5")
ifc = data["static"]["internal_forces"]    # dict {comp: (n_elem, n_punti)}
# elemento con indice k (ordine = element_ids)
k = 1
x  = ifc["x"][k]
Mz = ifc["Mz"][k]
# plot x vs Mz ...
```

---

## 8. Limitations and notes

- HDF5 saves the **results**, not the model. To reconstruct the
  `Result` objects with their methods (`displacement`, `mode_shape`, …) you need to pass the
  model: `io_hdf5.read_results(path, model=m)`. Without the model you get only
  the raw arrays.
- The internal action **diagrams** (`/static/internal_forces`) are present only
  if the file was written with `n_diagram > 1`.
- The section group and the load cases are saved as metadata (attributes), not as
  a complete model.

---

*See also:*
[22 - Saving Results (Excel and HDF5)](en-22-saving-results.html) |
[11 - Excel I/O](en-11-excel-io.html) |
[19 - Modal Analysis](en-19-modal-analysis.html)
