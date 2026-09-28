---
layout: default
title: "42 - Desktop and online GUI (feagent gui)"
parent: English
nav_order: 42
---

# 42 - Desktop and online GUI

`feagent gui` starts the solver's **graphical interface**: a local server
(on your PC only, address `127.0.0.1`) and an application window in the
browser. It works like a classic FEM program: model tree on the left, 3D view
in the middle, properties on the right, menus and toolbar on top, messages at
the bottom.

![Desktop GUI: tree, view, properties](images/gui_moment.png)

A model can be built in three ways, freely mixed:

* by **drawing** it in the view (nodes and beams snapping to the grid and to nodes);
* in **tabular form**, as in Excel, with copy and paste to and from Excel;
* by **importing** a feagent workbook, SAP2000/MIDAS/Robot tables or an AI
  connector JSON spec.

## Starting

```bash
pip install "feagent[gui]"      # adds pandas and openpyxl
feagent gui                     # empty model
feagent gui frame.xlsx          # open a model
```

On Windows `feagent-gui` starts the same interface without a terminal window,
which suits a desktop shortcut. The interface opens in an Edge or Chrome
window in application mode, or in the default browser if neither is found.

| Option | Effect |
|---|---|
| `--port 8777` | preferred port (a free one is picked if it is busy) |
| `--no-browser` | do not open the window: print the address instead |
| `--keep-alive` | keep the server running after the window is closed |
| `--online` | multi-user server (see [Online use](#online-use)) |

The server stops with **File > Exit**, when the window is closed (after about
45 seconds without signals from the interface) or with Ctrl+C in the
terminal. It listens locally only and every call needs the session token
created at start-up, so no web page open in the browser can read or change
your files.

## The model document

What the tree and the tables show is the **feagent Excel format** (sheets
Node, Material, Section, Element, Support, loads, Combination; see
[Excel I/O](en-11-excel-io.html)). Saving as `.xlsx` therefore writes a workbook
that Python (`Model.from_excel`), the command line (`feagent solve`) and the
AI connector read back unchanged. A `.feagent.json` project with the same
content is the alternative. The only extra sheet is **LoadCase**, which also
lists load cases that have no loads yet; the library ignores it.

## Menus and toolbar

![File menu with the import submenu](images/gui_menu.png)

| Menu | Content |
|---|---|
| File | new, open, recent files, save, save as, import, export, exit |
| Edit | undo and redo (60 steps), selection, delete, copy and move |
| View | 3D, plan, front, side, fit; labels, local axes, supports, loads, grid |
| Model | drawing tools, nodes by coordinates, structural grid, divide, merge nodes, properties, materials and sections |
| Loads | load cases, nodal, distributed, concentrated and thermal loads, self weight, combinations |
| Analysis | model check (codes E01-E63 of `feagent check`), static per load case, custom combination, combinations, modal, buckling |
| Results | deformed shape, N, Vy, Vz, T, My, Mz, reactions, modes, tables |
| Tools (Strumenti) | connect an AI (address, token, ready-made configurations), options |

**Export** writes the model for OpenSees (Tcl and Python), SAP2000, MIDAS,
Robot and Straus7, the results to Excel, the calculation printout and the
Word report. When native file dialogs are not available, opening and saving
go through browser upload and download.

## Model tree

It groups nodes, elements, materials, sections, supports, settlements, load
cases (with their loads by type) and combinations, then analyses and results.

* **click**: select in the view (a material or a section selects the beams
  using it; a load case becomes the displayed case);
* **double click**: open the matching table, run an analysis or show a result;
* **right click**: node actions (new case, new combination, rename, assign to
  the selection, parametric section, new material).

## 3D view

| Action | Command |
|---|---|
| rotate | right-button drag |
| pan | middle button, or Shift + right button |
| zoom | wheel (centred on the cursor) |
| select | click; Ctrl+click adds or removes |
| window selection | drag to the right (elements fully inside) |
| crossing selection | drag to the left (elements touched) |
| views | keys 1 (3D), 2 (plan), 3 (front), 4 (side), F (fit) |

The vertical axis (Y or Z) is inferred from the load directions and can be
fixed in **View > Options**. The loads drawn are those of the **active load
case**, chosen in the toolbar or in the tree.

### Drawing

* **N** (Draw nodes): a click adds a node on the working plane, on the grid step.
* **B** (Draw beams): click the first point and the following ones, like a
  polyline; right click or Esc ends the chain. Points snap to existing
  nodes; new beams take the material and section chosen in the toolbar.
* The working plane follows the view (plan, front) or is fixed in
  **View > Options**, together with its level and the grid step.

For regular geometry **Model > Structural grid** builds continuous beams,
plane frames and 3D frames from span lists (`3*6 4.5` means three 6 m spans
and one 4.5 m span), with fixed or pinned bases.

![Deformed 3D frame with colour scale](images/gui_deformed.png)

## Tables

![Element table](images/gui_table.png)

Every sheet opens in a tab (double click in the tree, or **Model > Tables**
and **Loads > Load tables**). The grid handles large models because it draws
only the visible rows.

| Key | Effect |
|---|---|
| arrows, Tab, PgUp/PgDn | move |
| typing, Enter, F2 | edit the cell |
| Shift + arrows, drag | select a block |
| Ctrl+C / Ctrl+V | copy and paste, also to and from Excel |
| Del | clear the selected cells |
| Ctrl+Del | delete the selected rows |

When the pasted block has a header row with column names, values go to the
right columns even in a different order; extra rows are appended. Selecting
rows in Nodes or Elements selects the objects in the view, and vice versa.

## Properties

The right panel follows the selection: coordinates, support, loads,
displacements and reactions for a node; nodes, material, section, Timoshenko
formulation, releases, local axes, loads and internal force extremes for a
beam; the available actions for a multiple selection. With nothing selected
it summarises the model.

## Results

After an analysis the toolbar offers the result (load case, combination or
mode), the quantity and the scale.

* **Deformed shape**: elastic line from the element shape functions (releases
  included), coloured by displacement.
* **Internal forces**: N, Vy, Vz, T, My and Mz diagrams in the element's local
  plane, with maximum and minimum. **Moments are drawn on the tension side**:
  for horizontal beams with local y upwards, negative moments lie above the
  beam and positive ones below.
* **Reactions**: arrows with values and the reaction sum.
* **Modes**: animated mode shapes, frequencies and participating masses, or
  buckling load multipliers.
* **Tables**: displacements, reactions (with their sum) and internal forces
  along the elements, ready to copy into Excel.

When the model changes after an analysis, the tree and the view flag the
results as out of date (F5 to rerun).

## Driving the interface with an AI

Each session's model lives on the server: the window, AI assistants and other
windows open on the same session read and change it, and an event channel
notifies everyone at once. While an AI works, the screen updates by itself:
tree, view, tables and results. The status bar shows **IA al lavoro** (AI at
work) and its messages appear in purple; every AI change is one undo step
(Ctrl+Z removes it, and the AI sees the undone state).

![Frame built, analysed and shown by an AI](images/gui_ai_live.png)

The AI has three channels, all on the same API:

* **MCP** (Claude Desktop, Claude Code, Codex, Cursor, ...): the connector's
  `gui_*` tools ([page 38](en-38-mcp-server.html)). Locally the configured
  connector is enough (`feagent connect claude-desktop --write`): `feagent
  gui` writes address and token to `~/.feagent/gui_session.json` and the tools
  find them.
* **REST/OpenAPI**: `POST /api/live/<action>` with `Authorization: Bearer
  <token>`; the spec is at `/api/live/openapi.json` (Custom GPT Actions, n8n,
  scripts).
* **Python**: `agent_api.call("gui_state")` and the other tools.

| Action | Effect |
|---|---|
| `live/state` | model summary, user selection and view, results |
| `live/model` | model sheets |
| `live/edit` | sheet operations (upsert, delete, replace_sheet, rename, set_meta) |
| `live/replace` | new model from a JSON spec |
| `live/run` | analysis; results appear in the window |
| `live/results` | displacements, reactions, diagrams |
| `live/show` | result, view, selection, table or message to show |
| `live/screenshot` | image of the view |
| `live/check`, `live/export` | validation, exported file |

**Strumenti > Collega un'IA** (Tools > Connect an AI, or the **IA** toolbar
button) shows the session address and token with the MCP configuration, a
`curl` example and Python lines ready to copy. The token opens **only that
session**.

![Connect an AI dialog](images/gui_ai_connect.png)

Example of `gui_edit` operations:

```json
[{"op": "upsert", "sheet": "nodes", "rows": [{"id": 5, "x": 12, "y": 4, "z": 0}]},
 {"op": "upsert", "sheet": "elements", "rows": [{"id": 4, "i": 3, "j": 5, "material": "S355", "section": "IPE400"}]},
 {"op": "delete", "sheet": "NodalLoad", "where": {"Case": "W"}}]
```

## Online use

The same program runs as a multi-user web service:

```bash
export FEAGENT_GUI_KEY="a-long-key"
feagent gui --online --port 8777 --public-url https://fem.example.com
```

* opening the page asks for the **access key**; every login opens a
  **separate session** (own model, results and token), kept for 12 hours of
  inactivity;
* the server **never touches its own file system**: models are uploaded and
  downloaded through the browser (File > Open, Save, Export);
* without a key the server refuses to start, unless `--no-auth` is given for
  trusted networks; `--max-sessions` caps concurrent sessions and at most two
  analyses run at the same time;
* expose it **only behind HTTPS** (a reverse proxy such as Caddy or nginx, or
  a tunnel); `--public-url` is the public address shown in the AI dialog.
  With nginx the event channel must pass unbuffered (the server already sends
  `X-Accel-Buffering: no`).

Example with Caddy, which obtains the certificate by itself:

```text
fem.example.com {
    reverse_proxy 127.0.0.1:8777
}
```

In a container, from the repository root:

```bash
docker build -f deploy/Dockerfile -t feagent-gui .
docker run -d -p 127.0.0.1:8777:8777 -e FEAGENT_GUI_KEY=a-long-key feagent-gui
```

## Shortcuts

| Keys | Command |
|---|---|
| Ctrl+N, Ctrl+O, Ctrl+S, Ctrl+Shift+S | new, open, save, save as |
| Ctrl+Z, Ctrl+Y | undo, redo |
| F5 | static per load case |
| F6 | combinations |
| S, N, B | select, draw nodes, draw beams |
| Del | delete the selection |
| Esc | end the beam chain, back to selection, clear selection |

## Relation to the Streamlit app

The Streamlit app ([page 28](en-28-web-ui.html)) is still available, but the
desktop interface replaces it for everyday work: drawing in the view, tables
with copy and paste, undo and redo, native file dialogs, no dependencies
beyond pandas and openpyxl.
