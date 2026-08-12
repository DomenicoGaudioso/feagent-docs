---
layout: default
title: "03 - Modello Strutturale"
parent: Italiano
nav_order: 3
---

# 03 - Modello Strutturale

## Nodi

Ogni nodo ha 6 gradi di libertà (GdL): `[ux, uy, uz, rx, ry, rz]`.

```python
m.add_node(id, x, y, z)   # id = identificativo intero, coordinate in metri
```

Esempio:
```python
m.add_node(1, 0, 0, 0)    # origine
m.add_node(2, 5, 0, 0)    # 5 m lungo X
m.add_node(3, 5, 3, 0)    # 5 m in X, 3 m in Y (piano X-Y)
m.add_node(4, 0, 0, 4)    # 4 m in Z (verticale)
```

## Materiali

```python
mat = Material(E=210e9, nu=0.3, alpha=1.2e-5)   # acciaio
mat = Material(E=30e9, nu=0.2, alpha=1.0e-5)       # calcestruzzo
```

| Parametro | Descrizione | Default |
|-----------|-------------|---------|
| `E` | Modulo di Young [Pa] | obbligatorio |
| `nu` | Coefficiente di Poisson | 0.3 |
| `alpha` | Coeff. dilatazione termica [1/°C] | 0.0 |
| `G` | Modulo di taglio [Pa] | calcolato da E e nu |
| `rho` | Densità [kg/m³] | 0.0 (non usato) |

## Sezioni

```python
# Sezione rettangolare b×h = 0.30×0.50 m
b, h = 0.30, 0.50
sec = Section(A=b*h, Iy=h*b**3/12, Iz=b*h**3/12, J=b*h*(b**2+h**2)/12)
```

| Parametro | Descrizione | Obbligatorio |
|-----------|-------------|--------------|
| `A` | Area trasversale | sì |
| `Iy` | Momento d'inerzia attorno ad y (flessione x-z) | sì |
| `Iz` | Momento d'inerzia attorno a z (flessione x-y) | sì |
| `J` | Costante di torsione | sì |
| `Asy`, `Asz` | Aree di taglio efficaci (Timoshenko) | no |
| `h_y`, `h_z` | Altezze sezione (per carichi termici) | no |

**Convenzione**: `Iy` → flessione nel piano x-z, `Iz` → flessione nel piano x-y.
Per una sezione rettangolare b×h con asse forte orizzontale: `Iz = b·h³/12` (forte), `Iy = h·b³/12` (debole).

## Elementi

### Elemento prismatico (Eulero-Bernoulli / Timoshenko)

```python
m.add_beam(id, node_i, node_j, material, section, ...)
```

Parametri opzionali:
- `ref_vector` — vettore per l'orientazione della sezione (vedi [Orientazione](it-08-section-orientation.html))
- `roll` — angolo di rotazione della sezione [rad]
- `shear=True` — attiva la formulazione Timoshenko (richiede Asy, Asz)
- `releases_i`, `releases_j` — lista di GdL svincolati (vedi [Rilasci](it-06-timoshenko-releases.html))

### Elemento a sezione variabile (tapered)

```python
from beamfeapy import VariableSection

# Metodo 1: funzione continua
vs = VariableSection.rectangular(b=0.30, h=lambda xi: 0.70*(1-0.6*xi))
m.add_tapered_beam(id, ni, nj, mat, vs)

# Metodo 2: sezioni agli estremi (interpolazione lineare)
m.add_section("root", A=1.5e-2, Iy=5e-5, Iz=9e-5, J=4e-5)
m.add_section("tip", A=0.7e-2, Iy=1.2e-5, Iz=2e-5, J=1e-5)
m.add_tapered_beam(id, ni, nj, mat, section_i="root", section_j="tip")

# Metodo 3: stazioni intermedie
m.add_tapered_beam(id, ni, nj, mat, stations={0.0: "root", 0.5: "mid", 1.0: "tip"})
```

Vedi la guida dedicata: [Sezione Variabile](it-05-tapered-section.html).

## Vincoli

```python
m.fix(node)                         # incastro: tutti i 6 GdL bloccati
m.pin(node)                          # cerniera sferica: ux, uy, uz bloccati
m.support(node, ux=True, uy=True)    # custom: solo i GdL specificati
```

| Metodo | GdL bloccati | Uso tipico |
|--------|---------------|------------|
| `fix(n)` | ux,uy,uz,rx,ry,rz | Incastro |
| `pin(n)` | ux,uy,uz | Cerniera sferica |
| `support(n,...)` | personalizzati | Appoggio a rulli, carrello, etc. |

Esempi di `support`:
```python
m.support(1, ux=True, uy=True, uz=True, rx=True)  # pin 3D (4 GdL)
m.support(2, uy=True, uz=True, rx=True)             # rulli (3 GdL, ux libero)
m.support(3, uy=True)                                # solo verticale (carrello)
```

## Soluzione

```python
res = m.solve()                   # solver denso (default)
res = m.solve(sparse=True)        # solver sparso (large modelli)
res = m.solve(cases=["G", "Q"])    # load case specifici
```

Vedi [Solver Sparso](it-12-sparse-solver.html) per dettagli sulle prestazioni.