---
layout: default
title: Home
nav_order: 1
description: "feagent — Python FEM solver for 3D frame structures"
permalink: /
---

# feagent

<div align="center">
  <img src="img/Design%206.png" alt="feagent Logo" width="216">
</div>

A Python finite-element solver for the static, modal and buckling analysis of
**3D frame structures** (Euler-Bernoulli & Timoshenko beams) and shells —
tapered elements, end releases, thermal loads, prestress, settlements, load
cases and combinations, Excel I/O, a **command-line interface** that works on
Excel workbooks, Plotly visualization, Word reports and a Streamlit web UI.

---

## 📖 Documentation / Documentazione

The documentation is available in two languages with the same set of topics.
La documentazione è disponibile in due lingue con lo stesso insieme di argomenti.

| | |
|---|---|
| 🇬🇧 **[English documentation](en.html)** | Full guide: installation, CLI tutorial, modeling, loads, analyses, post-processing, case studies and Web UI. |
| 🇮🇹 **[Documentazione in italiano](it.html)** | Guida completa: installazione, tutorial CLI, modellazione, carichi, analisi, post-processing, casi studio e interfaccia web. |

Use the **language sections in the sidebar** (English / Italiano) to browse all
chapters. Use la **barra laterale** per sfogliare tutti i capitoli.

---

## Quick start from the terminal (no code)

```bash
pip install "feagent[all]"                  # see the installation page for the wheel / clone forms
feagent doctor                              # environment check
feagent examples simply_supported --verify  # a ready-made Excel model, solved and verified
feagent template model.xlsx                 # your own workbook: fill the sheets in Excel...
feagent check model.xlsx --solve            # ...validate it...
feagent solve model.xlsx --combos --envelope   # ...solve every combination with envelopes
feagent plot model.xlsx --what deformed --open
```

→ [CLI tutorial (English)](en-41-cli-excel-tutorial.html) ·
[Tutorial CLI (italiano)](it-41-cli-excel-tutorial.html) ·
[CLI reference](en-37-cli.html) ·
[AI connector](en-38-mcp-server.html)

## Quick start from Python

```python
from feagent import Model, Material, Section

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

## Web UI

```bash
pip install feagent streamlit plotly openpyxl
streamlit run app.py
```

---

## Key features

- **3D Euler-Bernoulli & Timoshenko beams** (12 DOFs), tapered elements, end releases, Q4/T3 shells
- **Loads**: nodal, in-span concentrated, distributed (uniform/partial/trapezoidal), thermal profiles, prestress, settlements
- **Load cases & combinations** with multiplicative coefficients, EN 1990 builders, **envelopes** with governing combination
- **Modal**, **buckling**, response-spectrum, moving-load and time-history analyses (cross-validated against independent solvers and published benchmarks)
- **Excel I/O** with validation (`feagent check`), **HDF5** results, **export** to OpenSees / SAP2000 / MIDAS / Robot / Straus7
- **CLI** (`feagent`), **Plotly** 3D plots, **Word** reports, **Streamlit web UI**
- **AI connector**: MCP server (Claude, ChatGPT, Codex, Gemini, Copilot, Cursor, ...), REST/OpenAPI server and function-calling definitions on the same 17 tools — `feagent connect <client> --write` ([guide](en-38-mcp-server.html))

## License

PolyForm Noncommercial 1.0.0 — see `LICENSE`. Built by Domenico Gaudioso.
