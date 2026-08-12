---
layout: default
title: "14 - Esempi d'Uso"
parent: Italiano
nav_order: 14
has_children: true
---

# 14 - Esempi d'Uso

La cartella `usage_examples/` contiene **24 script** autocontenuti che coprono
ogni funzionalità di beamfeapy. Per la galleria con i grafici renderizzati vedi
[Casi studio](it-16-case-studies-gallery.html). (La cartella `examples/` contiene una serie
più breve `ex01..ex10` con gli stessi concetti.)

## Indice degli Esempi

| # | File | Descrizione |
|---|------|-------------|
| 01 | `01_cantilever_nodal_load.py` | Mensola con carico in punta — hello world |
| 02 | `02_simply_supported_beam.py` | Trave appoggiata con carico distribuito uniforme |
| 03 | `03_distributed_loads.py` | Carichi distribuiti parziali e trapezoidali |
| 04 | `04_concentrated_loads_in_span.py` | Carichi concentrati a punti interni dell'elemento |
| 05 | `05_thermal_loads.py` | Variazione termica uniforme + gradiente su portale 3D |
| 06 | `06_thermal_profile.py` | Profilo termico non lineare (eigenstress, EN 1991-1-5) |
| 07 | `07_settlements.py` | Cedimenti nodali imposti |
| 08 | `08_prestress_parabolic.py` | Precompressione parabolica (metodo carichi equivalenti) |
| 09 | `09_prestress_cable_geometry.py` | Precompressione dalla geometria 3D del cavo |
| 10 | `10_timoshenko.py` | Timoshenko vs Eulero-Bernoulli |
| 11 | `11_end_releases.py` | Rilasci di estremità (cerniere) |
| 12 | `12_tapered_beam.py` | Elemento a sezione variabile (1 solo elemento) |
| 13 | `13_tapered_beam_stations.py` | Tapered con sezioni per ID e stazioni |
| 14 | `14_3d_portal_frame.py` | Portale 3D con carichi multipli |
| 15 | `15_load_cases.py` | Load case e combinazioni |
| 16 | `16_ref_vector_and_roll.py` | Orientazione della sezione (ref_vector e roll) |
| 17 | `17_support_types.py` | Tipi di vincolo: fix, pin, roller, custom |
| 18 | `18_internal_forces.py` | Post-processing: azioni interne e deformata |
| 19 | `19_plotting.py` | Tutte le funzioni di visualizzazione Plotly |
| 20 | `20_excel_io.py` | Import/export Excel |
| 21 | `21_sparse_solver.py` | Confronto solver denso vs sparso |
| 22 | `22_continuous_beam.py` | Trave continua multi-campata |
| 23 | `23_frame_with_hinges.py` | Telaio con cerniere interne |
| 24 | `24_prestress_secondary_moments.py` | Momenti secondari in struttura iperstatica |
| 25 | `25_modal_analysis.py` | Analisi modale (masse dai load case) + combinazioni con coefficienti |
| 26 | `26_palazzina_9_piani.py` | Palazzina 9 piani (2×3 campate): statica + modale |
| 27 | `27_grigliato_3d.py` | Grigliato di travi 3D: tutte le analisi |
| 28 | `28_grattacielo.py` | Grattacielo 50 piani: benchmark prestazionale denso vs sparso |
| 29 | `29_tetto_falde.py` | Tetto a due falde con 4 pilastri: statica + modale |
| 30 | `30_validazione_letteratura.py` | Portale iperstatico: validazione vs soluzione analitica (slope-deflection) |

## Esecuzione

```bash
cd FEM
python usage_examples/01_cantilever_nodal_load.py
```

Gli output HTML (grafici Plotly) vengono salvati in `usage_examples/output/`.

## Dipendenze per i grafici e per Excel

```bash
pip install beamfeapy[plot]     # grafici Plotly (plotly + kaleido)
pip install beamfeapy[excel]    # import/export Excel (pandas + openpyxl)
pip install beamfeapy[all]      # tutto
```
