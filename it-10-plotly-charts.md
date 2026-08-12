---
layout: default
title: "10 - Plotly e Grafici"
parent: Italiano
nav_order: 10
---

# 10 - Plotly e Grafici

beamfeapy fornisce 6 funzioni di visualizzazione interattiva basate su Plotly.

## Installazione

```bash
pip install beamfeapy[plot]
```

## Funzioni disponibili

### plot_model(m)

Struttura del modello (nodi, elementi, vincoli):

```python
from beamfeapy.plotting import plot_model
fig = plot_model(m)
fig.show()
```

### plot_loads(m, case=None)

Struttura + carichi applicati. Filtrabile per load case:

```python
from beamfeapy.plotting import plot_loads
plot_loads(m, case="G").show()    # solo carichi del caso G
plot_loads(m, case="Q").show()    # solo caso Q
plot_loads(m).show()              # tutti i carichi
```

| Caso G (distribuito) | Caso Q (vento + concentrato) |
|---|---|
| ![loads G](images/cs9_loads_G.png) | ![loads Q](images/cs9_loads_Q.png) |

### plot_diagram(result, component)

Diagramma delle sollecitazioni lungo la struttura:

```python
from beamfeapy.plotting import plot_diagram
for comp in ["N", "Vy", "Vz", "T", "My", "Mz"]:
    plot_diagram(res, comp).show()
```

Il diagramma è disegnato come **area riempita bicolore per segno** (con
trasparenza): per la flessione il **positivo** è celeste, il **negativo** rosso
chiaro; gli altri componenti usano coppie di colori dedicate. Ogni azione è
tracciata sul proprio **asse di utilizzo** (N condivide l'asse di My).

**Convenzione europea**: momento negativo all'estradosso (sul telaio: positivo
all'intradosso in campata, negativo all'estradosso agli appoggi).

![diagramma Mz](images/cs9_Mz.png)

### plot_deformed(result, scale=1.0)

Configurazione deformata con fattore di scala:

```python
from beamfeapy.plotting import plot_deformed
plot_deformed(res, scale=200).show()
```

![deformata](images/cs9_deformata.png)

### plot_reactions(result)

Reazioni vincolari (forze e momenti) ai vincoli:

```python
from beamfeapy.plotting import plot_reactions
plot_reactions(res).show()
```

![reazioni](images/cs9_reactions.png)

### plot_internal_forces(result, elem_id)

I 6 diagrammi (N, Vy, Vz, T, My, Mz) per un singolo elemento:

```python
from beamfeapy.plotting import plot_internal_forces
plot_internal_forces(res, elem_id=1).show()
```

## Salvataggio

```python
fig = plot_diagram(res, "Mz")
fig.write_html("diagram_Mz.html", include_plotlyjs="cdn")
fig.write_image("diagram_Mz.png", width=1200, height=600)  # richiede kaleido
```

## Esempio completo nel portale

```python
from beamfeapy import Material, Model, Section
from beamfeapy.plotting import plot_loads, plot_diagram, plot_deformed, plot_reactions

mat = Material(210e9, 0.3)
col = Section(A=0.16, Iy=2.13e-3, Iz=2.13e-3, J=3.6e-3)
bm = Section(A=0.18, Iy=1.35e-3, Iz=5.4e-3, J=2.0e-3)

m = Model()
m.add_node(1, 0, 0, 0); m.add_node(2, 0, 0, 4)
m.add_node(3, 6, 0, 4); m.add_node(4, 6, 0, 0)
m.add_beam(1, 1, 2, mat, col)
m.add_beam(2, 2, 3, mat, bm, ref_vector=(0, 0, 1))
m.add_beam(3, 3, 4, mat, col)
m.fix(1); m.fix(4)
m.add_distributed_load(2, "fy", -20e3, case="G")
m.add_nodal_load(2, Fx=30e3, case="Q")

res = m.solve(cases=["G", "Q"])

plot_loads(m, case="G").show()
plot_diagram(res, "Mz").show()
plot_deformed(res, scale=200).show()
plot_reactions(res).show()
```