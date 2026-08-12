---
layout: default
title: "12 - Solver Sparso"
parent: Italiano
nav_order: 12
---

# 12 - Solver Sparso

Per modelli con molti gradi di libertà, il solver sparso (COO→CSR + SuperLU) è molto più efficiente del solver denso.

## Quando usarlo

| GdL | Denso | Sparso |
|-----|-------|--------|
| < 500 | OK | overhead |
| 500–5000 | lento | raccomandato |
| > 5000 | esaurimento memoria | necessario |

## Utilizzo

```python
res = m.solve(sparse=True)
```

I risultati sono **identici** a quelli del solver denso (a meno della precisione numerica).

## Verifica

```python
rd = m.solve(sparse=False)
rs = m.solve(sparse=True)
import numpy as np
assert np.allclose(rd.U, rs.U, atol=1e-10)
```

## Implementazione

L'assemblaggio sparso segue l'approccio standard (Cuvelier, Japhet & Scarella, BIT 2016):
1. Per ogni elemento si generano terne `(riga, colonna, valore)` della matrice 12×12
2. Le terne di tutti gli elementi vengono concatenate in una `scipy.sparse.coo_matrix`
3. La conversione a CSR somma automaticamente i contributi sui medesimi indici

Questo evita sia l'allocazione della matrice densa `ndof × ndof` sia lo scatter elemento per elemento.

## Ottimizzazioni del solutore

beamfeapy adotta diverse tecniche per avvicinarsi alla velocità dei solutori
compilati (vedi [29 - Prestazioni e Benchmark](it-29-performance.html)):

- **Forme chiuse per i carichi distribuiti**: le forze nodali equivalenti di
  carichi uniformi/trapezoidali su tutto l'elemento sono calcolate con formule
  esatte di Hermite, senza quadratura di Gauss (risultato identico).
- **Assemblaggio vettorizzato in batch**: le rotazioni `Kᵍ = Tᵀ K T` di tutti
  gli elementi sono eseguite in due `np.matmul` su array `(ne, 12, 12)`.
- **Cache geometriche** (vettore d'asse, matrici `R`/`T`, `K` locale) con chiave
  sui valori: si invalidano da sole al cambio di geometria o sezione.
- **Più combinazioni con una sola fattorizzazione** — `Model.solve_many`:

  ```python
  results = m.solve_many({"SLU": {"G": 1.35, "Q": 1.5}, "SLE": {"G": 1, "Q": 1}})
  ```

- **pypardiso opzionale** (MKL Pardiso): se installato (`pip install beamfeapy[fast]`)
  viene usato automaticamente come solutore sparso al posto di SuperLU.

## Analisi modale e di buckling sparse

Anche modale e buckling hanno un percorso sparso (ARPACK, attivo di default sui
modelli grandi) che calcola solo i modi richiesti senza fattorizzare matrici dense:

```python
modal = m.modal(n_modes=6, mass_source={"G": 1.0}, sparse=True)   # eigsh shift-invert
buck  = m.buckling(n_modes=4, cases="G", sparse=True)              # eigsh generalizzato
```

I risultati coincidono con il percorso denso; la modale è oltre 25× più rapida e
il buckling fino a ~160× (era basato su un `eig` non-simmetrico denso). Tutte le
tabelle e i grafici di confronto sono nella pagina
[29 - Prestazioni e Benchmark](it-29-performance.html).