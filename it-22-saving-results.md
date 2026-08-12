---
layout: default
title: "22 - Salvataggio dei Risultati"
parent: Italiano
nav_order: 22
---

# 22 - Salvataggio dei risultati (Excel e HDF5)

beamfeapy offre **due formati** per salvare e rileggere i risultati delle analisi:

| Formato | Estensione | Uso tipico | Dipendenza |
|---------|-----------|------------|------------|
| **Excel** | `.xlsx` | Report tabellari, condivisione, ispezione manuale | `pandas`, `openpyxl` |
| **HDF5** | `.h5` | Big data: tante analisi, forme modali e diagrammi, accesso rapido | `h5py` |

```bash
pip install beamfeapy[excel]   # per Excel
pip install beamfeapy[hdf5]    # per HDF5
pip install beamfeapy[all]     # entrambi + plot
```

L'Excel è ideale per i **risultati statici** in forma tabellare; l'HDF5 (formato
gerarchico compresso, stile WOBridge) è pensato per salvare in **un unico file**
risultati statici, modali e di buckling, comprese le grandi matrici delle forme
proprie (`ndof × n_modi`) e i diagrammi di azioni interne per ogni elemento.

---

## 1. Excel (.xlsx)

### Salvare i risultati statici

```python
res = m.solve(cases="G")
res.to_excel("risultati.xlsx", n_diagram=21)
```

Fogli generati:

| Foglio | Contenuto |
|--------|-----------|
| `Displacements` | spostamenti nodali `[ux,uy,uz,rx,ry,rz]` |
| `Reactions` | reazioni vincolari `[Fx,Fy,Fz,Mx,My,Mz]` |
| `ElementEndForces` | forze d'estremità locali (12 per elemento) |
| `InternalForces` | diagrammi azioni interne (se `n_diagram > 1`) |

### Rileggere i risultati statici

```python
from beamfeapy import read_results_excel

data = read_results_excel("risultati.xlsx")
data["displacements"][3]      # array(6,) spostamenti nodo 3
data["reactions"][1]          # array(6,) reazioni nodo 1
data["element_forces"][2]     # array(12,) forze elemento 2
data["internal_forces"]       # DataFrame (se presente il foglio)
```

> L'Excel salva **solo i risultati statici**. Per modale e buckling usa HDF5.

---

## 2. HDF5 (.h5) — formato big data

### Salvare (tutto in un file)

```python
res   = m.solve(cases="G")
modal = m.modal(n_modes=12, mass_source={"G": 1.0})
buck  = m.buckling(n_modes=6, cases="G")

# Metodo 1: dal Result, includendo modale e buckling
res.to_hdf5("out.h5", n_diagram=21, modal=modal, buckling=buck)

# Metodo 2: dal modulo
from beamfeapy import io_hdf5
io_hdf5.write_results(res, "out.h5", n_diagram=21,
                      modal=modal, buckling=buck)
```

Anche i singoli risultati hanno il proprio `to_hdf5`:

```python
modal.to_hdf5("solo_modale.h5")
buck.to_hdf5("solo_buckling.h5")
```

### Rileggere

```python
from beamfeapy import io_hdf5

# (a) array grezzi — non serve il modello
data = io_hdf5.read_results("out.h5")
data["modal"]["freq"]               # frequenze
data["static"]["U"]                 # spostamenti globali
data["static"]["internal_forces"]["Mz"]   # (n_elem, n_diagram)
data["buckling"]["load_factors"]

# (b) oggetti Result collegati al modello (con i metodi soliti)
out = io_hdf5.read_results("out.h5", model=m)
res2   = out["static"]      # Result
modal2 = out["modal"]       # ModalResult
buck2  = out["buckling"]    # BucklingResult

res2.displacement(13, "uy")
modal2.mass_participation()
buck2.mode_shape(0, node=13)
```

La funzione è anche esposta come `beamfeapy.read_results_hdf5`.

### Struttura del file HDF5

```
/                       attrs: format, format_version, ndof, n_nodes, n_elements
  node_ids              (n_nodes,)
  element_ids           (n_elements,)

  /static               attrs: cases, section_group
    U                   (ndof,)            spostamenti globali
    R                   (ndof,)            reazioni globali
    element_forces      (n_elem, 12)       forze d'estremità locali
    /internal_forces                        [se n_diagram > 1]
      x, N, Vy, Vz, T, My, Mz   (n_elem, n_diagram)

  /modal                attrs: section_group, n_modes
    omega, freq, period (n_modi,)
    phi                 (ndof, n_modi)     forme modali
    eff_mass, part      (n_modi, 3)
    total_mass          (3,)

  /buckling             attrs: cases, section_group, n_modes
    load_factors        (n_modi,)
    phi                 (ndof, n_modi)     forme di instabilità
```

I gruppi `/static`, `/modal`, `/buckling` sono **indipendenti**: puoi salvarli
insieme o separatamente. Tutti i dataset sono compressi con gzip.

---

## 3. Excel vs HDF5: quando usare cosa

| Criterio | Excel | HDF5 |
|----------|-------|------|
| Ispezione manuale | ✅ apri con Excel/LibreOffice | ❌ formato binario |
| Modale + buckling | ❌ solo statico | ✅ tutto in un file |
| Forme modali (ndof × n_modi) | ❌ pesante/lento | ✅ array nativi compressi |
| Modelli grandi (migliaia di DOF) | ⚠️ lento | ✅ veloce, compresso |
| Interoperabilità con altri linguaggi | media | ✅ HDF5 standard (MATLAB, C++, Julia) |
| Dipendenza | pandas + openpyxl | h5py |

**Regola pratica:** Excel per report e risultati statici da condividere; HDF5
per archiviare l'intera analisi (statica + modale + buckling) o per modelli grandi.

---

## 4. Esempio completo: palazzo 3D

```python
from beamfeapy import Material, Model, Section, io_hdf5

# ... costruzione del palazzo 3D (vedi pagina 20) ...

res   = m.solve(cases="G")
modal = m.modal(n_modes=12, mass_source={"G": 1.0})
buck  = m.buckling(n_modes=6, cases="G")

# Salva tutto in un file HDF5 (statica + 12 modi + 6 modi buckling + diagrammi)
io_hdf5.write_results(res, "palazzo.h5", n_diagram=21,
                      modal=modal, buckling=buck)

# Rilettura completa
out = io_hdf5.read_results("palazzo.h5", model=m)
print("f1 =", out["modal"].freq[0], "Hz")
print("lambda_cr =", out["buckling"].load_factors[0])
```

Su un palazzo di 48 nodi / 87 elementi / 288 DOF, il file HDF5 con statica + 12
modi + 6 modi di buckling + diagrammi a 21 punti pesa circa **110 KB**.

---

*Vedi anche:*
[11 - Excel I/O](it-11-excel-io.html) |
[19 - Analisi Modale](it-19-modal-analysis.html) |
[25 - Palazzo 3D](it-25-palazzo-3d.html)
