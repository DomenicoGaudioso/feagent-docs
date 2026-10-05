---
layout: default
title: "43 - Elementi solidi 3D"
parent: Italiano
nav_order: 43
---

# 43 - Elementi solidi 3D

feagent risolve anche modelli con **elementi solidi** (volumetrici), nello
stesso `Model` di travi e gusci. Gli elementi vengono dal ramo `volumfeapy`,
ora integrato nella libreria (modulo `feagent.solid`). La formulazione degli
elementi è nel [capitolo 44](it-44-solid-formulation.html), i casi studio nel
[capitolo 45](it-45-solid-case-studies.html).

| Elemento | Nodi | Formulazione | Quando usarlo |
|---|---|---|---|
| `Hex8` | 8 | trilineare, Gauss 2x2x2 | stati di tensione regolari, mesh strutturate |
| `Hex8` con `incompatible=True` | 8 | modi incompatibili di Wilson-Taylor, condensati | **flessione** con pochi elementi nello spessore (pareti, solette, conci) |
| `Tet4` | 4 | lineare, deformazione costante | riempimento; rigido a flessione, serve una mesh fitta |
| `Tet10` | 10 | quadratico isoparametrico | **geometrie qualsiasi da Gmsh**: l'elemento più affidabile |
| `Wedge6` | 6 | triangolo per estrusione | strati estrusi da mesh triangolari |
| `Pyramid5` | 5 | esaedro degenere | raccordo fra esaedri e tetraedri |

Tutti gli elementi sono isoparametrici (Zienkiewicz, Taylor & Zhu 2013, cap. 6;
Bathe 2014, par. 5.3) e superano il patch test a deformazione costante anche su
mesh distorte. L'Hex8 standard soffre di irrigidimento a taglio in flessione
(una mensola con 2 elementi nello spessore ha l'86 % della freccia esatta);
con i modi incompatibili si arriva al 97 % e la flessione pura su elementi
regolari è esatta.

## Primo esempio: mensola

```python
from feagent import Model, Material
from feagent.solid_mesh import box_hex, nodes_where, boundary_faces_where

m = Model()
acciaio = Material(E=210e9, nu=0.3, rho=7850)
box_hex(m, 2.0, 0.2, 0.4, 10, 1, 4, acciaio, incompatible=True)   # L x b x h

for n in nodes_where(m, lambda x, y, z: x < 1e-9):              # incastro
    m.fix(n, ["ux", "uy", "uz"])
for e, f in boundary_faces_where(m, lambda x, y, z: abs(x - 2.0) < 1e-9):
    m.add_solid_surface_load(e, f, qz=-1e4 / (0.2 * 0.4))      # 10 kN in punta

r = m.solve(sparse=True)
print(r.solid_stresses(1))              # tensioni al baricentro dell'elemento 1
nodali = r.solid_nodal_stresses()       # mappa continua: {nodo: {sxx, ..., von_mises, s1, s2, s3}}
```

## Nodi e facce

Un nodo di solido usa i soli **3 GdL traslazionali** dei 6 del nodo feagent.
Le rotazioni dei nodi collegati soltanto a solidi non hanno rigidezza e il
solutore le blocca da sé: non serve vincolarle. Un momento applicato a un nodo
solo-solido è un errore esplicito (andrebbe perso): si applica una coppia di
forze oppure si collega una trave.

Convenzione dei nodi (numerazione locale da 1):

* **Hex8**: base 1-2-3-4, cima 5-6-7-8 (5 sopra 1). Una numerazione speculare
  è accettata (si integra con il valore assoluto dello Jacobiano), un elemento
  distorto o degenere è rifiutato.
* **Tet10**: 4 vertici, poi i nodi di spigolo (1-2), (2-3), (3-1), (1-4),
  (2-4), (3-4). L'importazione da Gmsh li riordina per posizione.
* **Wedge6**: triangolo 1-2-3 alla base, 4-5-6 in cima. **Pyramid5**: base
  1-2-3-4 e apice 5.

Facce per i carichi (indice da 0): Hex8 0 = base, 1 = cima, 2..5 = laterali;
tetraedri: faccia *k* opposta al nodo *k*+1; Wedge6 0 = base, 1 = cima, 2..4
laterali; Pyramid5 0 = base, 1..4 triangoli. Non serve ricordarle:
`m.solid_boundary_faces()` elenca le facce di bordo e
`boundary_faces_where(m, pred)` le seleziona per posizione. La normale è
ricavata dalla geometria, quindi il verso di numerazione della faccia non conta.

## Carichi

| Metodo | Carico |
|---|---|
| `add_nodal_load(n, Fx=..., ...)` | forze nodali |
| `add_solid_pressure(e, faccia, p)` | pressione, positiva se spinge verso l'interno |
| `add_solid_surface_load(e, faccia, qx, qy, qz)` | trazione in coordinate globali [F/L²] |
| `add_solid_body_force(e, bx, by, bz)` | forza di volume [F/L³] |
| `add_solid_thermal(e, dT)` | variazione termica uniforme, oppure lista dei valori ai nodi |
| `add_solid_temperature_field(T)` | campo termico nel volume da una funzione `T(x, y, z)` o da `{nodo: T}` |
| `add_self_weight("G1")` | peso proprio anche dei solidi (forze nodali consistenti) |
| `add_settlement(n, "uz", v)` | spostamenti imposti |

Tutti i carichi accettano `case=` e si combinano con coefficienti come gli
altri (`solve(cases={"G1": 1.35, "Q": 1.5})`, `solve_many`). Le forze
equivalenti sono **consistenti** (∫Nᵀ q dA, ∫Nᵀ b dV, ∫Bᵀ D ε_th dV); la parte
termica è sottratta nel recupero delle tensioni.

## Modelli misti con travi e gusci: da evitare

Solidi, travi e gusci si possono mettere nello stesso modello, ma **non si
integrano bene**: travi e gusci hanno 6 GdL per nodo (rotazioni comprese), i
solidi solo le 3 traslazioni, quindi all'attacco il collegamento non è
coerente.

* Un **nodo condiviso** trasmette solo forze: è una **cerniera**. I momenti
  della trave o del guscio non passano nel solido; una trave a sbalzo
  appoggiata su un solo nodo del solido è labile. La forza concentrata in un
  nodo dà inoltre tensioni locali che crescono con l'infittimento della mesh.
* I **link rigidi** dalla trave a tutti i nodi della faccia trasmettono il
  momento, ma impongono la sezione piana: la faccia risulta più rigida e le
  tensioni vicino all'attacco sono disturbate.

Per questo feagent **avvisa** ogni volta che si analizza un modello con solidi
insieme a travi o gusci: un `UserWarning` all'inizio di `solve`, `solve_many`,
`modal`, `buckling`, `solve_pdelta` e `solve_nonlinear`, e l'avviso tipizzato
`SOLIDI_MISTI` in `Result.warnings` / `Result.warning_details` (con il numero
di nodi condivisi e di vincoli cinematici fra le parti). Se la struttura
risulta labile, il messaggio d'errore indica la cerniera trave-solido come
causa probabile. L'avviso non rende il risultato inaffidabile
(`Result.reliable` resta vero): segnala che va controllato.

Quando serve comunque, il collegamento meno peggio è con i link rigidi sulla
faccia; si controlla l'equilibrio all'interfaccia e si leggono le tensioni del
solido a distanza dall'attacco (almeno una dimensione della sezione):

```python
m.add_node(1000, 2.0, 0.1, 0.2)                 # centro della faccia di estremità
for n in nodes_where(m, lambda x, y, z: abs(x - 2.0) < 1e-9):
    m.add_rigid_link(1000, n, dofs=["ux", "uy", "uz"])
m.add_beam(1, 1000, 1001, acciaio, sezione)     # la trave prosegue dal solido
```

Nei test la mensola metà solido e metà trave collegata così ha il momento
d'incastro esatto e la freccia al 98 % della soluzione di trave: la risposta
globale è buona, quella locale all'attacco no. Dove possibile si modella la
parte di interesse tutta in solidi, oppure tutta in travi e gusci.

## Analisi

* **Statica** lineare, densa e sparsa, combinazioni, `solve_many`, vincoli
  cinematici.
* **Modale**: la massa propria dei solidi (`rho`) entra sia concentrata
  (diagonalizzazione HRZ, nessuna massa negativa sui Tet10) sia consistente
  (`mass="consistent"`). Il percorso con la massa consistente richiede una
  rigidezza definita positiva, quindi nessun moto rigido libero.
* **P-Delta e buckling**: i solidi entrano con la sola rigidezza elastica
  (nessuna rigidezza geometrica propria).

## Mesh

`feagent.solid_mesh`:

* `box_hex(m, lx, ly, lz, nx, ny, nz, mat)` e `structured_hex(m, nx, ny, nz, fmap, mat)`:
  griglie di esaedri su qualsiasi mappa `(u, v, w) -> (x, y, z)` (corone,
  conci a sezione variabile, settori); con `tol > 0` i blocchi adiacenti si
  saldano sui nodi coincidenti;
* `from_gmsh(mat, model)`: importa la mesh 3D del modello Gmsh attivo (Tet4,
  Tet10, Hex8, Wedge6, Pyramid5), anche in un modello che contiene già travi e
  gusci; `physical_materials` assegna materiali diversi ai volumi;
* `mesh_box_tet(mat, lx, ly, lz, mesh_size=..., order=2)`: parallelepipedo in Tet10;
* `nodes_where` e `boundary_faces_where`: selezioni per vincoli e carichi.

Gmsh si installa con `pip install "feagent[mesh]"`.

## Risultati e grafici

`Result.solid_stresses(e, at="centroid" | "gauss" | "nodes")`,
`Result.solid_nodal_stresses()` (media sugli elementi adiacenti),
`Result.max_von_mises()`, `Result.solid_displacements(e)`.

```python
from feagent.plotting import plot_solid_mesh, plot_solid_stress, plot_solid_deformed, plot_solid_mode
plot_solid_stress(r, "von_mises", scale=200).show()
```

Le mappe colorano le facce di bordo con i valori nodali mediati, interpolati
con continuità sulle facce.

## Validazione

* Patch test di MacNeal-Harder su mesh con nodo interno spostato (Hex8, Hex8
  a modi incompatibili, Tet4, Wedge6), Tet10 a lati curvi: tensioni costanti
  esatte.
* Trazione, pressione idrostatica, dilatazione termica libera e impedita,
  flessione termica libera: esatti; colonna sotto peso proprio entro lo 0,2 %.
* Mensola: Tet10 99 %, Hex8 a modi incompatibili 97 % della soluzione di
  Timoshenko; frequenze di mensola entro il 3 %.
* **NAFEMS LE10** (piastra spessa in pressione, σyy in D = −5,38 MPa): Tet10
  −5,36 MPa (0,4 %), Hex8 a modi incompatibili −5,51 MPa (2,5 %); report in
  `validation/nafems/LE10`.
* **NAFEMS FV52** con il modello solido: i due elementi concordano fra loro
  (44 Hz sul primo modo, 4 % sotto il valore in forma chiusa della piastra
  spessa); dettagli in `validation/nafems/FV52`.

I test sono in `tests/test_solid.py`.

## Limiti attuali

* Accoppiamento con travi e gusci non coerente (vedi sopra): avviso
  `SOLIDI_MISTI`.
* Gli esportatori verso altri programmi, il workbook Excel, il file HDF5 e
  l'interfaccia `feagent gui` non trattano ancora i solidi: le esportazioni e
  i salvataggi avvisano quanti elementi restano fuori.
* Materiale elastico lineare isotropo.
