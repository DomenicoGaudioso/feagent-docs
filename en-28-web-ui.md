---
layout: default
title: "28 - Web UI (Streamlit)"
parent: English
nav_order: 28
---

# 28 - Web UI (Streamlit)

`beamfeapy` ships with a **web user interface** built with
[Streamlit](https://streamlit.io/) (`app.py` in the repository root). It lets you
build, analyze and visualize 3D frame models **without writing code**.

The workflow is linear:

> **Import from Excel** (sidebar) → **edit the tables** → **run analyses** →
> **visualize and export**.

---

## Launch

From the repository root:

```bash
pip install beamfeapy streamlit plotly openpyxl
streamlit run app.py
```

The browser opens automatically at `http://localhost:8501`.

{: .note }
**Demo mode** — to explore the app with a ready-made model (a 3D frame with a
static and a modal analysis already computed) start it with the environment
variable `BEAMFEAPY_DEMO=1` **or** open the URL with the `?demo=1` query
parameter (e.g. `http://localhost:8501/?demo=1`). All the screenshots on this
page use demo mode.

---

## Layout overview

The interface follows one strict rule: **the left sidebar contains only the
Excel model upload**. Everything else (model, analyses, results) lives in the
main area, organized into four tabs.

![Interface overview](images/ui_00_overview.png)

| Area | Content |
|------|---------|
| **Sidebar** | `Upload` of the model Excel file + `Download empty template`. Nothing else. |
| **📐 Model tab** | Editable tables (one per collapsible menu) for nodes, materials, sections, elements, supports and loads + 3D preview. |
| **🧩 Section groups tab** | Definition of section groups and link to load cases. |
| **⚙️ Analysis tab** | Run static, modal and buckling analyses. |
| **📊 Results tab** | 3D charts, result tables and export. |

The model state is kept in `st.session_state`: edits persist while switching
between tabs.

---

## 1. Import the model (sidebar)

The only input in the sidebar is the **Excel file upload**. Recognized sheets
(case-insensitive) are `Node`, `Material`, `Section`, `Element`, `Support`,
`NodalLoad`, `DistributedLoad`, `ConcentratedLoad`, `Thermal`, `Settlement`,
`Prestress` — the same ones described in [Excel I/O](en-11-excel-io.html).

- **Upload** → drag or pick the `.xlsx` file. The model is read and the tables in
  the main area are populated automatically.
- **Download empty template** → generates an Excel file with all the sheets and
  correct headers, to fill in and re-upload.

Starting from Excel is not mandatory: you can also build the model from scratch
by filling in the tables in the **Model** tab.

---

## 2. Model tab

Here you define or edit the whole model through **editable tables**
(`st.data_editor`). Each table lets you **add rows** (empty row at the bottom)
and **delete them** (row selection + trash button).

Each table is wrapped in a **collapsible menu** (expander) whose title shows an
icon, the list of columns and the **row count** in parentheses. This way you see
**only the table you need**, keeping the page compact. When the tab opens, only
the **Nodes** table is expanded; the others open with a click on their title.

![Model tab: one collapsible menu per table (only "Nodes" open)](images/ui_00_overview.png)

Available tables (one collapsible menu each):

| Table | Main columns |
|-------|--------------|
| **Nodes** | `Node, X, Y, Z` |
| **Materials** | `Material (name), E, nu, alpha, G, rho` |
| **Sections** | `Section (name/ID), A, Iy, Iz, J, Asy, Asz` |
| **Elements** | `Element, NodeI, NodeJ, Material, Section, shear, RefX/Y/Z, ReleasesI, ReleasesJ` |
| **Supports** | `Node, Dx, Dy, Dz, Rx, Ry, Rz` (check = fixed DOF) |
| **Nodal loads** | `Node, Fx..Mz, Case` |
| **Distributed loads** | `Element, Component (fy…), qi, qj, a, b, frame, Case` |

Practical notes:

- **Materials and sections** are referenced **by name** in the Elements table:
  the name must match exactly.
- **`ReleasesI` / `ReleasesJ`**: end releases at the two ends, as a
  comma-separated list (e.g. `rz,ry`).
- **`RefX/Y/Z`**: reference vector to orient the section (see
  [Section Orientation](en-08-section-orientation.html)); leave empty for the default
  orientation.
- **`a, b`** of distributed loads are **normalized in [0, 1]** along the element;
  `frame` can be `local` or `global`.

### Apply changes

The **✅ Apply changes** button rebuilds the `Model` object from the tables.
Inputs are **validated** (numbers, references to existing nodes and sections,
duplicate IDs): any error is shown in red **without crashing the app**. When the
model is valid, a summary (number of nodes, elements and sections) is shown.

The **⬇️ Export model (Excel)** button re-exports the current model to a
reloadable Excel file.

### 3D preview

At the bottom of the tab there is an interactive Plotly preview with three
selectable views: **Geometry**, **Local axes** and **Loads** (per load case).

![3D geometry preview](images/ui_05_anteprima3d.png)

---

## 3. Section groups tab

**Section groups** let you assign **different sections to elements** depending on
the analysis or load case (useful, for example, for construction stages or for
limit states with cracked/uncracked sections).

![Section groups tab](images/ui_02_section_groups.png)

- **Group → element → section map**: each row associates, within a group, an
  `Element` with a `Section`. Leaving **`Element` empty** applies the given
  section to **all** the elements of the group.
- **Group → load case link** (`link_section_to_cases`): associates a group with
  one or more load cases, so analyses can pick it up automatically.

The **✅ Apply** button rebuilds the model including the groups. Defined groups
are then offered in the dropdown of the Analysis tab.

---

## 4. Analysis tab

Pick the **analysis type** and its parameters, then press **▶️ Run**. The result
is stored in session and shown in the Results tab.

![Analysis tab](images/ui_03_analisi.png)

A **Section group** can be selected for any analysis (or `(default)`).

### Static

Flexible **Load case** field:

| Syntax | Meaning |
|--------|---------|
| `all` (or empty) | sum of all load cases |
| `G` | single load case |
| `G, Q` | several load cases (coefficient 1) |
| `G:1.35, Q:1.5` | **combination** with coefficients |

**Sparse solver** option for large models (see [Sparse Solver](en-12-sparse-solver.html)).

### Modal

Parameters: **number of modes**, acceleration **g**, and **mass_source** (mass
source from load cases, e.g. `G:1.0, Q:0.3`). See [Modal Analysis](en-19-modal-analysis.html).

### Buckling

Parameters: **number of modes** and **reference load case** (same syntax as
static). Computes the critical multipliers λ.

---

## 5. Results tab

The content adapts to the last analysis that was run.

![Results tab with deformed shape and displacement table](images/ui_04_risultati.png)

### Static
- **Charts**: *Deformed shape* (with scale factor), *Diagram* of internal forces
  with selectable component (`N, Vy, Vz, T, My, Mz`), *Reactions*.
- **Tables**: nodal displacements, support reactions and internal forces along an
  element (adjustable number of points).

The *View* selector on the left of the chart switches between the three views.
The **deformed shape** shows the displaced configuration overlaid on the
undeformed one (amplifiable via the *Scale* field):

![Deformed shape of the frame (interactive 3D view)](images/ui_06_deformata.png)

Choosing *Diagram* and the desired component gives the **internal-force diagram**
along the elements (here the bending moment `Mz`, with positive/negative
shading):

![Internal-force diagram Mz](images/ui_07_sollecitazioni.png)

Further down, the *Internal forces along the element* section lets you pick an
element and the number of points to obtain the **value table** of
`N, Vy, Vz, T, My, Mz` (useful for section checks).

### Modal
- **Mode shape** with a slider on the mode and a scale factor.
- **Table** of frequencies, periods and participating masses (X, Y, Z).

### Buckling
- **Buckling shape** with a slider on the mode.
- **Table** of critical multipliers λ.

### Export

At the bottom of the tab:

- **Results (Excel)** and **Results (HDF5)** — `Result.to_excel()` /
  `Result.to_hdf5()`, see [API Reference](en-17-api-reference.html).
- **Model export** to the six external formats — OpenSees TCL/Py, SAP2000, MIDAS,
  Robot, Straus7 — via `Model.export()`.

---

## Tips

- **No active model?** The Analysis and Results tabs show a notice: import an
  Excel file or fill in the tables and press *Apply changes*.
- Editing the tables does **not** rebuild the model until you press *Apply
  changes*: it is therefore safe to make several edits in a row.
- The charts are **interactive** Plotly 3D figures: rotate, zoom and pan with the
  mouse.
- Validation errors do not interrupt the app: fix the table and try again.

---

### Related pages
- [Quick Start](en-02-quick-start.html)
- [Excel I/O](en-11-excel-io.html)
- [Modal Analysis](en-19-modal-analysis.html)
