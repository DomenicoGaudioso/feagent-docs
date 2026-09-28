---
layout: default
title: "11 - Excel I/O"
parent: Italiano
nav_order: 11
---

# 11 - Excel I/O

feagent legge e scrive modelli completi come workbook Excel (`.xlsx`): un
foglio per tipo di dato, una riga per nodo, elemento, vincolo o carico. E' il
formato usato dall'[interfaccia a riga di comando](it-37-cli.html) e
dall'[interfaccia web](it-28-web-ui.html), e un modo comodo per scambiare
modelli con altri strumenti. Per una guida foglio per foglio vedi il
[tutorial della CLI](it-41-cli-excel-tutorial.html).

## Installazione

```bash
pip install "feagent[excel]"     # pandas + openpyxl
```

## Generare un template

```python
from feagent.io_excel import write_template

write_template("input.xlsx")                    # mensola a 2 elementi, un carico di ogni tipo
write_template("vuoto.xlsx", blank=True)        # sole intestazioni di colonna
write_template("base.xlsx", readme=False, combinations=False)
```

Il template contiene tutti i fogli del formato, un foglio `README` con la
descrizione di ogni colonna (unita', convenzioni, in inglese e in italiano) e
un foglio `Combination` con esempi SLU/SLE. Dal terminale:
`feagent template input.xlsx [--blank] [--no-readme] [--no-combos]`.

## Importare un modello da Excel

```python
from feagent import Model, read_excel

m = read_excel("input.xlsx")            # metodo 1
m = Model.from_excel("input.xlsx")      # metodo 2 (equivalente)
res = m.solve(cases={"G": 1.35, "Q": 1.5})
```

Solo il foglio `Node` e' obbligatorio; i fogli mancanti o vuoti vengono
ignorati, come `README` e qualsiasi foglio dal nome non riconosciuto. I nomi
di fogli e colonne sono confrontati senza distinzione di maiuscole. I
**nomi** di materiali e sezioni restano sugli oggetti (`material.name`,
`section.name` e `m.sections[nome]`), quindi sopravvivono a un ciclo di
salvataggio e rilettura.

## Validare un workbook

```python
from feagent.validate import validate_excel

rep = validate_excel("input.xlsx", solve=True)
rep.ok                       # False se ci sono errori
for f in rep.findings:       # codice, livello, foglio, riga Excel, messaggio
    print(f)
```

`validate_excel` esegue gli stessi controlli di `feagent check`: fogli o
colonne mancanti, id duplicati, riferimenti a nodi/materiali/sezioni non
definiti, elementi di lunghezza nulla, vincoli assenti, carichi fuori
dall'elemento, gradienti termici senza altezza, combinazioni che citano casi
di carico sconosciuti; con `solve=True` anche un'analisi di prova con il
controllo di condizionamento della matrice di rigidezza e dell'equilibrio
globale. La tabella dei codici e' in
[37 - CLI, `check`](it-37-cli.html#check---validare-il-workbook).

## Combinazioni di carico: il foglio `Combination`

Le combinazioni con nome si possono memorizzare nel workbook, una riga per
coppia (combinazione, caso di carico):

| Name | Case | Coef |
|---|---|---|
| SLU | G | 1.35 |
| SLU | Q | 1.5 |
| SLE_rara | G | 1.0 |
| SLE_rara | Q | 1.0 |

```python
from feagent.io_excel import read_combinations

combos = read_combinations("input.xlsx")      # {"SLU": {"G": 1.35, "Q": 1.5}, "SLE_rara": {...}}
results = m.solve_many(combos)                # una fattorizzazione per tutte
env = m.envelope(combos)                      # feagent.combinations.Envelope
```

Alias di colonna: `Combination`/`Combo` per `Name`, `LoadCase` per `Case`,
`Coefficient`/`Factor`/`gamma` per `Coef` (vuoto = 1). Il foglio puo' anche
chiamarsi `Combinations`, `Combo` o `Combos`. La CLI lo usa con
`feagent solve --combos` e `feagent report --combos`.

## Salvare un modello in Excel

```python
m.to_excel("modello.xlsx")
m.to_excel("modello.xlsx", combinations={"SLU": {"G": 1.35, "Q": 1.5}})
mx = Model.from_excel("modello.xlsx")
```

Il workbook e' scritto nello stesso formato canonico letto da
`Model.from_excel`, quindi serve sia come file di salvataggio del modello sia
come file di scambio per l'app Streamlit. Flag Timoshenko (`shear`), aree di
taglio (`Asy`, `Asz`), rilasci di estremita', vettori di riferimento, nomi di
materiali e sezioni vengono tutti preservati. I cavi di precompressione sono
scritti nel solo foglio `Prestress`: i carichi nodali e distribuiti
equivalenti che generano **non** vengono duplicati nei fogli dei carichi,
perche' la lettura del foglio `Prestress` li rigenera.

## Esportare i risultati in Excel

```python
res.to_excel("risultati.xlsx", n_diagram=21)
```

Fogli:

- **Displacements**: spostamenti nodali `ux, uy, uz, rx, ry, rz` (assi globali)
- **Reactions**: reazioni vincolari `Fx .. Mz` (assi globali)
- **ElementEndForces**: forze d'estremita' di ogni elemento `FxI .. MzJ` (assi locali)
- **InternalForces**: `N, Vy, Vz, T, My, Mz` in `n_diagram` stazioni per elemento (se `n_diagram > 0`)

## Rileggere i risultati

```python
from feagent import read_results_excel

data = read_results_excel("risultati.xlsx")
data["displacements"][3]      # array(6,) del nodo 3
data["reactions"][1]          # array(6,) del nodo 1
data["element_forces"][2]     # array(12,) dell'elemento 2
data["internal_forces"]       # DataFrame dei diagrammi (se presente)
```

Dal terminale: `feagent results risultati.xlsx --node 3 --element 2`.

> Per salvare anche risultati **modali** e di **buckling**, e per modelli
> grandi, usa il formato HDF5: vedi [22 - Salvataggio risultati](it-22-saving-results.html).

## Workbook dell'inviluppo

```python
from feagent.io_excel import write_envelope

env = m.envelope(read_combinations("input.xlsx"))
write_envelope(env, "inviluppo.xlsx", n_diagram=21)
```

Fogli `Summary` (combinazioni e coefficienti), `Displacements` e `Reactions`
(min/max per nodo e GdL con la combinazione governante; reazioni per i soli
nodi vincolati) e `InternalForces` (min/max di ogni componente lungo ogni
elemento con la combinazione governante e la sua ascissa). E' il file scritto
da `feagent solve --combos --envelope`.

## Tabulato cliente

```python
res = m.solve(cases={"G": 1.35, "Q": 1.5})
res.to_client_excel("tabulato_cliente.xlsx", n_diagram=41)
```

Il workbook cliente contiene il modello, i carichi assegnati, spostamenti
nodali, reazioni, forze d'estremita' e tabelle delle azioni interne in un
unico file (`feagent solve --client`).

## Importare da tabelle Excel esterne

```python
from feagent import Model, write_normalized_external_excel

write_normalized_external_excel("tabelle_sap.xlsx", "feagent_input.xlsx")
m = Model.from_external_excel("tabelle_sap.xlsx", A=0.01, Iy=2e-5, Iz=3e-5, J=1e-5)
```

L'importatore riconosce i nomi di foglio e colonna comuni di SAP2000, MIDAS e
Robot come `Joint Coordinates`, `Connectivity - Frame`,
`Joint Restraint Assignments`, `Joint Loads - Force`, `NODE`, `ELEMENT`,
`CONSTRAINT`, `CONLOAD`, `Nodes`, `Bars` e `Supports`. Se il file esterno non
contiene le proprieta' numeriche complete di materiali o sezioni, passa i
valori di default (`E`, `nu`, `A`, `Iy`, `Iz`, `J`) e rivedi il workbook
normalizzato prima di usarlo (`feagent convert tabelle_sap.xlsx modello.xlsx --A ... --Iy ...`).

## Formato dei fogli Excel

| Foglio | Colonne (tra parentesi quelle opzionali) |
|--------|-------------------|
| Node | Node, X, Y, Z |
| Material | Material, E, [nu], [alpha], [G], [rho], [gamma] |
| Section | Section, A, Iy, Iz, J, [Asy], [Asz], [Shape, h, b, tw, tf, t, d] (forma, solo per la vista estrusa) |
| Element | Element, NodeI, NodeJ, Material, Section, [shear], [RefX, RefY, RefZ], [ReleasesI], [ReleasesJ], [SectionJ], [Stations], [Roll], [OffsetYI, OffsetZI, OffsetYJ, OffsetZJ] - `SectionJ` e `Stations` (`0.5:SEZ2`) rendono la trave a sezione variabile; `Roll` ruota la sezione attorno a x locale (gradi, con Ref vuoto); gli offset spostano l'asse della trave rispetto ai nodi (assi locali) |
| Support | Node, Dx, Dy, Dz, Rx, Ry, Rz (1 = vincolato, assi globali) |
| NodalLoad | Node, Fx, Fy, Fz, Mx, My, Mz, [Case] |
| DistributedLoad | Element, Component, qi, [qj], [a], [b], [frame], [Case] - `a`, `b` normalizzati in [0, 1] |
| ConcentratedLoad | Element, xi, Fx, Fy, Fz, Mx, My, Mz, [frame], [Case] - `xi` normalizzato in [0, 1] |
| Thermal | Element, [dT_axial], [dT_grad_y], [h_y], [dT_grad_z], [h_z], [Case] |
| Settlement | Node, Dof, Value (sempre attivo, senza caso di carico) |
| Prestress | Element, P, [e_i], [e_j], [plane], [sag], [Profile], [Case] - `Profile` (`0:0; 0.5:-0.3; 1:0`) e' il tracciato e(xi) poligonale al posto di e_i, e_j, sag |
| Combination | Name, Case, Coef (opzionale, vedi sopra) |
| ShellSection | Section, t, [kappa] - sezione di guscio o piastra |
| Shell | Shell, N1, N2, N3, [N4], Material, Section, [Formulation] - 4 nodi = Q4, 3 nodi = triangolo (`cst` o `thin`) |
| Truss | Truss, NodeI, NodeJ, Material, A oppure Section, [behavior] - biella (`both`, `tension`, `compression`) |
| Spring | Spring, NodeI, NodeJ, k, [behavior] - molla assiale (id condivisi con Truss) |
| Cable | Cable, NodeI, NodeJ, Type (`bar` o `catenary`), E, A, [w], [L0], [N0], [ernst], [tension_only] |
| ElasticSupport | Node, [kx], [ky], [kz], [krx], [kry], [krz] - molle a terra, assi globali |
| Constraint | Type (`rigid_link`, `equal_dof`, `diaphragm`), Master, Slave (anche `2,3,4`), [Dofs], [Plane] |
| ShellPressure | Shell, p, [Case] - pressione lungo la normale (ordine dei nodi, mano destra) |
| ShellLoad | Shell, qx, qy, qz, [frame], [projected], [Case] |
| ShellThermal | Shell, dT, [dT_grad], [Case] |
| SelfWeight | Case, [g], [DirX], [DirY], [DirZ] - peso proprio automatico di travi, bielle e gusci |
| Vehicle | Vehicle, Offset, Load, [Gauge] - veicolo, una riga per asse (Load = peso dell'asse, positivo) |
| Lane | Lane, Elements (`1:20` o `1,2,3`), [StartNode], [Ecc], [Skew], [Deck] - corsia su una catena di travi |
| MovingLoad | MovingLoad, Lane, Vehicle, [Positions], [Axis] (`-z`), [Factor], [Static], [UDL], [Width] - caso di carico mobile; `UDL` [N/m2] per `Width` (default 3 m) e' il carico distribuito di corsia applicato a scacchiera |
| SupportAxis | Node, xX, xY, xZ, yX, yY, yZ - appoggio ruotato: x locale e un vettore del piano x-y; i GdL di Support e Settlement del nodo diventano locali |
| SectionGroup | Group, [Elements], Section, [Cases] - sezioni alternative (fessurata, lungo termine); `Elements` vuoto = tutte le travi; `Cases` lega il gruppo ai casi di carico |
| ThermalProfile | Element (`3` o `1:10`), Axis (`y`, `z`), h, Profile (`-0.25:0; 0.2:4; 0.25:13`), [Width], [n_section], [Case] - profilo termico non lineare sull'altezza |
| Tendon | Tendon, P, [Elements], [Case] - cavo di precompressione definito dal tracciato |
| TendonPoint | Tendon, X, Y, Z - vertici del tracciato in ordine |
| Accelerogram | Accelerogram, Time, Acc - accelerogramma o funzione del tempo, una riga per campione |
| Device | Device, NodeI, [NodeJ], Type (`bilinear`, `fps`, `viscous`, `gap`), [Axes], parametri (k1 k2 Fy; W R mu mu_slow a u_y; c alpha; k gap sign), [coupled] - dispositivo non lineare, NodeJ vuoto = suolo |
| DynamicAnalysis | Analysis, Type, [Accelerogram], [Direction], [Scale], [dt], [t_end], [Damping], [DampingType], [F1], [F2], [Modes], [MassSource], [Method], [FreqMin], [FreqMax], [NFreq], [MovingLoad], [Speed], [SectionGroup] - analisi dinamica |
| DynamicLoad | Analysis, Node, Dof, Amplitude, [History], [Phase] - forza dinamica nodale |
| README | testo libero, ignorato |

Le unita' sono SI (N, m, Pa, kg, K) o qualsiasi sistema coerente. I carichi
gravitazionali sulle travi orizzontali sono componenti `fy` con segno
negativo (`y` locale = `Y` globale di default); vedi
[13 - Convenzioni](it-13-conventions.html) e
[08 - Orientazione della sezione](it-08-section-orientation.html). La
descrizione programmatica di ogni colonna (`feagent.io_excel.SHEET_DOCS`) e'
quella che il template scrive nel foglio `README`.

## Fogli avanzati: appoggi ruotati, gruppi di sezioni, profili termici, cavi

Ogni funzione della libreria ha il suo foglio, cosi' il modello si salva e si
rilegge senza perdite:

| In Python | Nel workbook |
|---|---|
| `add_beam(..., roll=...)`, `set_element_axes(...)` | colonne `Roll` (gradi) oppure `RefX, RefY, RefZ` (la y locale effettiva) di `Element` |
| `add_beam(..., offset_i=(oy, oz), offset_j=...)` | colonne `OffsetYI, OffsetZI, OffsetYJ, OffsetZJ` |
| `support(node, axes=R, ...)`, `set_support_axes(node, R)` | foglio `SupportAxis` (righe x e y di `R`) |
| `add_section_group(g, ...)`, `link_section_to_cases(g, ...)` | foglio `SectionGroup` |
| `add_thermal_profile(elem, punti, axis, h, width)` | foglio `ThermalProfile` |
| `add_prestress(elem, P, profile=...)` | colonna `Profile` di `Prestress` |
| `add_cable_prestress(P, punti, elements, name=...)` | fogli `Tendon` e `TendonPoint` |

I profili si scrivono come punti `ascissa:valore` separati da `;` e sono
lineari a tratti. Un profilo di precompressione dato come funzione Python si
salva campionato in 41 punti, gli stessi con cui la libreria costruisce il
cavo poligonale equivalente, quindi la rilettura restituisce gli stessi
carichi. I carichi generati da `add_cable_prestress` sono marcati
`origin="cable_prestress"` e non si riscrivono in `ConcentratedLoad`.

```python
m = Model()
...
m.support(9, axes=R, uy=True, uz=True)          # carrello su piano inclinato
m.add_section_group("FESS", "CRK")
m.link_section_to_cases("FESS", "G2")
m.add_thermal_profile(3, [(-0.25, 0), (0.2, 4), (0.25, 13)], axis="y", h=0.5, case="T")
m.add_cable_prestress(1.2e6, [(0, -0.1, 0), (6, -0.2, 0), (12, -0.1, 0)], case="P", name="C1")
m.to_excel("modello.xlsx")                       # tutto torna con read_model
```

## Analisi dinamiche nel workbook

`Accelerogram`, `Device`, `DynamicAnalysis` e `DynamicLoad` descrivono le
analisi dinamiche; `feagent.dynamic_cases.run_dynamic(model,
model.dynamic_defs, nome)` le esegue con la libreria:

| Type | Funzione |
|---|---|
| `time_history` | `Model.solve_time_history` (Newmark, integrazione diretta) |
| `modal_time_history` | `Model.solve_time_history_modal` (`Modes` modi, `Method` exact o newmark) |
| `nonlinear_time_history` | `nl_dynamics.solve_time_history_nl` con i dispositivi del foglio `Device` |
| `harmonic` | `Model.solve_harmonic` da `FreqMin` a `FreqMax` [Hz] in `NFreq` passi |
| `moving_dynamic` | `moving_dynamics.moving_load_dynamic_analysis` con il caso `MovingLoad` alla velocita' `Speed` [m/s], piu' il coefficiente di amplificazione dinamica rispetto alla scansione quasi statica |

L'eccitazione e' un accelerogramma alla base (`Accelerogram`, `Direction`,
`Scale`; piu' componenti separate da virgola) oppure le forze nodali di
`DynamicLoad` (`Amplitude` per la funzione del tempo `History`; senza
`History` la forza e' un gradino). Lo smorzamento e' di Rayleigh con rapporto
`Damping` alle frequenze `F1` e `F2` (vuote = primi due modi), modale nella
sovrapposizione modale, oppure assente (`DampingType = none`). `MassSource`
elenca i casi convertiti in massa (`G1=1 G2=1`); vuoto usa la densita' dei
materiali. `dt` e `t_end` vuoti prendono passo e durata dell'accelerogramma.

I dispositivi di `Device` seguono la legge non lineare solo nella time
history non lineare. Per le analisi lineari
`feagent.dynamic_cases.apply_device_stiffness(model)` li aggiunge come molle
con la rigidezza iniziale (vincolo elastico verso il suolo, molla
direzionale fra due nodi anche coincidenti) e `remove_device_stiffness` li
toglie; `run_dynamic` lo fa da solo per le analisi dinamiche lineari.
