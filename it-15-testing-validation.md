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
| `test_crossvalid_ext.py` | Cross-validation con un solutore FEM esterno (skippato se non disponibile) |
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

- **solutore esterno (openseespy)**: confronto spostamenti nodo per nodo su modelli 3D
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
python validation/validate_ext_3d.py            # cross-validation 3D (solutore esterno)
python validation/simis_benchmarks.py           # benchmark pubblicati (Bell, Irgens, MacNeal-Harder, NAFEMS, OpenSees)
```

## Benchmark pubblicati (simis.io / Ashes, NAFEMS)

`validation/simis_benchmarks.py` riproduce i benchmark strutturali della suite di regressione
di Ashes (<https://www.simis.io/docs/validation-benchmarks-benchmarking-tests>) applicabili a un
solutore di telai 3D, piu' un classico NAFEMS. Ogni caso confronta un risultato feagent con il
riferimento pubblicato usando la tolleranza del test originale; gli stessi casi girano sotto
pytest in `tests/test_benchmarks_simis.py`.

| Gruppo | Riferimento | Cosa si verifica | Tolleranza |
|---|---|---|---|
| Bell (1987) mensola | HE300B, l/h = 2, 5, 10, 20 | freccia in punta Eulero-Bernoulli e Timoshenko, rapporto w_T/w_E | 0,5 % |
| Bell (1987) fig. 6.6 | incastro + due appoggi | freccia sotto il carico, momenti all'incastro / sotto il carico / sull'appoggio, con e senza taglio | 1 % |
| Irgens (1985) | es. 1, 3 (cap. 19), es. 5 (cap. 24) | freccia e rotazione della mensola, trave appoggiata con q, angolare non simmetrico (flessione deviata, assi principali via `axes`) | 1 % |
| MacNeal & Harder (1985) | trave dritta, curva, svergolata | estensione, taglio nel piano e fuori piano, torsione; trave curva con 6 e 24 corde; svergolata con 12, 48, 91 elementi (`roll`) | 1-2 % (torsione 7 %) |
| Static pull, Maximum stress | mensola a un elemento, 17 + 2 casi | spostamenti, torsione, tensioni di Navier (`beam_stresses`), scatolare ruotato | 0,1 % |
| Element weight, Spring test | peso proprio, molle nei 6 GdL | reazioni da `add_self_weight` (anche con g di Marte), `P/K` sugli appoggi elastici | 0,01 % |
| Eigenfrequency cylinder | tubo a mensola, 22 elementi | prima frequenza con massa lumped e consistente | 0,1 % |
| Decay test tower | torre scatolare 87,6 m, 100 elementi | periodi (Biggs 1964) e decadimento libero da velocita' iniziale modale con 10 modelli di smorzamento (massa, rigidezza, Rayleigh) contro l'oscillatore smorzato analitico | 1 % |
| Earthquake / Static pull OpenSees | torre tubolare 100 m, 150 t in testa | storie temporali per accelerazione alla base (costante e sinusoidale) e forza in testa (rampa, mantenimento, rilascio) contro OpenSees eseguito in locale (`OpenSees.exe`): spostamento e accelerazione in testa, taglio e momento alla base | 0,5 % / 5 % |
| NAFEMS FV2 | croce incernierata | prime 8 frequenze nel piano (11,336; 17,709 x3; 45,345; 57,390 x3 Hz) | 1 % |
| NAFEMS FV4 | mensola con masse eccentriche | 6 frequenze flesso-torsionali accoppiate e ravvicinate (1,723; 1,727; 7,413; 9,972; 18,155; 26,957 Hz); masse su bracci rigidi | 1 % |
| NAFEMS FV5 | trave tozza appoggiata | 9 frequenze (flessione, torsione, assiale) con taglio di Timoshenko e inerzia rotazionale (`mass="consistent-rotary"`) | 2 % |
| Solutore non lineare | isolatori e dissipatori vs OpenSees | bilineare, pendolo a scorrimento, isolatore bidirezionale accoppiato, dissipatore viscoso non lineare sotto accelerogrammi EC8-compatibili; bilancio energetico di un impalcato isolato, vedi [40 - Time-history non lineare](it-40-nonlinear-time-history.html) | 0,5-1 % |

La tabella completa dei risultati viene scritta in `validation/output/simis_benchmarks.md` ed e'
pubblicata come [39 - Report dei benchmark](it-39-benchmark-report.html). Due
dettagli utili nel leggerla: il riferimento di torsione di MacNeal dipende dalla costante torsionale
adottata (feagent usa il valore esatto di Roark per il rettangolo 2:1, quindi lo scarto e' del 6 %,
dentro il 7 % che Ashes concede per lo stesso motivo), e per un'accelerazione alla base applicata a
gradino a t = 0 OpenSees parte con accelerazione nulla mentre feagent la ricava dal carico, il che
lascia uno 0,1 % RMS di differenza su una risposta per il resto identica.

## Benchmark prestazionale

```bash
python benchmark/benchmark.py   # confronto tempi denso/sparso/PyNite
```