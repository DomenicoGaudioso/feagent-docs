---
layout: default
title: "06 - Timoshenko e Rilasci"
parent: Italiano
nav_order: 6
---

# 06 - Timoshenko e Rilasci

## Elemento di Timoshenko (deformabilità a taglio)

Per travi tozze (rapporto L/h basso) il contributo del taglio non è trascurabile. L'elemento Timoshenko include i parametri di taglio `Φ_y` e `Φ_z`:

```python
sec = Section(A=0.18, Iy=5e-3, Iz=2e-3, J=3e-3,
              Asy=5/6*0.18, Asz=5/6*0.18)  # aree di taglio efficaci
m.add_beam(id, ni, nj, mat, sec, shear=True)
```

**Per una sezione rettangolare**: `Asy = Asz = 5/6 · A` (fattore di taglio).

Per travi snelle (L/h grande), `shear=False` (default) e Timoshenko → Eulero-Bernoulli.

## Rilasci di estremità (cerniere)

I rilasci eliminano la rigidezza per specifici GdL all'estremità dell'elemento tramite **condensazione statica**. Sono ideali per modellare cerniere e giunti.

```python
# Cerniera flessionale (Mz=0) all'estremo j
m.add_beam(id, ni, nj, mat, sec, releases_j=["rz"])

# Cerniera a entrambi gli estremi
m.add_beam(id, ni, nj, mat, sec, releases_i=["rz"], releases_j=["rz"])

# Svincoli multipli (es. giunto sferico con traslazione libera)
m.add_beam(id, ni, nj, mat, sec, releases_i=["uy", "uz"])
```

**GdL ammessi**: `ux`, `uy`, `uz`, `rx`, `ry`, `rz` (coordinate locali).

**Attenzione**: un GdL rilasciato deve avere rigidezza da almeno un altro elemento connesso al nodo, altrimenti la matrice globale diventa singolare.

### Esempio: trave continua con cerniera al nodo centrale

```python
# Elemento 1: cerniera al j-end (nodo centrale)
m.add_beam(1, 1, 2, mat, sec, releases_j=["rz"])
# Elemento 2: continuo (nessun rilascio)
m.add_beam(2, 2, 3, mat, sec)
```

### Esempio illustrato: mensola tozza Timoshenko (L/h = 2)

Deformata con contributo del taglio (freccia ~20% maggiore rispetto a EB):

![](images/cs4_deformata.png)

Il diagramma del momento Mz mostrerà M=0 all'estremo j dell'elemento 1 (nodo 2 dal lato sinistro), che è esattamente l'effetto della cerniera.