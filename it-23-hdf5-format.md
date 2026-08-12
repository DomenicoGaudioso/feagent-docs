---
layout: default
title: "23 - Formato HDF5"
parent: Italiano
nav_order: 23
---

# 23 - Il formato HDF5 dei risultati

Questa pagina descrive in dettaglio il **formato binario HDF5** (`.h5`) usato da
beamfeapy per archiviare i risultati delle analisi, come ispezionarlo e come
rileggerlo da altri strumenti (Python puro, MATLAB, Julia, C++).

Per l'**uso pratico** (salvare/rileggere dalla libreria) vedi
[22 - Salvataggio risultati](it-22-saving-results.html). Questa pagina è
complementare e si concentra sul **formato in sé**.

---

## 1. Perché HDF5

**HDF5** (Hierarchical Data Format v5) è lo standard di fatto per i dati scientifici
"big data". È stato scelto per i risultati strutturali perché:

- **Gerarchico**: organizza i dati in gruppi e dataset come un filesystem interno
  (`/static`, `/modal`, `/buckling`).
- **Array nativi compressi**: le forme modali $(n_{dof} \times n_{modi})$ e i
  diagrammi di azioni interne $(n_{elem} \times n_{punti})$ sono memorizzati come
  array binari con compressione gzip — molto più efficiente di tabelle Excel.
- **Metadati (attributi)**: ogni gruppo porta con sé le proprie informazioni
  (load case, section group, numero di modi).
- **Interoperabile**: leggibile da Python, MATLAB, Julia, R, C/C++, Java senza
  dipendere da beamfeapy.
- **Accesso parziale**: si può leggere un singolo dataset senza caricare l'intero
  file in memoria (utile per archivi di molte analisi).

---

## 2. Struttura del file

Un file scritto da `io_hdf5.write_results(res, "demo.h5", n_diagram=11,
modal=modal, buckling=buck)` ha questa struttura (dump reale, modello 4 nodi /
3 elementi / 24 DOF, 4 modi, 3 modi di buckling):

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

I tre gruppi `/static`, `/modal`, `/buckling` sono **indipendenti**: un file può
contenerne uno, due o tutti e tre.

### Convenzioni dei dati

| Elemento | Convenzione |
|----------|-------------|
| `U`, `R` | vettori globali di lunghezza `ndof`, ordine GdL `[ux,uy,uz,rx,ry,rz]` per nodo |
| ordine nodi | `node_ids` ordinati crescenti; il GdL globale del nodo $p$ è `[6p … 6p+5]` |
| `element_forces` | riga = elemento (stesso ordine di `element_ids`), 12 forze d'estremità locali |
| `phi` | colonna = modo, riga = GdL globale |
| `cases` | stringa JSON del dict `{case: coefficiente}` (vuota = None) |

---

## 3. Ispezionare un file HDF5

### Da riga di comando (strumenti HDF5)

Se hai installato gli strumenti HDF5 (`apt install hdf5-tools` / `brew install hdf5`):

```bash
h5ls -r demo.h5            # elenco ricorsivo di gruppi e dataset
h5dump -a / demo.h5        # attributi della root
h5dump -d /modal/freq demo.h5   # contenuto di un singolo dataset
```

### Da Python con h5py (senza beamfeapy)

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

### Da beamfeapy (lettura strutturata)

```python
from beamfeapy import io_hdf5
data = io_hdf5.read_results("demo.h5")    # dict di array
out  = io_hdf5.read_results("demo.h5", model=m)   # oggetti Result
```

---

## 4. Compressione

Tutti i dataset usano **gzip livello 4** (`compression="gzip",
compression_opts=4`), un buon compromesso tra rapporto di compressione e velocità.

Per dati FEM (molti zeri nelle forme modali e nei diagrammi) il guadagno è
significativo. Esempio su palazzo 3D (288 DOF, 12 modi + 6 buckling + diagrammi):

| Contenuto | Dimensione |
|-----------|-----------|
| HDF5 compresso (gzip-4) | ~110 KB |
| Stesso contenuto Excel (solo statico) | ~99 KB |

> L'HDF5 contiene **molti più dati** dell'Excel (modale + buckling + tutte le forme
> proprie) a parità di dimensione, grazie alla compressione e allo storage binario.

---

## 5. Interoperabilità con altri linguaggi

Il formato è leggibile da qualsiasi linguaggio con supporto HDF5, **senza
beamfeapy**. I percorsi dei dataset sono stabili.

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

> **Nota:** MATLAB legge gli array HDF5 in ordine column-major, quindi `phi`
> risulta trasposto rispetto a NumPy: `(n_modi × ndof)`. Usa `phi'` per riallineare.

### Julia

```julia
using HDF5

h5open("demo.h5", "r") do f
    freq = read(f, "modal/freq")
    phi  = read(f, "modal/phi")
    ndof = read_attribute(f, "ndof")
end
```

### Python puro (NumPy + h5py)

Vedi sezione 3. Nessuna dipendenza da beamfeapy: bastano `h5py` e `numpy`.

---

## 6. Versionamento del formato

L'attributo di root `format_version` (attualmente **1**) consente l'evoluzione
del formato mantenendo la retrocompatibilità. Un lettore può controllarlo:

```python
with h5py.File(path, "r") as h5:
    assert h5.attrs["format"] == "beamfeapy-results"
    if h5.attrs["format_version"] > 1:
        print("Attenzione: file scritto da una versione più recente.")
```

---

## 7. Pattern utili

### Salvare più analisi nello stesso file

Il parametro `mode` controlla l'apertura del file (`"w"` sovrascrive, `"a"`
aggiunge/aggiorna):

```python
res.to_hdf5("archivio.h5", mode="w")              # crea con la statica
modal.to_hdf5("archivio.h5", mode="a")            # aggiunge /modal
buck.to_hdf5("archivio.h5", mode="a")             # aggiunge /buckling
```

> Equivale a `io_hdf5.write_results(res, "archivio.h5", modal=modal,
> buckling=buck)` in una sola chiamata.

### Leggere solo ciò che serve (file grandi)

```python
import h5py
with h5py.File("archivio.h5", "r") as h5:
    # carica solo le frequenze, non le forme modali (potenzialmente enormi)
    freq = h5["/modal/freq"][()]
    # carica una sola colonna (un modo) di phi
    modo0 = h5["/modal/phi"][:, 0]
```

### Estrarre i diagrammi di un elemento

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

## 8. Limiti e note

- L'HDF5 salva i **risultati**, non il modello. Per ricostruire gli oggetti
  `Result` con i loro metodi (`displacement`, `mode_shape`, …) serve passare il
  modello: `io_hdf5.read_results(path, model=m)`. Senza modello si ottengono solo
  gli array grezzi.
- I **diagrammi** di azioni interne (`/static/internal_forces`) sono presenti solo
  se la scrittura è avvenuta con `n_diagram > 1`.
- Il section group e i load case sono salvati come metadati (attributi), non come
  modello completo.

---

*Vedi anche:*
[22 - Salvataggio risultati (Excel e HDF5)](it-22-saving-results.html) |
[11 - Excel I/O](it-11-excel-io.html) |
[19 - Analisi Modale](it-19-modal-analysis.html)
