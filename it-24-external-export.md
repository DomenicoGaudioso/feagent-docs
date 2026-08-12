---
layout: default
title: "24 - Export Software Esterni"
parent: Italiano
nav_order: 24
---

# 24 - Export verso software di calcolo esterni

beamfeapy può **esportare il modello** verso i principali software di calcolo
strutturale, generando file di testo importabili.

| Software | Funzione | Estensione | Stato |
|----------|----------|-----------|-------|
| **OpenSees** (TCL) | `to_opensees_tcl` | `.tcl` | completo |
| **OpenSeesPy** (Python) | `to_openseespy` | `.py` | completo |
| **SAP2000** | `to_sap2000_s2k` | `.s2k` | completo |
| **MIDAS** Civil/Gen | `to_midas_mct` | `.mct` | completo |
| **Robot** (RSA) | `to_robot_str` | `.str` | best-effort* |
| **Straus7** / Strand7 | `to_straus7_txt` | `.txt` | best-effort* |

> *Robot e Straus7: la struttura segue il formato testo documentato, ma alcune
> parole chiave possono variare con versione/lingua. Vedi §6.

---

## 1. Uso rapido

Dal modello (formato dedotto dall'estensione o esplicito):

```python
m.export("modello.tcl")                    # OpenSees TCL (da estensione)
m.export("modello.py",  fmt="openseespy")
m.export("modello.s2k", fmt="sap2000")
m.export("modello.mct", fmt="midas")
m.export("modello.str", fmt="robot")
m.export("modello.txt", fmt="straus7")
```

Dal modulo `export`:

```python
from beamfeapy import export

export.export(m, "modello.tcl", fmt="opensees_tcl")
export.to_openseespy(m, "modello.py")
export.to_sap2000_s2k(m, "modello.s2k")
```

### Cosa viene esportato

| Entità | OpenSees | SAP2000 | MIDAS | Robot | Straus7 |
|--------|:--------:|:-------:|:-----:|:-----:|:-------:|
| Nodi | ✅ | ✅ | ✅ | ✅ | ✅ |
| Materiali | ✅ (inline) | ✅ | ✅ | ✅ | ✅ |
| Sezioni | ✅ (inline) | ✅ General | ✅ Value | ✅ | ✅ |
| Elementi (assi locali) | ✅ | ✅ | ✅ | ✅ | ✅ |
| Vincoli | ✅ | ✅ | ✅ | ✅ | ✅ |
| Cedimenti imposti | — | — | — | — | ✅ |
| Rilasci (cerniere) | nota | ✅ | — | ✅ | ✅ |
| Carichi nodali | ✅ | ✅ | ✅ | ✅ | ✅ |
| Carichi distribuiti | ✅** | ✅ | ✅ | ✅ | ✅ |

> **OpenSees `eleLoad -beamUniform` è uniforme: i carichi trapezoidali/parziali
> sono esportati con il valore medio equivalente (con nota nel file).

---

## 2. OpenSees (TCL e OpenSeesPy)

L'elemento usato è **`elasticBeamColumn`**, trave 3D di Eulero-Bernoulli a 12 GdL
— corrispondenza **esatta** con beamfeapy.

### Esempio TCL generato

```tcl
wipe
model basic -ndm 3 -ndf 6

# --- nodi ---
node 1 0 0 0
node 2 0 4 0
...
# --- trasformazioni geometriche (vecxz = local_z) ---
geomTransf Linear 1 0 0 -1
# --- elementi: elasticBeamColumn eid ni nj A E G J Iy Iz transfTag ---
element elasticBeamColumn 1 1 2 0.012 2.1e+11 8.08e+10 0.0002 4e-05 8e-05 1
# --- carichi: case 'G' ---
timeSeries Linear 1
pattern Plain 1 1 {
    eleLoad -ele 2 -type -beamUniform -8000 0 0
}
```

### Assi locali: corrispondenza esatta

Il punto delicato è l'orientazione della sezione. beamfeapy esporta per ogni
elemento il versore **`local_z` (ez)** come vettore `vecxz` della `geomTransf`.
In OpenSees vale `local_y = vecxz × local_x`; poiché in beamfeapy il sistema è
destrorso (`ez = ex × ey`, quindi `ez × ex = ey`), la trasformazione riproduce
**esattamente** gli stessi assi locali — qualunque sia l'orientazione impostata
(`ref_vector`, `set_axes`, default). Questo è verificato dai test.

```python
m.export("modello.tcl")                # per OpenSees Tcl
m.export("modello.py", fmt="openseespy")  # per Python

# tipo di trasformazione geometrica
m.export("modello.tcl", transf="PDelta")        # o "Corotational"
```

Lo script **OpenSeesPy** generato è Python eseguibile (include `system`,
`numberer`, `analysis` e `analyze(1)` per una statica lineare).

---

## 3. SAP2000 (.s2k)

File di testo a tabelle (`TABLE: "..."`), importabile da
**File > Import > SAP2000 .s2k Text File**.

```
TABLE:  "JOINT COORDINATES"
   Joint=1   CoordSys="GLOBAL"   CoordType="Cartesian"   XorR="0"   Y="0"   Z="0"

TABLE:  "FRAME SECTION PROPERTIES 01 - GENERAL"
   SectionName="SEC1"   Material="MAT1"   Shape="General"
   Area="0.012"   TorsConst="0.0002"   I33="8e-05"   I22="4e-05"

TABLE:  "CONNECTIVITY - FRAME"
   Frame=1   JointI=1   JointJ=2
```

Le sezioni sono esportate come **General** (proprietà numeriche dirette: area,
J, I22=Iy, I33=Iz). L'orientazione usa l'angolo dell'asse locale 2.

---

## 4. MIDAS Civil/Gen (.mct)

File di comandi **MIDAS Command Text**, importabile dalla *MCT Command Shell*.

```
*NODE    ; iNO, X, Y, Z
   1, 0, 0, 0
   2, 0, 4, 0

*MATERIAL    ; iMAT, TYPE, MNAME, ...
   1, ELAST, MAT1, ..., 2.1e+11, 0.3, 0, 0

*SECTION    ; iSEC, TYPE, SNAME, ..., Area, Asy, Asz, Ixx, Iyy, Izz
   1, VALUE, SEC1, ..., 0.012, 0, 0, 0.0002, 4e-05, 8e-05

*ELEMENT    ; iEL, TYPE, iMAT, iPRO, iN1, iN2, ANGLE
   1, BEAM, 1, 1, 1, 2, 180
```

Le sezioni usano il tipo **VALUE** (proprietà numeriche). L'angolo beta orienta
la sezione attorno all'asse barra.

---

## 5. Esportare con tutte le entità

L'esportatore include nodi, materiali, sezioni, elementi (con assi locali),
vincoli, rilasci, carichi nodali e distribuiti, organizzati per load case:

```python
m = Model()
# ... geometria, sezioni, vincoli ...
m.add_distributed_load(2, "fy", -8000., case="G")
m.add_nodal_load(2, Fx=5000., case="SX")
m.add_beam(3, 3, 4, mat, sec, releases_j=["rz"])   # cerniera

m.export("modello.s2k", fmt="sap2000")   # esporta tutto, per load case
```

I load case diventano:
- OpenSees: `pattern Plain` distinti per case
- SAP2000: `LOAD PATTERN DEFINITIONS` + `JOINT/FRAME LOADS`
- MIDAS: `*STLDCASE` + `*CONLOAD`/`*BEAMLOAD`

---

## 6. Robot e Straus7 (best-effort)

Robot (`.STR`) e Straus7 (testo) **hanno** un formato di import testuale, ma la
sintassi esatta è descritta in documenti proprietari distribuiti con i programmi
(`R97mod01.doc` per Robot; reference Strand7). Gli esportatori producono una
struttura coerente con il formato documentato e includono **tutti i dati** del
modello, ma vanno **validati con un file di esempio reale**.

```python
m.export("modello.str", fmt="robot")      # Robot RSA
m.export("modello.txt", fmt="straus7")    # Straus7 / Strand7
```

**Per affinare il mapping:** esporta un piccolo modello (2-3 barre) dalla tua
installazione di Robot (`.STR`) / Straus7 (Strand7 Text File) e confronta i nomi
di blocchi e campi — bastano piccole modifiche per allinearli al 100%.

---

## 7. Limitazioni note

- I **carichi termici** e la **precompressione** non sono ancora esportati
  (geometria + carichi meccanici sì).
- I **profili termici** generici (`ThermalProfile`) sono ignorati.
- I carichi distribuiti **trapezoidali/parziali** in OpenSees sono approssimati
  con il valore medio uniforme (gli altri formati li supportano nativamente).
- Materiali e sezioni identici vengono **deduplicati** automaticamente.

---

*Vedi anche:*
[08 - Orientazione Sezione](it-08-section-orientation.html) |
[15 - Testing e Validazione](it-15-testing-validation.html) |
[11 - Excel I/O](it-11-excel-io.html)
