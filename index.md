---
layout: default
title: Home
nav_order: 1
description: "beamfeapy — Python FEM solver for 3D frame structures"
permalink: /
---

# beamfeapy

<div align="center">
  <img src="img/Design%206.png" alt="beamfeapy Logo" width="216">
</div>

A Python finite-element solver for the static, modal and buckling analysis of
**3D frame structures** (Euler-Bernoulli & Timoshenko beams) — tapered elements,
end releases, thermal loads, prestress, settlements, load cases, Excel I/O,
Plotly visualization and a Streamlit web UI.

---

## 📖 Documentation / Documentazione

The documentation is available in two languages with the same set of topics.
La documentazione è disponibile in due lingue con lo stesso insieme di argomenti.

| | |
|---|---|
| 🇬🇧 **[English documentation](en.html)** | Full guide: installation, modeling, loads, analyses, post-processing, case studies and Web UI. |
| 🇮🇹 **[Documentazione in italiano](it.html)** | Guida completa: installazione, modellazione, carichi, analisi, post-processing, casi studio e interfaccia web. |

Use the **language sections in the sidebar** (English / Italiano) to browse all
chapters. Use la **barra laterale** per sfogliare tutti i capitoli.

---

## Quick start

```bash
# install (with plotting + Excel extras)
pip install "beamfeapy[all]"

# or run the web UI
pip install beamfeapy streamlit plotly openpyxl
streamlit run app.py
```

```python
from beamfeapy import Model, Material, Section

m = Model()
m.add_node(1, 0, 0, 0); m.add_node(2, 5, 0, 0)
sec = m.add_section("S", A=0.01, Iy=8e-5, Iz=8e-5, J=1e-6)
m.add_beam(1, 1, 2, Material(2.1e11), sec)
m.fix(1)
m.add_nodal_load(2, case="G", Fy=-10_000)

res = m.solve(cases="G")
print(res.displacements(2))
```

→ Continue with the [English Quick Start](en-02-quick-start.html) or the
[Quick Start in italiano](it-02-quick-start.html).

---

## Key features

- **3D Euler-Bernoulli & Timoshenko beams** (12 DOFs), tapered elements, end releases
- **Loads**: nodal, in-span concentrated, distributed (uniform/partial/trapezoidal), thermal profiles, prestress, settlements
- **Load cases & combinations** with multiplicative coefficients
- **Modal** and **buckling** analysis (validated vs OpenSees)
- **Excel I/O**, **HDF5** results, **export** to OpenSees / SAP2000 / MIDAS / Robot / Straus7
- **Plotly** 3D plots and a **Streamlit web UI** (`app.py`)

## License

MIT — see `LICENSE`. Built by Domenico Gaudioso.
