---
layout: default
title: "15 - Testing e Validazione"
parent: Italiano
nav_order: 15
---

# 15 - Testing e Validazione

## Test automatici

Il progetto include una suite completa di test che verificano i risultati contro soluzioni analitiche e altri solutori FEM.

### Esecuzione

```bash
python -m pytest tests/ -v
```

### Struttura dei test

| File | Contenuto |
|------|-----------|
| `test_beam.py` | Test originali: mensola, trave appoggiata, termica, cedimenti, Timoshenko, carichi concentrati, precompressione, carichi distribuiti |
| `test_tapered.py` | Elemento a sezione variabile: rigidezza esatta, confronto con mesh fine, termica, Timoshenko tapered, carichi distribuiti |
| `test_io_excel.py` | Import/export Excel |
| `test_loadcases_plots.py` | Load case e test smoke delle funzioni di plot |
| `test_analytical_2d.py` | Soluzioni analitiche 2D: mensola, trave appoggiata, incastro-incastro, carichi triangolari, superposizione |
| `test_analytical_3d.py` | Soluzioni analitiche 3D: torsione, biassiale, elementi inclinati, termica, precompressione |
| `test_vs_opensees.py` | Cross-validation con OpenSeesPy (skippato se non disponibile) |
| `test_vs_pynite.py` | Cross-validation con PyNite (skippato se non disponibile) |
| `test_vs_anastruct.py` | Cross-validation con anastruct (2D) |
| `test_solver_consistency.py` | Sparse = dense, equilibrio globale, tapered vs prismatico, Timoshenko |

## Tipi di verifica

### Soluzioni analitiche esatte

- Trave appoggiata: `5qL⁴/384EI`
- Mensola: `PL³/3EI`, `PL²/2EI`, `PL/EA`
- Torsione: `TL/GJ`
- Dilatazione termica: `α·ΔT·L`
- Barra incastrata: `N = -EA·α·ΔT`

### Cross-validation con altri solutori

- **OpenSeesPy**: confronto spostamenti nodo per nodo su modelli 3D
- **PyNite**: confronto su mensola single e multi-elemento
- **anastruct**: confronto su modelli 2D (trave appoggiata, mensola, trave continua)

### Consistenza interna

- Solver sparso = solver denso (a meno di tolleranza numerica)
- Equilibrio globale (reazioni = carichi applicati)
- Elemento tapered a sezione costante = elemento prismatico
- Sezione per ID = sezione diretta

## Cartella validation/

Script di validazione estesi che generano grafici:

```bash
python validation/validate.py                    # validazione analitica
python validation/validate_timoshenko.py        # Timoshenko vs EB
python validation/validate_releases.py          # rilasci (cerniere)
python validation/validate_thermal_profile.py  # profilo termico eigenstress
python validation/validate_opensees_3d.py       # cross-validation 3D OpenSees
```

## Benchmark prestazionale

```bash
python benchmark/benchmark.py   # confronto tempi denso/sparso/PyNite
```