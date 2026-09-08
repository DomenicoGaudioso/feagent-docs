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
| Material | Material, E, [nu], [alpha], [G], [rho] |
| Section | Section, A, Iy, Iz, J, [Asy], [Asz] |
| Element | Element, NodeI, NodeJ, Material, Section, [shear], [RefX, RefY, RefZ], [ReleasesI], [ReleasesJ] |
| Support | Node, Dx, Dy, Dz, Rx, Ry, Rz (1 = vincolato, assi globali) |
| NodalLoad | Node, Fx, Fy, Fz, Mx, My, Mz, [Case] |
| DistributedLoad | Element, Component, qi, [qj], [a], [b], [frame], [Case] - `a`, `b` normalizzati in [0, 1] |
| ConcentratedLoad | Element, xi, Fx, Fy, Fz, Mx, My, Mz, [frame], [Case] - `xi` normalizzato in [0, 1] |
| Thermal | Element, [dT_axial], [dT_grad_y], [h_y], [dT_grad_z], [h_z], [Case] |
| Settlement | Node, Dof, Value (sempre attivo, senza caso di carico) |
| Prestress | Element, P, [e_i], [e_j], [plane], [sag], [Case] |
| Combination | Name, Case, Coef (opzionale, vedi sopra) |
| README | testo libero, ignorato |

Le unita' sono SI (N, m, Pa, kg, K) o qualsiasi sistema coerente. I carichi
gravitazionali sulle travi orizzontali sono componenti `fy` con segno
negativo (`y` locale = `Y` globale di default); vedi
[13 - Convenzioni](it-13-conventions.html) e
[08 - Orientazione della sezione](it-08-section-orientation.html). La
descrizione programmatica di ogni colonna (`feagent.io_excel.SHEET_DOCS`) e'
quella che il template scrive nel foglio `README`.
