---
layout: default
title: "17 - API Reference"
parent: Italiano
nav_order: 17
---

# 17 - API Reference

Riferimento completo delle funzioni pubbliche di **beamfeapy**. Per gli esempi
illustrati di ciascuna famiglia vedi [Funzioni illustrate](it-18-illustrated-functions.html).

Import tipico:

```python
from beamfeapy import Model, Material, Section, VariableSection, read_excel
from beamfeapy import postprocess
from beamfeapy.plotting import (plot_model, plot_loads, plot_diagram,
                                plot_deformed, plot_reactions, plot_internal_forces)
```

---

## Materiali e sezioni

### `Material(E, nu=0.3, alpha=0.0, G=None, rho=0.0, name="")`
Materiale elastico isotropo. `G` (modulo di taglio) è derivato come
`E/(2(1+nu))` se non fornito. `alpha` = dilatazione termica; `rho` = densità
(non usata nell'analisi statica).

### `Section(A, Iy, Iz, J, Asy=None, Asz=None, h_y=None, h_z=None, name="")`
Proprietà della sezione (assi locali principali y, z).
- `A` area; `Iy` inerzia attorno a y (piano x-z); `Iz` attorno a z (piano x-y); `J` torsione.
- `Asy`, `Asz` aree di taglio efficaci — **solo per Timoshenko** (`shear=True`).
- `h_y`, `h_z` altezze sezione — **solo per i carichi termici a gradiente**.

### `VariableSection(A, Iy, Iz, J, Asy=None, Asz=None, breakpoints=())`
Sezione variabile lungo l'elemento: ogni proprietà è un valore costante o una
funzione di `xi = x/L ∈ [0,1]`.
- **`VariableSection.from_sections(sec_i, sec_j)`** — interpolazione lineare tra due sezioni.
- **`VariableSection.from_stations([(xi, sec), ...])`** — lineare a tratti tra stazioni (xi=0 e 1 obbligatorie); le ascisse interne diventano `breakpoints`.
- **`VariableSection.rectangular(b, h, E_shear=False)`** — sezione rettangolare con `b(xi)`, `h(xi)` (costanti o callable).

---

## Modello

### `Model()`
Contenitore del modello FEM. Attributi: `nodes`, `elements`, `sections`,
`nodal_loads`, `concentrated_loads`, `distributed_loads`, `thermal_loads`,
`prestress_loads`, `settlements`.

### `add_node(id, x, y, z) -> Node`
Aggiunge un nodo (6 GdL: `ux, uy, uz, rx, ry, rz`).

### `add_section(id, section=None, **props) -> Section`
Registra una sezione con un `id` riusabile. Si passa un `Section` o i parametri
(`A=`, `Iy=`, ...). Utile per definire elementi per id di sezione.

### `add_beam(id, node_i, node_j, material, section, ref_vector=None, roll=0.0, shear=False, releases_i=None, releases_j=None) -> BeamElement3D`
Elemento trave 3D prismatico (Eulero-Bernoulli; Timoshenko se `shear=True`).
- `section`: oggetto `Section` **o id** registrato con `add_section`.
- `ref_vector`: vettore che, con l'asse x locale, definisce il piano x-y (orientamento sezione). Se `None`, scelta automatica (Z globale, Y per elementi verticali).
- `roll`: angolo [rad] di rotazione della sezione attorno all'asse locale x.
- `shear`: se `True` usa Timoshenko (richiede `Asy`/`Asz` nella sezione).
- `releases_i`, `releases_j`: liste di GdL svincolati agli estremi, es. `["rz"]` (cerniera flessionale), `["ry","rz"]`. Eliminati per condensazione statica.

### `add_tapered_beam(id, node_i, node_j, material, varsection=None, section_i=None, section_j=None, stations=None, ref_vector=None, roll=0.0, shear=False, n_gauss=8)`
Elemento a **sezione variabile** (rigidezza esatta force-based). La sezione si
specifica in tre modi:
- `varsection`: una `VariableSection`;
- `section_i`, `section_j`: sezione (o id) a inizio/fine → interpolazione lineare;
- `stations={xi: sezione, ...}` o `[(xi, sezione), ...]` → lineare a tratti.

### Vincoli
- **`fix(node, dofs=None)`** — vincola i GdL elencati (`["ux","uy",...]`); `None` = tutti e 6 (incastro).
- **`pin(node)`** — cerniera sferica: blocca le 3 traslazioni.
- **`support(node, ux=False, uy=False, ..., rz=False)`** — vincolo selettivo, es. `support(1, uy=True, uz=True)`.

### `add_settlement(node, dof, value) -> Settlement`
Cedimento (spostamento/rotazione imposto): `dof` ∈ `{ux,uy,uz,rx,ry,rz}`.

---

## Carichi

Tutti i metodi `add_*` accettano `case="..."` (load case, default `"default"`).

### `add_nodal_load(node, case="default", Fx=0, Fy=0, Fz=0, Mx=0, My=0, Mz=0) -> NodalLoad`
Forza/coppia concentrata a un nodo (sistema globale).

### `add_concentrated_load(elem, xi, Fx=0, Fy=0, Fz=0, Mx=0, My=0, Mz=0, frame="local", case="default")`
Forza/coppia concentrata a un'**ascissa interna** `xi ∈ [0,1]` dell'elemento.
`frame`: `"local"` (default) o `"global"`. Carico equivalente `N(ξL)ᵀ[F,M]`.

### `add_distributed_load(elem, component, q_i, q_j=None, a=None, b=None, frame="local", case="default")`
Carico distribuito su un elemento. `component` ∈ `{fx,fy,fz,mx,my,mz}` (forze e
**momenti** distribuiti). `q_i`→`q_j` lineare (trapezoidale; uniforme se `q_j=None`);
`a,b` per un tratto **parziale**. `frame`: `"local"`/`"global"`.

### `add_thermal_load(elem, dT_axial=0, dT_grad_y=0, dT_grad_z=0, case="default")`
Variazione termica uniforme (`dT_axial`) e gradiente lineare lungo y/z (richiede
`h_y`/`h_z` nella sezione).

### `add_thermal_profile(elem, profile, axis="y", width=None, n_section=24, case="default")`
Profilo termico **generico** lungo l'altezza: `profile` è una funzione `T(s)` o
una sequenza di punti `[(s, T), ...]`. Proiezione su parte uniforme+lineare +
residuo autoequilibrato (eigenstress, EN 1991-1-5).

### `add_prestress(elem, P, e_i=0, e_j=None, plane="y", sag=0.0, profile=None, case="default")`
Precompressione da cavo (metodo dei carichi equivalenti): tiro `P`, eccentricità
`e_i`→`e_j` + freccia parabolica `sag` (o `profile(xi)`). `plane` ∈ `{y,z}`.

### `add_cable_prestress(P, points, elements=None, case="default")`
Precompressione dalla **geometria 3D del cavo**: `points` = polilinea di
coordinate globali, `P` = tiro. Calcola forze di ancoraggio (`±P·t`) e deviazione
(`P·(t_out−t_in)`) e le applica come carichi concentrati eccentrici sulle travi.

---

## Soluzione

### `load_cases() -> list[str]`
Elenco ordinato dei load case presenti tra i carichi.

### `solve(sparse=False, cases=None) -> Result`
Risolve il sistema.
- `sparse`: `True` usa assemblaggio a triplette COO→CSR e solver sparso SuperLU (modelli grandi).
- `cases`: combinazione di carico —
  - `None` = tutti i carichi (coeff 1);
  - stringa = un singolo load case;
  - lista/insieme = combinazione (coeff 1 ciascuno);
  - **dict `{case: coefficiente}`** = combinazione con **coefficienti moltiplicativi** per ogni load pattern, es. SLU `solve(cases={"G": 1.35, "Q": 1.5})`.

### `assemble_mass(mass_source, g=9.81) -> ndarray`
Vettore delle masse concentrate (diagonale, ndof) ottenute dai carichi dei load
case indicati. `mass_source` è un dict `{case: coefficiente}`: massa = coeff ·
|forza| / g, attribuita ai 3 GdL traslazionali del nodo. I carichi distribuiti
sono ripartiti ai nodi (forze nodali equivalenti), i concentrati in campata
linearmente. Mettere nella sorgente i load case gravitazionali (verticali).

### `modal(n_modes=10, mass_source=None, g=9.81) -> ModalResult`
Analisi modale: risolve `K φ = ω² M φ` sui GdL liberi, con masse da `mass_source`
(l'utente sceglie quali load case trasformare in massa e con quale coefficiente).
I GdL liberi senza massa sono eliminati per condensazione statica (niente modi
spuri). Validata vs OpenSees a precisione macchina.

#### `ModalResult`
Attributi: `omega` [rad/s], `freq` [Hz], `period` [s], `phi` (ndof × n_modi,
forme normalizzate a massa), `eff_mass` (n_modi × 3), `part`, `total_mass`.
Metodi: `mode(i)`, `mode_shape(i, node)`, `mass_participation()` (rapporti per
modo e direzione X/Y/Z), `summary()`.

### `from_excel(path)` *(classmethod)* / `assemble_stiffness()` / `assemble_stiffness_sparse()` / `assemble_loads(cases=None)`
`Model.from_excel(path)` costruisce il modello da Excel (vedi [Excel I/O](it-11-excel-io.html)).
Gli `assemble_*` espongono matrice di rigidezza e vettore dei carichi.

---

## Risultati (`Result`)

Attributi: `U` (spostamenti globali), `R` (reazioni globali),
`element_forces` (forze d'estremità locali per elemento, 12),
`element_local_disp`, `cases`.

- **`displacements(node) -> ndarray(6)`** — `[ux,uy,uz,rx,ry,rz]` del nodo.
- **`displacement(node, dof) -> float`** — singola componente.
- **`reactions(node) -> ndarray(6)`** — `[Fx,Fy,Fz,Mx,My,Mz]` del nodo.
- **`to_excel(path, n_diagram=0)`** — esporta spostamenti, reazioni, forze d'estremità (e diagrammi se `n_diagram>1`).

---

## Post-processing (`beamfeapy.postprocess`)

### `internal_forces(result, elem_id, n=51) -> dict`
Azioni interne lungo l'elemento. Restituisce `{x, N, Vy, Vz, T, My, Mz}` (array
di `n` valori). N>0 trazione; include i contributi di carichi distribuiti e
concentrati (salti).

### `element_displacements(result, elem_id, n=21) -> dict`
`{x, u_local}` (n×6): spostamenti locali interpolati lungo l'elemento.

### `deformed_shape_global(result, elem_id, n=21, scale=1.0) -> ndarray(n,3)`
Coordinate globali della linea d'asse deformata (amplificata di `scale`).

---

## Visualizzazione (`beamfeapy.plotting`)

Richiede l'extra `plot` (`plotly`, `kaleido`). Ogni funzione restituisce una
`plotly.graph_objects.Figure` (`.show()`, `.write_html(...)`, `.write_image(...)`).

- **`plot_model(model, show_node_ids=True)`** — geometria (nodi, elementi).
- **`plot_loads(model, case=None, scale=0.14)`** — struttura + carichi (filtrabile per load case).
- **`plot_diagram(result, component="Mz", scale=None, n=41)`** — diagramma di una sollecitazione lungo la struttura, disegnato come **area riempita bicolore** (positivo e negativo con colori diversi e trasparenza; flessione: positivo celeste, negativo rosso chiaro). Ogni azione è tracciata sul proprio asse di utilizzo (N sull'asse di My). Momenti in convenzione europea (negativo all'estradosso). Etichette sul massimo e minimo globali.
- **`plot_deformed(result, scale=1.0, n=21)`** — configurazione deformata.
- **`plot_reactions(result, scale=0.14)`** — reazioni (forze e momenti) ai vincoli.
- **`plot_internal_forces(result, elem_id, n=101)`** — i 6 diagrammi 2D di un elemento.
- **`plot_mode(modal_result, i=0, scale=1.0, n=21)`** — disegna la i-esima forma modale (con frequenza e periodo nel titolo).

---

## Import/Export Excel (`beamfeapy.io_excel`)

- **`read_model(path)`** (alias `beamfeapy.read_excel`, `Model.from_excel`) — costruisce il modello dai fogli Excel.
- **`write_results(result, path, n_diagram=0)`** (alias `Result.to_excel`) — esporta i risultati.
- **`write_template(path)`** — genera un workbook di esempio compilabile.

Fogli: `Node, Material, Section, Element, Support, NodalLoad, DistributedLoad,
ConcentratedLoad, Thermal, Settlement, Prestress` (vedi [Excel I/O](it-11-excel-io.html)).
