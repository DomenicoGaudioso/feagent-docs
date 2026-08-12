---
layout: default
title: "08 - Orientazione della Sezione"
parent: Italiano
nav_order: 8
---

# 08 - Orientazione della Sezione

In un modello 3D, l'orientazione della sezione trasversale determina quali assi
dell'elemento corrispondono a `Iy` e `Iz` e quindi in quale direzione si piega
l'elemento sotto un dato carico.

---

## Assi locali — convenzione standard (SAP2000 / Przemieniecki)

Per un elemento che va dal nodo i al nodo j, beamfeapy usa la **formula di proiezione**:

```
local_x = normalize(j - i)                                    (asse della trave)
local_y = normalize( ref_vector − (ref_vector · local_x) · local_x )  (piano x-y)
local_z = local_x × local_y                                   (destrorso)
```

`ref_vector` definisce direttamente la **direzione di `local_y`** (proiettata
perpendicolarmente all'asse della trave).

**Proprieta' di sezione:**

| Inerzia | Piano di flessione | Direzione forza | Momento |
|---------|--------------------|-----------------|---------|
| `Iz` | x-y locale | `'fy'` | `Mz` |
| `Iy` | x-z locale | `'fz'` | `My` |

---

## Scelta automatica del ref_vector (default)

Se non si specifica `ref_vector`:

| Tipo elemento | Check | ref_vector usato | local_y risultante |
|--------------|-------|-----------------|-------------------|
| Non verticale (`\|ex · Y\| < 0.999`) | travi orizzontali | `(0, 1, 0)` | **Y globale** |
| Verticale (`\|ex · Y\| ≥ 0.999`) | colonne | `(1, 0, 0)` | **X globale** |

```python
m.add_beam(1, 1, 2, mat, sec)   # default: local_y = Y globale per travi orizzontali
```

**Conseguenza pratica:** per travi orizzontali (in qualunque direzione del piano XZ),
il default da' sempre `local_y = Y globale`, quindi:
- Carichi gravitazionali: componente `'fy'`
- `Iz` governa la flessione principale (asse forte)

---

## ref_vector esplicito

Passare `ref_vector` quando si vuole controllare precisamente l'orientamento.

### Travi in qualunque direzione orizzontale

```python
# Trave lungo X — default (None) equivale a ref_vector=(0,1,0)
m.add_beam(1, 1, 2, mat, sec)
# local_x=(1,0,0), local_y=(0,1,0), local_z=(0,0,1)

# Trave lungo Z — default funziona ugualmente
m.add_beam(2, 3, 4, mat, sec)
# local_x=(0,0,1), local_y=(0,1,0), local_z=(-1,0,0)
```

Entrambe le travi hanno `local_y = Y globale` con il default. `'fy' = -q` e'
il carico gravitazionale per entrambe.

### Colonne verticali

```python
# Colonna con asse forte in sway X (Iz = asse forte, piano x-y)
m.add_beam(3, 5, 6, mat, col_sec, ref_vector=(1, 0, 0))
# local_x=(0,1,0), local_y=(1,0,0)=X, local_z=(0,0,-1)=-Z
# Iz governa sway in X, Iy governa sway in Z

# Ruotata 90 gradi: asse forte in sway Z
m.add_beam(4, 7, 8, mat, col_sec, ref_vector=(0, 0, 1))
# local_x=(0,1,0), local_y=(0,0,1)=Z, local_z=(1,0,0)=X
# Iz governa sway in Z, Iy governa sway in X
```

### Trave inclinata

```python
# Trave con inclinazione generica
m.add_beam(5, 9, 10, mat, sec, ref_vector=(0, 1, 0))
# local_y = proiezione di (0,1,0) perp all'asse inclinato
# Ha sempre componente Y positiva → bending "verso l'alto"
```

---

## Definizione delle inerzie (sezioni standard)

Con la convenzione SAP2000 (`Iz` = asse forte per travi orizzontali):

```python
from beamfeapy import Section

# IPE 300 — trave orizzontale, gravita' in local_y='fy'
# local_z = orizzontale (fuori-piano) → Iy = asse debole
sec_bx = Section(
    A  = 53.8e-4,
    Iy = 604e-8,    # asse debole (bending laterale, fz)
    Iz = 8356e-8,   # asse forte  (bending gravitazionale, fy)
    J  = 20.1e-8,
)

# IPE 240 — trave orizzontale (stessa logica)
sec_bz = Section(
    A  = 39.1e-4,
    Iy = 284e-8,
    Iz = 3892e-8,
    J  = 12.9e-8,
)

# HEA 240 — colonna verticale con ref=(1,0,0)
# local_y = X globale → Iz governa sway X, Iy governa sway Z
sec_col = Section(
    A  = 76.8e-4,
    Iy = 2769e-8,   # debole (sway Z)
    Iz = 7763e-8,   # forte  (sway X)
    J  = 41.5e-8,
)
```

> **Tabella di riferimento Eurocodice vs beamfeapy:**
> 
> | Sezione | I_y EC (forte) | I_z EC (debole) | beamfeapy Iz (forte) | beamfeapy Iy (debole) |
> |---------|---------------|----------------|---------------------|----------------------|
> | IPE 300 | 8356 cm⁴ | 604 cm⁴ | 8356e-8 m⁴ | 604e-8 m⁴ |
> | IPE 240 | 3892 cm⁴ | 284 cm⁴ | 3892e-8 m⁴ | 284e-8 m⁴ |
> | HEA 240 | 7763 cm⁴ | 2769 cm⁴ | 7763e-8 m⁴ | 2769e-8 m⁴ |
> 
> Nel Eurocodice `I_y` e' l'asse forte (asse y-y dei profili standard = bending nel
> piano delle ali). In beamfeapy con `ref=(0,1,0)`, l'asse forte diventa `Iz`
> perche' `local_y = Y globale` fa si' che la flessione gravitazionale (fy) avvenga
> nel piano x-y, governata da `Iz`.

---

## Roll angle

`roll` ruota la sezione attorno all'asse locale x (in radianti), dopo il calcolo
del ref_vector. Utile per sezioni ruotate rispetto all'orientamento standard:

```python
import numpy as np

# HEA 240 colonna ruotata di 45 gradi attorno al proprio asse
m.add_beam(1, 1, 2, mat, col_sec, roll=np.radians(45))
```

> `roll` e' ignorato se `ref_vector` e' specificato esplicitamente.

---

## Ispezione degli assi

Per verificare l'orientamento assegnato a un elemento:

```python
el = m.elements[eid]
R  = el.rotation_matrix()         # righe = [local_x, local_y, local_z] in globale
ex, ey, ez = R[0], R[1], R[2]
print('local_x =', ex)
print('local_y =', ey)   # <- dev'essere Y globale per travi orizzontali con default
print('local_z =', ez)

# Oppure tramite il metodo statico
ex2, ey2, ez2 = el._local_axes(ex, ref)
```

---

## Esempio: edificio 3D completo

```python
# Modello palazzo: 3 piani, 3x2 campate, acciaio S275
NX, NZ, NF = 4, 3, 3
LX, LZ, H  = 5.0, 4.0, 3.5

# Colonne: ref=(1,0,0) → local_y = X globale
for f in range(NF):
    for iz in range(NZ):
        for ix in range(NX):
            m.add_beam(eid, nid(f,ix,iz), nid(f+1,ix,iz),
                       mat, sec_col, ref_vector=(1., 0., 0.))

# Travi X e Z: default ref → local_y = Y globale in entrambe le direzioni
for f in range(1, NF+1):
    for iz in range(NZ):
        for ix in range(NX-1):
            m.add_beam(eid, nid(f,ix,iz), nid(f,ix+1,iz), mat, sec_bx)
    for ix in range(NX):
        for iz in range(NZ-1):
            m.add_beam(eid, nid(f,ix,iz), nid(f,ix,iz+1), mat, sec_bz)

# Carichi gravitazionali: 'fy' per tutte le travi orizzontali
for e in bx_eids:
    m.add_distributed_load(e, 'fy', -20_000., case='G')
for e in bz_eids:
    m.add_distributed_load(e, 'fy', -15_000., case='G')
```

Vedi il caso studio completo: [25 - Palazzo 3D](it-25-palazzo-3d.html).

---

## Riepilogo

| Scenario | ref_vector | Carico gravita' | Inerzia forte |
|----------|-----------|-----------------|---------------|
| Trave X o Z (default) | `None` | `'fy'` | `Iz` |
| Trave con orientazione personalizzata | `(rx, ry, rz)` | dipende | dipende |
| Colonna verticale, forte in X | `(1, 0, 0)` | — | `Iz` (sway X) |
| Colonna verticale, forte in Z | `(0, 0, 1)` | — | `Iz` (sway Z) |
| Sezione ruotata | `roll=angolo` | `'fy'` | ruotata |
