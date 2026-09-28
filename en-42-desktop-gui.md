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

## Shells, plates, trusses, springs and cables

Besides beams the model accepts every element of the solver, each with its own
sheet (see [Excel I/O](en-11-excel-io.html)) and tree group:

| Element | Sheet | How to create it |
|---|---|---|
| Shell or plate (Q4, triangle) | Shell, ShellSection | tool **P** (3 or 4 vertices in order), **Model > Plate mesh generator**, table |
| Truss bar | Truss | Truss tool (area and behaviour asked first), table |
| Axial spring | Spring | Spring tool, table |
| Cable (bar or catenary) | Cable | Cable tool, table |
| Elastic support to ground | ElasticSupport | selected nodes, **Model > Elastic supports** |
| Rigid link, equalDOF, diaphragm | Constraint | two or more selected nodes, **Model > Kinematic constraint** (the first is the master) |
| Pressure, surface and thermal loads on shells | ShellPressure, ShellLoad, ShellThermal | selected shells, **Loads > Shell load** |
| Automatic self weight | SelfWeight | **Loads > Self weight** |

![Steel frame with shell slab, truss bar, spring and constraints](images/gui_mixed.png)

**Self weight** is no longer a list of generated loads: it is a definition the
solver recomputes at every analysis from the unit weight (γ, or ρ·g) times the
area of beams and trusses and the thickness of shells, so it stays right when
sections change. With **cables** the static analysis becomes nonlinear
(Newton-Raphson) automatically and the message log says so.

## Extruded view

**View > Extruded view** (key **E**, or the cube in the toolbar) draws beams
with their real section and shells with their thickness, coloured by material
(steel, concrete, timber). The shape comes from the `Shape`, `h`, `b`, `tw`,
`tf`, `t`, `d` columns of the Section sheet, filled by the parametric section
wizard; otherwise the rectangle equivalent to `A`, `Iy`, `Iz` is used.

![Extruded view: I-section columns and slab thickness](images/gui_extruded.png)

## Analyses and results of the special elements

| Analysis | Notes |
|---|---|
| Static per case, combination, combinations | linear; nonlinear automatically when cables are present |
| P-Delta | second order with the updated geometric stiffness |
| Nonlinear | Newton-Raphson with load steps; cables and tension-only or compression-only members |
| Modal, buckling | as before, with shells, trusses and cables (cable pretension included) |
| Moving loads | axle trains on lanes: envelopes, travelling vehicle, influence lines (see below) |
| Response spectrum | EC8 / NTC 2018 type 1 spectrum (a<sub>g</sub>, soil, q, ξ), CQC or SRSS, one result per direction (unsigned envelopes) and base shear |

Results add **colour maps on shells** (Mx, My, Mxy, Nx, Ny, Nxy, Qx, Qy at the
element centre, local axes), the **axial force of trusses, springs and
cables** (blue tension, red compression, width proportional), elastic support
reactions and the related tables.

![Plate supported on its edges under pressure: moment My](images/gui_plate.png)

## Calculation report

**File > Export > Word calculation report** runs the selected analyses (load
cases, combinations, modal) and writes a report in the house style: Calibri
11, black headings, native tables with a light blue header, centred figures
captioned "Figura N — …", no software names in the text. Figures are taken
from the view, so they include shells, cables, the extruded model view and
moments on the tension side.

Contents: introduction, codes, units and conventions, materials, beam and
shell sections, model with figure and input tables, loads per case with
figures and combinations, results per case and combination (deformed shape,
diagrams, shell maps, axial forces, reactions, extremes per beam) with the
**global equilibrium check** between applied loads and reactions, modal
analysis. The same data without figures are available to an AI through
`live/export` with format `report`.

## Tapered beams

A beam becomes tapered by setting the **section at node J** (column
`SectionJ`) and optional **inner stations** (`Stations`, e.g.
`0.3:SEC2; 0.7:SEC3`), from the properties panel or **Element properties**.
The stiffness is exact (integration of the section flexibility along the
axis), so one beam per span is enough.

When the sections at the stations share the same shape with dimensions (those
created with **New parametric section** do), the **dimensions** are
interpolated: for a linearly varying depth the inertia then varies with the
cube of the depth, as it should. Otherwise A, I and J are interpolated. The
extruded view follows the taper.

![Grillage with variable depth main girders, extruded view](images/gui_tapered.png)

## Moving loads

Three sheets describe moving loads (**Loads > Moving loads**):

| Sheet | Content |
|---|---|
| Vehicle | one vehicle per name, one row per axle: position, weight, gauge (presets LM1 lanes 1, 2, 3, LM2, single axle, train of equal axles) |
| Lane | lane on a chain of beams (from the selected beams), start node, eccentricity, axle skew, distributing cross beams |
| MovingLoad | moving case: lane, vehicle, number of positions, load direction, factor, superposed static combination |

The vehicle travels the whole lane; each position is a static solution (one
stiffness factorisation for all positions). With eccentricity, gauge or skewed
axles the wheels are spread over the grillage cross beams; the eccentricity is
positive to the left of the travel direction seen from above (normal =
vertical × tangent).

**Analysis > Moving loads** returns for each case:

* **envelopes** of N, V, T and M (maximum in blue, minimum in red), with the
  superposed static combination and the factor on the moving load, and the
  reaction envelope;
* the **travelling vehicle**: a position slider and a play button in the
  toolbar, with the deformed shape at each position and the wheels drawn
  where the solver applies them;
* tables of envelopes per beam, of minimum and maximum displacements and
  reactions, and of the **reaction influence lines**.

![Moment envelope under the LM1 tandem](images/gui_moving.png)

The calculation report lists vehicles, lanes, moving cases, envelope figures,
the vehicle at midspan and the envelope tables.

The **lane distributed load** (`UDL` and `Width` columns of the moving case,
for example 9 kN/m² over 3 m for lane 1 of load model LM1) is applied **in a
checkerboard pattern**: for every quantity, at every station, only the
stretches where the influence line has an adverse sign are loaded, and the
contribution adds to the vehicle's (for two equal spans the largest positive
moment is 0.0957 qL², the one over the support qL²/8). When a wheel falls
beyond the ends of the distributing cross beams the log says so, with the
number of cases and the largest distance: usually eccentricity or gauge are
wrong.

## Rotated and offset beams, rotated supports, section groups

* **Section roll** about the local x axis (`Roll` column, degrees, with an
  empty reference vector) and **axis offsets** from the nodes (`OffsetYI`,
  `OffsetZI`, `OffsetYJ`, `OffsetZJ`, local axes), like the section offset of
  commercial programs: from the properties panel or **Element properties**.
  The view draws the offset axis with dashed rigid arms, and the extruded view
  shifts the section.
* **Rotated support axes** (**Model > Rotated support axes**, `SupportAxis`
  sheet): for example a roller on an inclined plane; the node's restraints and
  settlements become local, the view draws the x′ and y′ axes and the results
  also list the reactions in the local axes.
* **Section groups** (**Model > Section group**, `SectionGroup` sheet):
  alternative sections (cracked, long term) for some beams or all of them.
  When all the cases of an analysis are linked to the same group it applies by
  itself; otherwise it is chosen in the static, modal and buckling dialogs.

## Thermal profiles and prestressing tendons

* **Nonlinear thermal profile** (**Loads > Thermal profile**): depth:temperature
  points over the section depth, with a starting model for top-surface
  heating; the analysis takes the uniform and linear parts of the profile,
  weighted on the section width.
* **Tendon by path** (**Loads > Prestressing tendon**): X Y Z vertices of the
  tendon (also from the selected nodes, shifted by the eccentricity), force
  and candidate beams. Anchor and deviation forces go to the nearest beams
  with the eccentricity moment; the view draws the dashed path.
* Beam prestress also accepts a **profile** `xi:e` instead of eccentricities
  and sag: the tendon is the polygon through those points.

![Bridge with a tendon path, rotated abutment support and offset beam](images/gui_advanced.png)

## Dynamic analyses

The **Dynamics** group of the tree and **Analysis > Dynamics** hold:

| Object | Content |
|---|---|
| Accelerograms | from a text file (one or two columns, scale factor), artificial ones compatible with the EC8 / NTC 2018 spectrum (Gasparini and Vanmarcke), sines, pulses; the chart shows the record and its elastic response spectrum |
| Devices | friction pendulum (FPS), elastomeric or bilinear isolators, viscous dampers, gap restrainers; between two nodes or a node and the ground |
| Dynamic analyses | linear (Newmark) or modal time history, nonlinear time history with the devices, harmonic response, moving train |
| Dynamic forces | nodal forces times a time function, or harmonic with a phase |

Each analysis has its mass source, damping (Rayleigh at two frequencies,
modal, none) and optional section group. **Analysis > Dynamic analyses** runs
them and returns for each one:

* the **deformed shape in time** (or per frequency for the harmonic one):
  slider and play button in the toolbar;
* the **envelopes** of N, V, T and M and of the reactions (amplitudes for the
  harmonic one);
* the **time history of any node** (displacement, velocity, relative or
  absolute acceleration, reaction) or the harmonic **response curve**,
  computed on request, exportable as CSV and PNG;
* the **base shear** in time (restraints plus ground devices);
* the **force-displacement loop** and the dissipated energy of every device;
* for the moving train, the **dynamic amplification factor** against the
  quasi static scan of the same train.

![Frame on friction pendulum isolators: deformed shape during the earthquake](images/gui_dynamic.png)

![Force-displacement loop of a friction pendulum isolator](images/gui_device.png)

The calculation report adds the dynamic analyses chapter: equation of motion,
damping, accelerograms, devices, peak summary, histories, base shear, device
loops and envelopes.

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
| `live/history` | time history of a node or harmonic curve of a dynamic analysis |
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
| S, N, B, P | select, draw nodes, beams, shells |
| E | extruded view |
| Enter | close the shell being drawn as a triangle |
| Del | delete the selection |
| Esc | end the beam chain, back to selection, clear selection |

## Relation to the Streamlit app

The Streamlit app ([page 28](en-28-web-ui.html)) is still available, but the
desktop interface replaces it for everyday work: drawing in the view, tables
with copy and paste, undo and redo, native file dialogs, no dependencies
beyond pandas and openpyxl.
