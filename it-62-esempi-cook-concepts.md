---
layout: default
title: "62 - Esempi del libro Cook, Malkus, Plesha"
parent: Italiano
nav_order: 62
---

# 62 - Esempi del libro Cook, Malkus, Plesha

Questa pagina raccoglie **tutti gli esempi svolti** del libro *Concepts and Applications of Finite Element Analysis* (R.D. Cook, D.S. Malkus, M.E. Plesha, 3rd ed., Wiley, 1989) rifatti con feagent. Per ogni esempio: riferimento nel libro, modello feagent, confronto numerico fra il valore del libro e quello di feagent, note (dati mancanti, refusi del libro corretti, differenze di modello). I testi sono riassunti con parole nostre; i dati numerici servono a rifare il calcolo. Il codice di ogni esempio e' in `validation/books/cook/`; per rieseguirli:

```bash
python -m validation.books.run_books cook
python scripts/_gen_book_docs.py
```

**Esiti**: ✅ riprodotto e confrontato; 🟡 riprodotto in parte (dati o risultati del libro incompleti); 📐 ricavo di formule verificato numericamente; ⛔ non riproducibile (motivo nelle note). La colonna *scarto* e' l'errore relativo massimo dei confronti; *rif.* indica il riferimento del confronto: libro, soluzione esatta o valore corretto di un refuso.

**135 esempi**: 36 riprodotti, 19 in parte, 77 ricavi verificati, 3 non riproducibili; 785 confronti numerici, 785 entro la tolleranza.

## Riepilogo

| Esempio | Titolo | Esito | Confronti | Scarto max |
|---|---|---|---|---|
| [Sez. 1.1, Fig. 1.1-1; Problema 1.1](#cook-1-1-tapered-bar-stepped-model) | Barra rastremata modellata con elementi uniformi a gradini | ✅ | 6/6 | 0.00 % |
| [Sez. 1.2, Eq. 1.2-1..1.2-4](#cook-1-2-bar-stiffness-direct) | Matrice di rigidezza della barra con il metodo diretto | 📐 | 6/6 | 0.00 % |
| [Sez. 1.2, Eq. 1.2-5..1.2-7](#cook-1-2-heat-current-bar) | Elemento barra per conduzione del calore e resistore elettrico | 📐 | 4/4 | 0.00 % |
| [Sez. 1.2, Eq. 1.2-8..1.2-14](#cook-1-2-beam-stiffness-direct) | Matrice di rigidezza della trave piana con il metodo diretto | 📐 | 10/10 | 0.00 % |
| [Sez. 1.3, Eq. 1.3-1..1.3-6](#cook-1-3-two-bar-series-assembly) | Assemblaggio e soluzione di due barre in serie | ✅ | 6/6 | 0.00 % |
| [Sez. 2.2-2.5, 3.3; Eq. 2.2-6, 2.3-8, 2.3-9](#cook-2-2-three-bar-truss) | Reticolare piana a tre aste: rigidezza globale e soluzione | ✅ | 13/13 | 0.00 % |
| [Sez. 2.4, Eq. 2.4-1..2.4-5](#cook-2-4-plane-truss-element) | Matrice di rigidezza dell'asta di reticolare piana inclinata | 📐 | 10/10 | 0.00 % |
| [Sez. 2.6, Eq. 2.6-1..2.6-3](#cook-2-6-truss-weight-thermal-loads) | Carichi nodali equivalenti di peso proprio e riscaldamento dell'asta | 📐 | 8/8 | 0.00 % |
| [Sez. 2.7, Fig. 2.7-1..2.7-4](#cook-2-7-assembly-hypothetical-triangles) | Assemblaggio guidato dai numeri di nodo (struttura ipotetica di due triangoli) | 📐 | 10/10 | 0.00 % |
| [Sez. 2.8-2.10, Fig. 2.8-2, 2.8-3](#cook-2-8-bandwidth-numbering) | Numerazione dei nodi e semibanda di una reticolare | ✅ | 2/2 | 0.00 % |
| [Sez. 2.10, Eq. 2.10-1](#cook-2-10-penalty-prescribed-displacement) | Imposizione di uno spostamento prescritto con molla di penalita' e metodo zero-uno | 📐 | 5/5 | 0.00 % |
| [Sez. 2.11, Fig. 2.11-1; Problema 8.1](#cook-2-11-gauss-elimination-bars-in-series) | Tre barre in serie: eliminazione di Gauss e significato fisico | ✅ | 8/8 | 0.00 % |
| [Sez. 2.11, Fig. 2.11-2](#cook-2-11-singular-stiffness-cases) | Strutture con K singolare (trave con sole rotazioni bloccate, due aste collineari) | ✅ | 3/3 | 0.00 % |
| [Sez. 2.13 (Example), Fig. 2.13-1](#cook-2-13-three-bar-thermal-axial) | Barra a tre elementi incastrata agli estremi con forza e riscaldamento parziale | ✅ | 7/7 | 0.00 % |
| [Sez. 3.2, Eq. 3.2-1..3.2-6](#cook-3-2-spring-potential-energy) | Molla lineare: stazionarieta' dell'energia potenziale | 📐 | 3/3 | 0.00 % |
| [Sez. 3.3 (Example: Springs in Series), Eq. 3.3-3..3.3-5](#cook-3-3-springs-in-series) | Tre molle in serie con tre carichi | ✅ | 8/8 | 0.00 % |
| [Sez. 3.4 (Example: Bar Under Axial Load)](#cook-3-4-bar-end-load-and-heating) | Barra uniforme con carico di estremita' e riscaldamento uniforme | ✅ | 2/2 | 0.00 % |
| [Sez. 3.5, Fig. 3.5-1, Eq. 3.5-3..3.5-9](#cook-3-5-bar-linear-load-rayleigh-ritz) | Barra con carico assiale lineare q = cx: Rayleigh-Ritz classico a 1, 2, 3 termini | 📐 | 6/6 | 0.00 % |
| [Sez. 3.7, Eq. 3.7-8..3.7-15](#cook-3-7-bar-variational-derivation) | Barra con carico distribuito e tensione di estremita': equazione di Eulero e condizione naturale | 📐 | 4/4 | 0.00 % |
| [Sez. 3.9, Fig. 3.9-1, Eq. 3.9-6..3.9-12](#cook-3-9-bar-linear-load-three-elements) | Barra con carico lineare q = cx modellata con tre elementi lineari | ✅ | 12/12 | 0.00 % |
| [Sez. 3.10, Eq. 3.10-1..3.10-9](#cook-3-10-heat-conduction-functional) | Formulazione FE della conduzione piana da un funzionale | 📐 | 7/7 | 0.00 % |
| [Sez. 3.12, Eq. 3.12-2, 3.12-3, 3.12-6, 3.12-10](#cook-3-12-lagrange-shape-functions) | Funzioni di forma C0: lineari, quadratiche (Lagrange) e bilineari | 📐 | 14/14 | 0.00 % |
| [Sez. 3.13, Eq. 3.13-1..3.13-4](#cook-3-13-hermite-cubic-shape-functions) | Interpolazione cubica di Hermite (funzioni di forma della trave) | 📐 | 8/8 | 0.00 % |
| [Sez. 4.2, Eq. 4.2-1..4.2-19](#cook-4-2-element-stiffness-overview) | Matrici di rigidezza di barra, trave, telaio piano, CST, rettangolo bilineare, brick trilineare | 📐 | 5/5 | 0.00 % |
| [Sez. 4.3, Fig. 4.3-1, Eq. 4.3-1, 4.3-2](#cook-4-3-bar-initial-strain-thermal) | Carichi nodali di una barra troppo lunga di DeltaL e riscaldata di T | 📐 | 3/3 | 0.00 % |
| [Sez. 4.3 (Example: Bar Element), Eq. 4.3-6..4.3-9](#cook-4-3-bar-consistent-loads) | Carichi consistenti di una barra con carico lineare e forza concentrata a 2L/3 | 📐 | 4/4 | 0.00 % |
| [Sez. 4.3, Fig. 4.3-3..4.3-5, Eq. 4.3-10, 4.3-11](#cook-4-3-plane-solid-consistent-loads) | Carichi consistenti su lati lineari e quadratici, forza al centro di Q8, faccia caricata | 📐 | 8/8 | 0.00 % |
| [Sez. 4.3, Fig. 4.3-6, Eq. 4.3-12](#cook-4-3-beam-consistent-loads) | Carichi consistenti di trave per q uniforme, P e M in mezzeria | 📐 | 12/12 | 0.00 % |
| [Sez. 4.3 (Error Produced by Lumping), Fig. 4.3-7, Eq. 4.3-13, 4.3-14](#cook-4-3-cantilever-udl-lumped-vs-consistent) | Mensola con carico uniforme: carichi consistenti contro concentrati | ✅ | 12/12 | 0.81 % |
| [Sez. 4.4, 4.5, Fig. 4.4-1, 4.4-2, 4.5-1..4.5-3](#cook-4-4-equilibrium-compatibility) | Equilibrio e compatibilita' nella soluzione FE; requisiti di convergenza | 📐 | 3/3 | 0.00 % |
| [Sez. 4.6, Fig. 4.6-1](#cook-4-6-patch-test-q4) | Patch test di quattro quadrilateri a 4 nodi in tensione uniforme sigma_x | ✅ | 4/4 | 0.00 % |
| [Sez. 4.7, Fig. 4.7-1, Eq. 4.7-3](#cook-4-7-bar-self-weight-fixed-ends) | Barra verticale sotto peso proprio incastrata agli estremi: 1, 2, 4 elementi | ✅ | 8/8 | 0.00 % |
| [Sez. 4.7 (Thermal Stress), Fig. 4.7-2, Eq. 4.7-4, 4.7-5](#cook-4-7-cst-linear-temperature) | Tensioni spurie in un CST con campo di temperatura lineare | 📐 | 5/5 | 0.00 % |
| [Sez. 4.7 (controesempio), Fig. 4.2-4](#cook-4-7-q4-beam-linear-temperature) | Trave fra pareti rigide con temperatura lineare nello spessore (un elemento bilineare) | 📐 | 4/4 | 0.00 % |
| [Sez. 4.7, Fig. 4.7-3, Tabella 4.7-1, Eq. 4.7-8](#cook-4-7-plate-hole-stress-improvement) | Piastra con foro centrale: tensione al bordo del foro, metodo standard e iterativo | 🟡 | 5/5 | 0.80 % |
| [Sez. 5.1, Eq. 5.1-1..5.1-12](#cook-5-1-natural-coordinates-bar) | Coordinate naturali lineari e barra a 2 e 3 nodi | 📐 | 6/6 | 0.00 % |
| [Sez. 5.2, Eq. 5.2-1..5.2-13](#cook-5-2-area-volume-coordinates) | Coordinate d'area e di volume, derivate e formule di integrazione | 📐 | 7/7 | 0.00 % |
| [Sez. 5.4, Eq. 5.4-1..5.4-7](#cook-5-4-cst-formulation) | Triangolo lineare: campo scalare e triangolo a deformazione costante (CST) | 📐 | 2/2 | 0.00 % |
| [Sez. 5.4, Fig. 5.4-1, Eq. 5.4-8, 5.4-9](#cook-5-4-cst-pure-bending) | CST in flessione pura: deformazioni errate | 📐 | 4/4 | 0.00 % |
| [Sez. 5.4-5.5, Fig. 5.4-2](#cook-5-4-cantilever-cst-vs-lst) | Mensola con carico parabolico di estremita': CST contro LST | ✅ | 6/6 | 0.74 % |
| [Sez. 5.5, Eq. 5.5-1..5.5-10](#cook-5-5-lst-formulation) | Triangolo quadratico (LST) a lati retti | 📐 | 3/3 | 0.00 % |
| [Sez. 5.6, Fig. 5.6-1, Eq. 5.6-1](#cook-5-6-quadratic-tetrahedron) | Funzioni di forma del tetraedro quadratico a 10 nodi | 📐 | 12/12 | 0.00 % |
| [Sez. 6.2, Fig. 6.2-1, Eq. 6.2-1..6.2-7](#cook-6-2-isoparametric-bar-3-node) | Barra isoparametrica a 3 nodi e Jacobiano | 📐 | 9/9 | 0.00 % |
| [Sez. 6.3-6.5, Eq. 6.3-1..6.3-21, Fig. 6.5-1, 6.5-2](#cook-6-3-q4-isoparametric) | Quadrilatero bilineare isoparametrico: Jacobiano, [B], [k], subroutine SHAPE e QUAD4 | 📐 | 3/3 | 0.00 % |
| [Sez. 6.4 (Example), Tabella 6.4-1, Eq. 6.4-3..6.4-8](#cook-6-4-gauss-quadrature) | Quadratura di Gauss: esempio sul polinomio cubico, regole 2D e 3D | 📐 | 5/5 | 0.00 % |
| [Sez. 6.6, Eq. 6.6-1..6.6-8, Tabella 6.6-1](#cook-6-6-quadratic-quadrilaterals) | Funzioni di forma di quadrilateri da 4 a 9 nodi (serendipity e Lagrange) | 📐 | 9/9 | 0.00 % |
| [Sez. 6.7, Fig. 6.7-1, Eq. 6.7-1..6.7-6](#cook-6-7-hexahedral-elements) | Elementi solidi isoparametrici esaedrici | 📐 | 2/2 | 0.00 % |
| [Sez. 6.8, Tabella 6.8-1, Eq. 6.8-1..6.8-8](#cook-6-8-triangle-isoparametric-quadrature) | Triangoli isoparametrici e regole di integrazione su triangolo | 📐 | 6/6 | 0.00 % |
| [Sez. 6.9, Eq. 6.9-1](#cook-6-9-lst-body-force) | Forza di volume uniforme su triangolo quadratico: carico solo ai nodi di meta' lato | 📐 | 2/2 | 0.00 % |
| [Sez. 6.10, 6.11, Fig. 6.10-1, 6.10-2](#cook-6-10-isoparametric-validity) | Validita' degli elementi isoparametrici; superparametrico che fallisce il patch test; ordine di quadratura | 📐 | 5/5 | 0.00 % |
| [Sez. 6.12, Fig. 6.12-1..6.12-3, Eq. 6.12-2..6.12-8](#cook-6-12-zero-energy-modes) | Modi a energia nulla (hourglass) di Q4 con 1 punto e di Q8/Q9 con 2x2 | 📐 | 9/9 | 0.00 % |
| [Sez. 6.12, Fig. 6.12-4](#cook-6-12-near-mechanism-beam) | Quasi-meccanismi con integrazione ridotta (mensola con carico concentrato, inclusione rigida) | 🟡 | 4/4 | 1.57 % |
| [Sez. 6.13, Fig. 6.13-1..6.13-3, Eq. 6.13-2..6.13-5](#cook-6-13-gauss-point-stress-extrapolation) | Punti di superconvergenza ed estrapolazione delle tensioni dai punti di Gauss | 📐 | 6/6 | 0.02 % |
| [Sez. 6.14, Tabella 6.14-1](#cook-6-14-cantilever-q4-aspect-distortion) | Mensola con elementi bilineari: effetto dell'allungamento e della distorsione | ✅ | 6/6 | 0.54 % |
| [Sez. 6.14, Tabella 6.14-2](#cook-6-14-cantilever-q8-q9-distortion) | Mensola a due elementi quadratici (8 e 9 nodi): regola di Gauss e distorsione | 🟡 | 25/25 | 1.42 % |
| [Sez. 6.14, Fig. 6.14-1](#cook-6-14-badly-shaped-elements) | Elementi di forma degenere e Jacobiano negativo ai punti di Gauss | 📐 | 4/4 | 0.00 % |
| [Sez. 7.2-7.3, Eq. 7.2-1..7.3-11](#cook-7-3-stress-strain-material-transformation) | Trasformazione di vettori, deformazioni, tensioni e matrice dei materiali | 📐 | 5/5 | 0.00 % |
| [Sez. 7.4-7.5, Fig. 7.5-1, 7.5-2, Eq. 7.5-1..7.5-7](#cook-7-5-truss-frame-transformation) | Trasformazione delle matrici di rigidezza di asta piana, asta spaziale e telaio piano | 📐 | 2/2 | 0.00 % |
| [Sez. 7.6, Fig. 7.6-1, Eq. 7.6-1..7.6-8](#cook-7-6-inclined-support) | Appoggio inclinato con trasformazione dei gdl del nodo | 📐 | 5/5 | 0.00 % |
| [Sez. 7.7, Fig. 7.7-1, Eq. 7.7-1..7.7-5](#cook-7-7-dissimilar-elements) | Collegamento di un elemento telaio o di un'asta al lato di un quadrilatero | 📐 | 3/3 | 0.00 % |
| [Sez. 7.8, Fig. 7.8-1, Eq. 7.8-1..7.8-4](#cook-7-8-rigid-link-stiffened-plate) | Trave eccentrica collegata alla piastra con link rigidi; errore di accoppiamento | 🟡 | 3/3 | 1.47 % |
| [Sez. 7.8, Fig. 7.8-2, Eq. 7.8-5](#cook-7-8-rigid-triangle) | Triangolo piano reso rigido con trasformazione a 3 gdl | 📐 | 2/2 | 0.00 % |
| [Sez. 8.1-8.2, Eq. 8.1-1..8.1-5, Fig. 8.1-1, 8.2-1, 8.2-2](#cook-8-1-static-condensation) | Condensazione statica, gdl interni e senza nodo, recupero | 📐 | 5/5 | 0.00 % |
| [Sez. 8.3, Fig. 8.3-1, Eq. 8.3-1..8.3-5](#cook-8-3-bilinear-parasitic-shear) | Taglio parassita del rettangolo bilineare in flessione pura | 📐 | 6/6 | 0.00 % |
| [Sez. 8.3, Fig. 8.3-3](#cook-8-3-cooks-membrane-qm6-q4) | Membrana di Cook: QM6 contro bilineare | 🟡 | 17/17 | 0.63 % |
| [Sez. 8.4, Fig. 8.4-1, Eq. 8.4-1..8.4-9](#cook-8-4-drilling-dof-triangle) | Triangolo piano con rotazioni 'drilling' ottenuto dal LST | 📐 | 4/4 | 0.00 % |
| [Sez. 8.5 (Example), Fig. 8.5-1, Eq. 8.5-1..8.5-16](#cook-8-5-hybrid-stress-rectangle) | Elemento ibrido a tensioni assunte (rettangolo 5-beta) | 📐 | 1/1 | 0.00 % |
| [Sez. 8.6, Fig. 8.6-1, 8.6-2](#cook-8-6-hybrid-drilling-triangle-membrane) | Membrana di Cook con triangoli a rotazioni drilling (Eq. 8.4-5 e macroelemento ibrido) | 🟡 | 5/5 | 0.29 % |
| [Sez. 8.7 (Example), Eq. 8.7-1..8.7-9](#cook-8-7-elastic-kernel-beam) | Nucleo elastico: ricostruzione della [k] completa della trave | 📐 | 1/1 | 0.00 % |
| [Sez. 8.9, Fig. 8.9-1..8.9-4, Eq. 8.9-1..8.9-8](#cook-8-9-quarter-point-crack) | Elementi a quarto di lato per la singolarita' all'apice della fessura | 🟡 | 5/5 | 0.86 % |
| [Sez. 8.10, Fig. 8.10-1, Eq. 8.10-1, 8.10-2](#cook-8-10-winkler-foundation) | Matrice di rigidezza di una fondazione alla Winkler | 📐 | 3/3 | 0.03 % |
| [Sez. 8.11, Fig. 8.11-1..8.11-3, Eq. 8.11-1..8.11-8](#cook-8-11-infinite-elements) | Elementi infiniti mappati 1D e 2D | ⛔ | 0/0 | - |
| [Sez. 8.12-8.16, Fig. 8.14-1, 8.15-1, 8.16-1](#cook-8-14-substructuring-symmetry) | Differenze finite, rianalisi, sottostrutture, simmetria e simmetria ciclica | 📐 | 2/2 | 0.00 % |
| [Sez. 9.1 (Example), Fig. 9.1-2, Eq. 9.1-7..9.1-9](#cook-9-1-bars-constraint-transformation) | Tre barre con vincolo u2 = u3 imposto per trasformazione | ✅ | 3/3 | 0.00 % |
| [Sez. 9.1, Fig. 9.1-3, Eq. 9.1-10](#cook-9-1-rigid-bar-two-springs) | Barra rigida su due molle con vincolo v1 = v2 | ✅ | 3/3 | 0.00 % |
| [Sez. 9.2 (Example), Eq. 9.2-1..9.2-4](#cook-9-2-bars-lagrange-multiplier) | Tre barre con vincolo u2 = u3 imposto con moltiplicatore di Lagrange | ✅ | 4/4 | 0.00 % |
| [Sez. 9.3 (Example), Fig. 9.3-1, Eq. 9.3-4..9.3-6](#cook-9-3-penalty-two-bars) | Due barre con vincolo u1 = u2 imposto con funzione di penalita' | ✅ | 5/5 | 0.00 % |
| [Sez. 9.4-9.5, Fig. 9.4-1, Eq. 9.4-1..9.4-5](#cook-9-4-mindlin-beam-locking) | Locking a taglio di un elemento trave di Mindlin lineare | 🟡 | 15/15 | 0.00 % |
| [Sez. 9.4-9.6, Fig. 9.5-1, 9.5-2, 9.6-1](#cook-9-4-incompressibility-constraint-counting) | Materiali quasi incomprimibili, conteggio dei vincoli e penalita' consistente | 🟡 | 12/12 | 3.36 % |
| [Sez. 10.4, Fig. 10.4-1, Eq. 10.4-4](#cook-10-4-square-wave-fourier) | Serie di Fourier di un'onda quadra (carico uniforme su una luce) | 📐 | 4/4 | 0.00 % |
| [Sez. 10.4, Eq. 10.4-5](#cook-10-4-point-load-fourier) | Serie di Fourier di un carico concentrato in mezzeria e freccia della trave | 📐 | 2/2 | 0.00 % |
| [Sez. 10.4, Eq. 10.4-6..10.4-12, Fig. 10.4-2](#cook-10-4-ss-beam-sine-load) | Trave appoggiata con carico sinusoidale: soluzione armonica disaccoppiata | ✅ | 3/3 | 0.32 % |
| [Sez. 11.3, Fig. 11.3-2, Tabella 11.3-1](#cook-11-3-mindlin-clamped-square-plate) | Piastra quadrata incastrata con elementi di Mindlin: locking al variare di L/t | 🟡 | 4/4 | 1.14 % |
| [Sez. 11.4, Tabella 11.4-1, Fig. 11.4-2](#cook-11-4-dkt-square-plates) | Elemento DKT: freccia al centro di piastre quadrate | ✅ | 16/16 | 0.04 % |
| [Sez. 11.4, Fig. 11.4-3](#cook-11-4-dkt-stiffness-code) | Matrice di rigidezza 9x9 dell'elemento DKT | 📐 | 4/4 | 0.00 % |
| [Sez. 11.5, Fig. 11.5-2](#cook-11-5-rhombic-plate) | Piastra romboidale appoggiata (Morley): condizioni d'appoggio classiche e 'agli elementi finiti' | ✅ | 4/4 | 2.93 % |
| [Sez. 11.5, Fig. 11.5-3](#cook-11-5-patch-test-curvature) | Patch test di curvatura costante per elementi di piastra | ✅ | 8/8 | 0.00 % |
| [Sez. 11.5, Fig. 11.5-4](#cook-11-5-twisted-ribbon) | Nastro torto: mensola sottile caricata da forze d'angolo al variare della lunghezza | 🟡 | 4/4 | 3.05 % |
| [Sez. 12.2, Fig. 12.2-1a](#cook-12-2-semicircular-arch) | Arco semicircolare incastrato con carico in chiave: elementi rettilinei | 🟡 | 3/3 | 36.69 % |
| [Sez. 12.2, Eq. 12.2-12](#cook-12-2-curved-element-rigid-body) | Elemento d'arco curvo con modo interno: energia spuria di moto rigido | ⛔ | 0/0 | - |
| [Sez. 12.3, Eq. 12.3-4](#cook-12-3-drilling-stiffness) | Rigidezza fittizia per la rotazione normale (drilling) nei gusci piani | 📐 | 6/6 | 0.00 % |
| [Sez. 12.4, Fig. 12.4-4](#cook-12-4-cylinder-internal-pressure) | Cilindro a estremi aperti sotto pressione interna: momento meridiano vicino al bordo | 🟡 | 3/3 | 0.73 % |
| [Sez. 13.3, Eq. 13.3-1..13.3-3, Fig. 13.3-1](#cook-13-3-bar-beam-mass-matrices) | Matrici di massa consistente e concentrata di barra e trave | 📐 | 4/4 | 0.00 % |
| [Sez. 13.3, Fig. 13.3-2](#cook-13-3-cst-q4-consistent-mass) | Matrici di massa consistenti di CST e rettangolo bilineare | 📐 | 2/2 | 0.00 % |
| [Sez. 13.3, Eq. 13.3-4, 13.3-5, Fig. 13.3-3](#cook-13-3-hrz-lumping) | Masse diagonali HRZ di barra, trave, Q8 e Q9 | 📐 | 13/13 | 0.00 % |
| [Sez. 13.3, Tabella 13.3-1](#cook-13-3-thick-plate-frequencies-mass) | Frequenze di una piastra spessa appoggiata: massa consistente e concentrata | 🟡 | 7/7 | 6.34 % |
| [Sez. 13.3, Eq. 13.3-6..13.3-9, Fig. 13.3-4](#cook-13-3-optimal-lumping-quadratic-bar) | Concentrazione ottimale delle masse: barra quadratica, T6, Q8, Q9 | 📐 | 9/9 | 0.00 % |
| [Sez. 13.4, Eq. 13.4-1..13.4-3, Fig. 13.4-1](#cook-13-4-rayleigh-damping) | Smorzamento di Rayleigh: coefficienti da due rapporti di smorzamento | 📐 | 4/4 | 0.00 % |
| [Sez. 13.5, Eq. 13.5-8..13.5-10, Fig. 13.5-1](#cook-13-5-free-bar-eigen) | Barra libera con un elemento: autovalori con massa consistente e concentrata | ✅ | 3/3 | 0.00 % |
| [Sez. 13.7, Fig. 13.7-1](#cook-13-7-cantilever-plate-guyan) | Piastra quadrata a mensola: prime 4 frequenze, sistema completo e riduzione di Guyan | 🟡 | 8/8 | 4.40 % |
| [Sez. 13.7, Eq. 13.7-8..13.7-13, Fig. 13.7-2](#cook-13-7-beam-guyan) | Trave a un elemento: autovalori, riduzione di Guyan e massa concentrata | ✅ | 8/8 | 0.00 % |
| [Sez. 13.8, Eq. 13.8-6..13.8-14, Tabella 13.8-1, Fig. 13.8-1](#cook-13-8-cms-bar) | Sintesi modale (CMS1 e Craig-Bampton) di una barra a gradino | ✅ | 16/16 | 0.08 % |
| [Sez. 13.10, Fig. 13.10-2..13.10-5](#cook-13-10-bar-wave-propagation) | Propagazione d'onda in una barra con le differenze centrali | ✅ | 6/6 | 0.22 % |
| [Sez. 13.12, Eq. 13.12-5..13.12-7, Tabella 13.12-1](#cook-13-12-newmark-stability-table) | Famiglia di Newmark: limiti di stabilita' | 📐 | 5/5 | 0.00 % |
| [Sez. 13.13, Eq. 13.13-1..13.13-26, Fig. 13.13-2](#cook-13-13-period-error) | Errore di periodo delle differenze centrali e della regola trapezia | 📐 | 8/8 | 0.00 % |
| [Sez. 14.1, Eq. 14.1-1..14.1-9, Fig. 14.1-1](#cook-14-1-beam-column-energy) | Trave-colonna appoggiata: irrigidimento tensionale e carico euleriano | 📐 | 4/4 | 0.05 % |
| [Sez. 14.2, Eq. 14.2-9, 14.2-11; Sez. 14.4, Eq. 14.4-9](#cook-14-2-stress-stiffness-beam-bar) | Matrici di rigidezza geometrica di trave e barra | 📐 | 2/2 | 0.00 % |
| [Sez. 14.4, Eq. 14.4-10..14.4-16, Fig. 14.4-1](#cook-14-4-one-bar-large-rotation) | Barra a un elemento con deformazioni di Green-Lagrange: rotazione rigida e linearizzazione | 📐 | 4/4 | 0.00 % |
| [Sez. 14.5, Fig. 14.5-1](#cook-14-5-shallow-two-bar-snap) | Asta elastica inclinata incernierata: carico critico linearizzato e collasso non lineare | 🟡 | 4/4 | 0.12 % |
| [Sez. 14.5, Eq. 14.5-8..14.5-10, Fig. 14.5-2](#cook-14-5-cantilever-column-buckling) | Colonna a mensola: carico critico con un elemento di trave | ✅ | 3/3 | 0.01 % |
| [Sez. 14.5, Eq. 14.5-11, 14.5-12](#cook-14-5-buckling-condensation) | Condensazione della rotazione nel buckling della mensola | ✅ | 2/2 | 0.00 % |
| [Sez. 15.3, Eq. 15.3-1..15.3-14, Tabella 15.3-1, Fig. 15.3-1](#cook-15-3-weighted-residual-bar) | Metodi dei residui pesati su una barra con carico assiale lineare | 📐 | 13/13 | 0.04 % |
| [Sez. 15.4, Eq. 15.4-1..15.4-20](#cook-15-4-galerkin-bar-beam) | Galerkin per la barra e per la dinamica della trave: le matrici standard | 📐 | 4/4 | 0.00 % |
| [Sez. 15.4, Eq. 15.4-21..15.4-24, Fig. 15.4-3](#cook-15-4-heat-bar-galerkin) | Conduzione stazionaria in una barra: elemento di Galerkin a 2 nodi | 📐 | 3/3 | 0.00 % |
| [Sez. 15.6, Eq. 15.6-1..15.6-7](#cook-15-6-quasiharmonic-galerkin) | Equazione quasi-armonica 2D: matrice di conduttivita' e vettori di Galerkin | 📐 | 2/2 | 0.00 % |
| [Sez. 16.2, Eq. 16.2-1..16.2-8, Fig. 16.2-1](#cook-16-2-heat-bar-convection) | Barra con conduzione, convezione laterale e sorgente: matrici e soluzione | 📐 | 3/3 | 0.01 % |
| [Sez. 16.5, Eq. 16.5-5..16.5-10, Fig. 16.5-1](#cook-16-5-q4-heat-matrices) | Elemento Q4 per la conduzione: convezione di bordo e sorgente puntuale | 📐 | 4/4 | 0.00 % |
| [Sez. 16.6, Eq. 16.6-6..16.6-9](#cook-16-6-thermal-transient-trapezoid) | Transitori termici: regola trapezia generalizzata e passo critico | 📐 | 7/7 | 0.00 % |
| [Sez. 16.7, Fig. 16.7-1](#cook-16-7-potential-flow-cylinder) | Flusso potenziale attorno a un cilindro in un canale: potenziale e funzione di corrente | 🟡 | 2/2 | 1.12 % |
| [Sez. 16.9, Eq. 16.9-7, 16.9-8, Fig. 16.9-2b](#cook-16-9-slosh-one-element) | Oscillazione del liquido in un serbatoio con un elemento 'mock-fluid' | ✅ | 3/3 | 0.00 % |
| [Sez. 16.9, Eq. 16.9-9, 16.9-10, Fig. 16.9-2c](#cook-16-9-acoustic-pipe-spring) | Modi acustici dell'aria in un tubo con due elementi | ✅ | 3/3 | 0.03 % |
| [Sez. 17.2, Eq. 17.2-1..17.2-16, Fig. 17.2-1..17.2-9](#cook-17-2-nonlinear-spring-methods) | Molla non lineare: sostituzione diretta, Newton-Raphson, Newton modificato | 📐 | 4/4 | 0.00 % |
| [Sez. 17.3, Eq. 17.3-1..17.3-7, Fig. 17.3-1..17.3-3](#cook-17-3-tapered-bar-plasticity) | Barra rastremata elasto-plastica: metodo incrementale con rigidezza tangente | 📐 | 3/3 | 0.00 % |
| [Sez. 17.4, Eq. 17.4-16..17.4-19](#cook-17-4-uniaxial-plasticity) | Plasticita' di von Mises monoassiale con incrudimento cinematico | 📐 | 4/4 | 0.00 % |
| [Sez. 17.6, Fig. 17.6-1, 17.6-2](#cook-17-6-bar-dynamic-plasticity) | Onda elastica e onda plastica in una barra elasto-plastica (differenze centrali) | ✅ | 4/4 | 4.08 % |
| [Sez. 17.7, Eq. 17.7-2..17.7-7, Fig. 17.7-1a](#cook-17-7-cantilever-tip-moment) | Mensola con momento di punta in grandi rotazioni (corotazionale piano) | ✅ | 5/5 | 0.10 % |
| [Sez. 18.2, Eq. 18.2-1..18.2-3, Fig. 18.2-1](#cook-18-2-ill-conditioning-springs) | Mal condizionamento: sistema quasi singolare e molla rigida su molla cedevole | 📐 | 5/5 | 0.00 % |
| [Sez. 18.3, Fig. 18.3-1, Eq. 18.3-1..18.3-10](#cook-18-3-condition-bar) | Numero di condizionamento: barra a due elementi e stime di crescita | 📐 | 4/4 | 1.54 % |
| [Sez. 18.4, Tabella 18.4-1](#cook-18-4-diagonal-decay-cantilever) | Rapporti di decadimento diagonale e numerazione dei nodi in una mensola lunga | 🟡 | 19/19 | 0.83 % |
| [Sez. 18.6, Eq. 18.6-1..18.6-9, Fig. 18.6-1](#cook-18-6-bar-discretization-error) | Errore di discretizzazione nella barra con carico distribuito | 📐 | 4/4 | 0.00 % |
| [Sez. 18.7, Eq. 18.7-3, Fig. 18.7-1](#cook-18-7-laplacian-mesh-estimate) | Stima a priori dell'errore relativo su una mesh graduata | ⛔ | 0/0 | - |
| [Sez. 18.7, Fig. 18.7-4, Tabella 18.7-1](#cook-18-7-cantilever-regular-refinement) | Mensola con elementi bilineari: raffinamento regolare ed estrapolazione di Richardson | ✅ | 17/17 | 0.37 % |
| [Sez. 18.7, Fig. 18.7-6](#cook-18-7-beam-with-hole-irregular) | Mensola con grande foro centrale: tensione massima al raffinamento della mesh | 🟡 | 2/2 | 3.27 % |
| [Sez. 18.8, Fig. 18.8-1, Eq. 18.8-1, 18.8-2](#cook-18-8-q4-eigenvalue-test) | Autovalori della matrice di rigidezza di un Q4 quadrato in deformazione piana | ✅ | 8/8 | 0.00 % |
| [Sez. 18.8, Fig. 18.8-2](#cook-18-8-single-element-cantilever-test) | Test del singolo elemento: mensola in flessione pura al variare di L/H | ✅ | 8/8 | 0.00 % |

## Capitolo 1

<a id="cook-1-1-tapered-bar-stepped-model"></a>

### Sez. 1.1, Fig. 1.1-1; Problema 1.1 - Barra rastremata modellata con elementi uniformi a gradini

✅ · pag. PDF 22 · bar

Barra di lunghezza L incastrata a un estremo e tirata da P all'altro, con area che varia linearmente da 3A0 all'incastro ad A0 all'estremo libero. Il libro chiede l'errore dei modelli a gradini con 1-4 elementi e stampa lo spostamento esatto u = P L ln3 / (2 E A0) = 0,5493 P L/(E A0).

**Modello**: Barre L2 di feagent (add_bar) in serie, 1, 2, 3, 4 e 64 elementi; area di ciascun elemento = area reale a meta' elemento, oppure aree nodali (barra rastremata): per l'L2 le due scelte coincidono.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| u esatto P L ln3/(2 E A0) [PL/EA0] | libro | 0.5493 | 0.54931 | 0.00 % | ✅ |
| u, 1 elementi a gradini (somma dei tratti) [PL/EA0] | esatta | 0.5 | 0.5 | 0.00 % | ✅ |
| u, 2 elementi a gradini (somma dei tratti) [PL/EA0] | esatta | 0.53333 | 0.53333 | 0.00 % | ✅ |
| u, 3 elementi a gradini (somma dei tratti) [PL/EA0] | esatta | 0.54167 | 0.54167 | 0.00 % | ✅ |
| u, 4 elementi a gradini (somma dei tratti) [PL/EA0] | esatta | 0.54488 | 0.54488 | 0.00 % | ✅ |
| u, 64 barre con aree nodali [PL/EA0] | esatta | 0.54931 | 0.54929 | 0.00 % | ✅ |

**Note**: Dati adimensionali P = L = E = A0 = 1. Il libro non stampa i valori dei modelli a gradini: si confrontano con la somma in forma chiusa dei tratti (esatta per il modello a gradini) e con lo spostamento esatto della barra rastremata. Con 1 elemento l'errore e' -9,0 %, con 4 elementi -0,7 %.

*Codice*: `validation/books/cook/ch01.py`

<a id="cook-1-2-bar-stiffness-direct"></a>

### Sez. 1.2, Eq. 1.2-1..1.2-4 - Matrice di rigidezza della barra con il metodo diretto

📐 · pag. PDF 28 · bar

La rigidezza di una barra si ottiene imponendo spostamenti unitari ai due estremi: [k] = AE/L [[1, -1], [-1, 1]].

**Modello**: Biella di feagent (add_truss) e barra isoparametrica L2 (add_bar) con A = 2, E = 3, L = 4 lungo una direzione inclinata.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| biella: k11 [F/L] | esatta | 1.5 | 1.5 | 0.00 % | ✅ |
| biella: k12 [F/L] | esatta | -1.5 | -1.5 | 0.00 % | ✅ |
| biella: k22 [F/L] | esatta | 1.5 | 1.5 | 0.00 % | ✅ |
| barra L2: k11 [F/L] | esatta | 1.5 | 1.5 | 0.00 % | ✅ |
| barra L2: k12 [F/L] | esatta | -1.5 | -1.5 | 0.00 % | ✅ |
| barra L2: k22 [F/L] | esatta | 1.5 | 1.5 | 0.00 % | ✅ |

**Note**: Si proietta la matrice 6x6 di feagent sulla direzione dell'asse e si confronta termine a termine con AE/L.

*Codice*: `validation/books/cook/ch01.py`

<a id="cook-1-2-heat-current-bar"></a>

### Sez. 1.2, Eq. 1.2-5..1.2-7 - Elemento barra per conduzione del calore e resistore elettrico

📐 · pag. PDF 29 · heat_steady

La stessa matrice della barra vale per la conduzione 1D (Ak/L [[1,-1],[-1,1]] sulle temperature) e per un resistore elettrico (1/r [[1,-1],[-1,1]] sulle tensioni).

**Modello**: FieldModel('heat') con elementi L2 (area A, conducibilita' k); per il resistore si usa k = 1/r con A = L = 1. Due elementi in serie risolti con temperature (tensioni) imposte agli estremi.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| k11 termico = A k / L [W/K] | esatta | 10 | 10 | 0.00 % | ✅ |
| k12 termico = -A k / L [W/K] | esatta | -10 | -10 | 0.00 % | ✅ |
| tensione al nodo intermedio [V] | esatta | 6 | 6 | 0.00 % | ✅ |
| corrente entrante al nodo 1 [A] | esatta | 2 | 2 | 0.00 % | ✅ |

**Note**: Valori di prova A = 0,5, k = 40, L = 2; resistori r1 = 2, r2 = 3 con V = 10 e 0 agli estremi: corrente I = 2 e tensione intermedia 6.

*Codice*: `validation/books/cook/ch01.py`

<a id="cook-1-2-beam-stiffness-direct"></a>

### Sez. 1.2, Eq. 1.2-8..1.2-14 - Matrice di rigidezza della trave piana con il metodo diretto

📐 · pag. PDF 30 · beam

Le colonne della rigidezza della trave si ottengono attivando un gdl alla volta: freccia unitaria all'estremo (12EI/L^3, 6EI/L^2) e rotazione unitaria (4EI/L, 2EI/L).

**Modello**: Una trave di Eulero-Bernoulli di feagent (EI = 6, L = 2); colonne di [k] ricavate come reazioni con un solo gdl imposto (add_settlement) e gli altri bloccati, come nel libro.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| k11 (reazione) | esatta | 9 | 9 | 0.00 % | ✅ |
| k21 (reazione) | esatta | 9 | 9 | 0.00 % | ✅ |
| k31 (reazione) | esatta | -9 | -9 | 0.00 % | ✅ |
| k41 (reazione) | esatta | 9 | 9 | 0.00 % | ✅ |
| k22 (reazione) | esatta | 12 | 12 | 0.00 % | ✅ |
| k32 (reazione) | esatta | -9 | -9 | 0.00 % | ✅ |
| k42 (reazione) | esatta | 6 | 6 | 0.00 % | ✅ |
| k33 (reazione) | esatta | 9 | 9 | 0.00 % | ✅ |
| k43 (reazione) | esatta | -9 | -9 | 0.00 % | ✅ |
| k44 (reazione) | esatta | 12 | 12 | 0.00 % | ✅ |

**Note**: Confronto con Eq. 4.2-5 del libro, gdl (v1, theta1, v2, theta2).

*Codice*: `validation/books/cook/ch01.py`

<a id="cook-1-3-two-bar-series-assembly"></a>

### Sez. 1.3, Eq. 1.3-1..1.3-6 - Assemblaggio e soluzione di due barre in serie

✅ · pag. PDF 32 · bar

Assemblaggio della rigidezza di due barre in serie e soluzione: u1 = -P/k1 - P/k2, u2 = -P/k2, sigma_i = -P/A_i.

**Modello**: Due bielle in serie (nodo 3 incastrato a sinistra, nodo 1 all'estremo destro) con A1 = 2, E1 = 100, L1 = 1 e A2 = 1, E2 = 200, L2 = 4; P = 10 verso -x al nodo 1 (compressione).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| K22 assemblata = k1 + k2 [F/L] | esatta | 250 | 250 | 0.00 % | ✅ |
| K12 assemblata = -k1 [F/L] | esatta | -200 | -200 | 0.00 % | ✅ |
| u1 [L] | esatta | -0.25 | -0.25 | 0.00 % | ✅ |
| u2 [L] | esatta | -0.2 | -0.2 | 0.00 % | ✅ |
| sigma1 [F/L2] | esatta | -5 | -5 | 0.00 % | ✅ |
| sigma2 [F/L2] | esatta | -10 | -10 | 0.00 % | ✅ |

**Note**: Esempio simbolico: valori numerici di prova. Si confronta anche la matrice assemblata di feagent con [[k1,-k1,0],[-k1,k1+k2,-k2],[0,-k2,k2]].

*Codice*: `validation/books/cook/ch01.py`

## Capitolo 2

<a id="cook-2-2-three-bar-truss"></a>

### Sez. 2.2-2.5, 3.3; Eq. 2.2-6, 2.3-8, 2.3-9 - Reticolare piana a tre aste: rigidezza globale e soluzione

✅ · pag. PDF 52 · truss

Il libro costruisce la rigidezza 6x6 della reticolare a tre aste attivando un gdl alla volta (Eq. 2.2-6), la riduce ai gdl attivi u1, v1, v3 (Eq. 2.3-8) e scrive le reazioni (Eq. 2.3-9); la [K] libera ha rango 3.

**Modello**: Tre bielle di feagent (nodi 2 = (0,0), 3 = (0,4), 1 = (3,0)); aree scelte per avere k1 = 2, k2 = 3, k3 = 5 con E = 1; cerniera in 2, carrello verticale in 3, P = 1 verso il basso al nodo 1.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| K ridotta (u1, u1), Eq. 2.3-8 [F/L] | esatta | 6.08 | 6.08 | 0.00 % | ✅ |
| K ridotta (u1, v1), Eq. 2.3-8 [F/L] | esatta | -1.44 | -1.44 | 0.00 % | ✅ |
| K ridotta (u1, v3), Eq. 2.3-8 [F/L] | esatta | 1.44 | 1.44 | 0.00 % | ✅ |
| K ridotta (v1, v1), Eq. 2.3-8 [F/L] | esatta | 1.92 | 1.92 | 0.00 % | ✅ |
| K ridotta (v1, v3), Eq. 2.3-8 [F/L] | esatta | -1.92 | -1.92 | 0.00 % | ✅ |
| K ridotta (v3, v3), Eq. 2.3-8 [F/L] | esatta | 3.92 | 3.92 | 0.00 % | ✅ |
| rango della [K] 6x6 non vincolata | esatta | 3 | 3 | 0.00 % | ✅ |
| u1 (soluzione di Eq. 2.3-8) [L] | esatta | -0.15 | -0.15 | 0.00 % | ✅ |
| v1 (soluzione di Eq. 2.3-8) [L] | esatta | -1.1333 | -1.1333 | 0.00 % | ✅ |
| v3 (soluzione di Eq. 2.3-8) [L] | esatta | -0.5 | -0.5 | 0.00 % | ✅ |
| reazione p2, Eq. 2.3-9 [F] | esatta | 0.75 | 0.75 | 0.00 % | ✅ |
| reazione q2, Eq. 2.3-9 [F] | esatta | 1 | 1 | 0.00 % | ✅ |
| reazione p3, Eq. 2.3-9 [F] | esatta | -0.75 | -0.75 | 0.00 % | ✅ |

**Note**: Il libro e' simbolico (non stampa numeri): la [K] di feagent si confronta termine a termine con Eq. 2.3-8 valutata con i k di prova, la soluzione con quella del sistema 2.3-8 e le reazioni con Eq. 2.3-9.

*Codice*: `validation/books/cook/ch02.py`

<a id="cook-2-4-plane-truss-element"></a>

### Sez. 2.4, Eq. 2.4-1..2.4-5 - Matrice di rigidezza dell'asta di reticolare piana inclinata

📐 · pag. PDF 56 · truss

La rigidezza dell'asta inclinata di beta e' AE/L [[c², cs, -c², -cs], ...] con c = cos beta, s = sin beta (Eq. 2.4-5).

**Modello**: Biella di feagent fra (1, 2) e (1 + 4 cos 30°, 2 + 4 sin 30°), A = 1,5, E = 2.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| k11 [F/L] | esatta | 0.5625 | 0.5625 | 0.00 % | ✅ |
| k12 [F/L] | esatta | 0.32476 | 0.32476 | 0.00 % | ✅ |
| k13 [F/L] | esatta | -0.5625 | -0.5625 | 0.00 % | ✅ |
| k14 [F/L] | esatta | -0.32476 | -0.32476 | 0.00 % | ✅ |
| k22 [F/L] | esatta | 0.1875 | 0.1875 | 0.00 % | ✅ |
| k23 [F/L] | esatta | -0.32476 | -0.32476 | 0.00 % | ✅ |
| k24 [F/L] | esatta | -0.1875 | -0.1875 | 0.00 % | ✅ |
| k33 [F/L] | esatta | 0.5625 | 0.5625 | 0.00 % | ✅ |
| k34 [F/L] | esatta | 0.32476 | 0.32476 | 0.00 % | ✅ |
| k44 [F/L] | esatta | 0.1875 | 0.1875 | 0.00 % | ✅ |

**Note**: Confronto dei 16 termini sui gdl (u1, v1, u2, v2).

*Codice*: `validation/books/cook/ch02.py`

<a id="cook-2-6-truss-weight-thermal-loads"></a>

### Sez. 2.6, Eq. 2.6-1..2.6-3 - Carichi nodali equivalenti di peso proprio e riscaldamento dell'asta

📐 · pag. PDF 60 · truss, thermal

Il peso W dell'asta va ai nodi come W/2 verso il basso (Eq. 2.6-1); il riscaldamento uniforme T da' {r} = alfa E A T [-c, -s, c, s] (Eq. 2.6-3).

**Modello**: Barra L2 di feagent inclinata di 30° (A = 2, E = 3, L = 5, alfa = 1e-3, T = 40, peso specifico 0,7 verso -y): forze nodali da equivalent_body_force e equivalent_thermal_load; peso delle bielle con add_truss_self_weight.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| peso: v1 = -W/2 [F] | esatta | -3.5 | -3.5 | 0.00 % | ✅ |
| peso: v2 = -W/2 [F] | esatta | -3.5 | -3.5 | 0.00 % | ✅ |
| peso: u1 = 0 [F] | esatta | 0 | 0 | 0.00 % | ✅ |
| termico: u1 [F] | esatta | -0.20785 | -0.20785 | 0.00 % | ✅ |
| termico: v1 [F] | esatta | -0.12 | -0.12 | 0.00 % | ✅ |
| termico: u2 [F] | esatta | 0.20785 | 0.20785 | 0.00 % | ✅ |
| termico: v2 [F] | esatta | 0.12 | 0.12 | 0.00 % | ✅ |
| peso della biella al nodo 2 [F] | esatta | -3.5 | -3.5 | 0.00 % | ✅ |

**Note**: Valori di prova; le forze di feagent sono quelle applicate ai nodi.

*Codice*: `validation/books/cook/ch02.py`

<a id="cook-2-7-assembly-hypothetical-triangles"></a>

### Sez. 2.7, Fig. 2.7-1..2.7-4 - Assemblaggio guidato dai numeri di nodo (struttura ipotetica di due triangoli)

📐 · pag. PDF 61 · assembly

Il libro mostra come i coefficienti delle matrici d'elemento finiscono nella [K] globale seguendo i numeri di nodo (array NOD, Eq. 2.7-4).

**Modello**: FieldModel con due triangoli T3 a un gdl per nodo (elemento 1: nodi 1, 4, 2; elemento 2: nodi 4, 3, 2), come nella struttura ipotetica del libro.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| K11 assemblata contro somma a mano | esatta | 1.3951 | 1.3951 | 0.00 % | ✅ |
| K12 assemblata contro somma a mano | esatta | -0.34568 | -0.34568 | 0.00 % | ✅ |
| K13 assemblata contro somma a mano | esatta | 0 | 0 | 0.00 % | ✅ |
| K14 assemblata contro somma a mano | esatta | -1.0494 | -1.0494 | 0.00 % | ✅ |
| K22 assemblata contro somma a mano | esatta | 2.3506 | 2.3506 | 0.00 % | ✅ |
| K23 assemblata contro somma a mano | esatta | -0.74599 | -0.74599 | 0.00 % | ✅ |
| K24 assemblata contro somma a mano | esatta | -1.2589 | -1.2589 | 0.00 % | ✅ |
| K33 assemblata contro somma a mano | esatta | 1.8128 | 1.8128 | 0.00 % | ✅ |
| K34 assemblata contro somma a mano | esatta | -1.0668 | -1.0668 | 0.00 % | ✅ |
| K44 assemblata contro somma a mano | esatta | 3.3752 | 3.3752 | 0.00 % | ✅ |

**Note**: Si confronta la [K] assemblata da feagent con la somma delle matrici d'elemento sparse a mano secondo la connettivita' e si verifica che K13 = 0 (nodi 1 e 3 non appartengono allo stesso elemento).

*Codice*: `validation/books/cook/ch02.py`

<a id="cook-2-8-bandwidth-numbering"></a>

### Sez. 2.8-2.10, Fig. 2.8-2, 2.8-3 - Numerazione dei nodi e semibanda di una reticolare

✅ · pag. PDF 65 · truss, assembly

La semibanda b dipende dalla massima differenza fra i numeri dei nodi di uno stesso elemento: con la numerazione buona b = 6, con quella cattiva b = 12.

**Modello**: Reticolare piana a 6 nodi e 9 aste della Fig. 2.8-2 (coordinate indicative, conta solo la connettivita') con la numerazione buona e quella cattiva; semibanda della [K] di feagent ordinata per numero di nodo.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| semibanda, numerazione buona | libro | 6 | 6 | 0.00 % | ✅ |
| semibanda, numerazione cattiva | libro | 12 | 12 | 0.00 % | ✅ |

**Note**: Il libro non fissa le coordinate: si usano posizioni plausibili della figura.

*Codice*: `validation/books/cook/ch02.py`

<a id="cook-2-10-penalty-prescribed-displacement"></a>

### Sez. 2.10, Eq. 2.10-1 - Imposizione di uno spostamento prescritto con molla di penalita' e metodo zero-uno

📐 · pag. PDF 71 · truss

Uno spostamento prescritto si impone eliminando riga e colonna (Eq. 2.10-1, sistema ridotto con il termine noto corretto) oppure con una molla molto rigida e una forza pari alla rigidezza per lo spostamento voluto.

**Modello**: Reticolare a tre aste della Fig. 2.2-1 (k1 = 2, k2 = 3, k3 = 5) con v1 = -0,1 imposto: cedimento esatto (add_settlement) e molla di penalita' a terra kb = 1e6 K22 con forza kb v1 (add_elastic_support).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| u1, gdl prescritto (Eq. 2.10-1) [L] | esatta | -0.013235 | -0.013235 | 0.00 % | ✅ |
| v3, gdl prescritto (Eq. 2.10-1) [L] | esatta | -0.044118 | -0.044118 | 0.00 % | ✅ |
| v1 con molla di penalita' [L] | esatta | -0.1 | -0.1 | 0.00 % | ✅ |
| u1 con molla di penalita' [L] | esatta | -0.013235 | -0.013235 | 0.00 % | ✅ |
| v3 con molla di penalita' [L] | esatta | -0.044118 | -0.044118 | 0.00 % | ✅ |

**Note**: Errore relativo atteso della penalita' circa 1e-6.

*Codice*: `validation/books/cook/ch02.py`

<a id="cook-2-11-gauss-elimination-bars-in-series"></a>

### Sez. 2.11, Fig. 2.11-1; Problema 8.1 - Tre barre in serie: eliminazione di Gauss e significato fisico

✅ · pag. PDF 74 · bar

Sistema [[12,-6,0],[-6,12,-6],[0,-6,6]]{u2,u3,u4} = {24,0,0}: eliminando u2 resta K33' = 9 con R' = 12, eliminando u3 resta 2 u4 = 8; u2 = u3 = u4 = 4 e le barre 2 e 3 sono scariche.

**Modello**: Tre bielle in serie con AE/L = 6, nodo 1 incastrato, P = 24 al nodo 2; eliminazione di Gauss eseguita sulla [K] ridotta assemblata da feagent.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| K33' dopo l'eliminazione di u2 [F/L] | libro | 9 | 9 | 0.00 % | ✅ |
| R' dopo l'eliminazione di u2 [F] | libro | 12 | 12 | 0.00 % | ✅ |
| K44'' dopo l'eliminazione di u3 [F/L] | libro | 2 | 2 | 0.00 % | ✅ |
| R'' dopo l'eliminazione di u3 [F] | libro | 8 | 8 | 0.00 % | ✅ |
| u2 [L] | libro | 4 | 4 | 0.00 % | ✅ |
| u3 [L] | libro | 4 | 4 | 0.00 % | ✅ |
| u4 [L] | libro | 4 | 4 | 0.00 % | ✅ |
| sforzo nella barra 2 [F] | libro | 0 | 0 | 0.00 % | ✅ |

**Note**: Ogni passo dell'eliminazione equivale a una condensazione statica (Problema 8.1).

*Codice*: `validation/books/cook/ch02.py`

<a id="cook-2-11-singular-stiffness-cases"></a>

### Sez. 2.11, Fig. 2.11-2 - Strutture con K singolare (trave con sole rotazioni bloccate, due aste collineari)

✅ · pag. PDF 75 · beam, truss

Il libro mostra due strutture per cui l'eliminazione di Gauss fallisce perche' la [K] e' singolare: la trave puo' traslare rigidamente, il nodo fra due aste allineate non ha rigidezza trasversale.

**Modello**: (a) una trave piana di feagent con le sole rotazioni bloccate; (b) due bielle allineate con il nodo centrale caricato trasversalmente. Meccanismi trovati da Model.find_mechanisms.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| meccanismi della trave con sole rotazioni bloccate | esatta | 2 | 2 | 0.00 % | ✅ |
| meccanismi delle due aste allineate | esatta | 1 | 1 | 0.00 % | ✅ |
| il meccanismo e' v2 (trasversale al nodo 2) | esatta | 1 | 1 | 0.00 % | ✅ |

**Note**: Il libro non stampa numeri: si confronta il numero di meccanismi atteso (2 traslazioni rigide per la trave nel piano, 1 moto trasversale del nodo centrale).

*Codice*: `validation/books/cook/ch02.py`

<a id="cook-2-13-three-bar-thermal-axial"></a>

### Sez. 2.13 (Example), Fig. 2.13-1 - Barra a tre elementi incastrata agli estremi con forza e riscaldamento parziale

✅ · pag. PDF 77 · bar, thermal

Barra divisa in tre elementi uguali, incastrata agli estremi, con forza F al nodo 2 e i due elementi di sinistra riscaldati di T. Il libro ricava u2, u3, le tre tensioni e le reazioni (Fig. 2.13-1b).

**Modello**: Tre barre L2 di feagent uguali (L = 2, A = 3, E = 200, alfa = 1e-3), nodi 1 e 4 bloccati, F = 30 verso -x al nodo 2, elementi 1 e 2 riscaldati di T = 50 (add_solid_thermal).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| u2 | esatta | -0.033333 | -0.033333 | 0.00 % | ✅ |
| u3 | esatta | 0.033333 | 0.033333 | 0.00 % | ✅ |
| sigma1 | esatta | -13.333 | -13.333 | 0.00 % | ✅ |
| sigma2 | esatta | -3.3333 | -3.3333 | 0.00 % | ✅ |
| sigma3 | esatta | -3.3333 | -3.3333 | 0.00 % | ✅ |
| R1 | esatta | 40 | 40 | 0.00 % | ✅ |
| R4 | esatta | -10 | -10 | 0.00 % | ✅ |

**Note**: Formule simboliche del libro valutate con i dati di prova; spostamenti ricostruiti dalle equazioni (OCR rovinato) e coerenti con le tensioni stampate.

*Codice*: `validation/books/cook/ch02.py`

## Capitolo 3

<a id="cook-3-2-spring-potential-energy"></a>

### Sez. 3.2, Eq. 3.2-1..3.2-6 - Molla lineare: stazionarieta' dell'energia potenziale

📐 · pag. PDF 91 · spring

L'energia potenziale della molla e' Pi = k D^2/2 - P D; la stazionarieta' dPi/dD = 0 da' D = P/k e il minimo vale Pi = -P^2/(2k).

**Modello**: Una molla di feagent (k = 50) con forza P = 20 all'estremo libero.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| D = P/k [L] | esatta | 0.4 | 0.4 | 0.00 % | ✅ |
| Pi minima = -P²/(2k) [F L] | esatta | -4 | -4 | 0.00 % | ✅ |
| aumento di Pi per D + 10 % [F L] | esatta | 0.04 | 0.04 | 0.00 % | ✅ |

**Note**: Pi calcolata con la [K] e la soluzione di feagent; si verifica anche che Pi cresca allontanandosi da D.

*Codice*: `validation/books/cook/ch03.py`

<a id="cook-3-3-springs-in-series"></a>

### Sez. 3.3 (Example: Springs in Series), Eq. 3.3-3..3.3-5 - Tre molle in serie con tre carichi

✅ · pag. PDF 93 · spring

Dalla stazionarieta' dell'energia potenziale si ottiene [[k1+k2,-k2,0],[-k2,k2+k3,-k3],[0,-k3,k3]]{D} = {P}; una molla k4 dal supporto al nodo 3 aggiunge k4 a K33.

**Modello**: Tre molle di feagent in serie (k1 = 10, k2 = 20, k3 = 30), carichi P1 = 1, P2 = 2, P3 = 3; variante con una quarta molla k4 = 15 fra il supporto e il nodo 3.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| K33 [F/L] | esatta | 30 | 30 | 0.00 % | ✅ |
| D1 [L] | esatta | 0.6 | 0.6 | 0.00 % | ✅ |
| D2 [L] | esatta | 0.85 | 0.85 | 0.00 % | ✅ |
| D3 [L] | esatta | 0.95 | 0.95 | 0.00 % | ✅ |
| K33 con k4 [F/L] | esatta | 45 | 45 | 0.00 % | ✅ |
| D1 con k4 [L] | esatta | 0.22 | 0.22 | 0.00 % | ✅ |
| D2 con k4 [L] | esatta | 0.28 | 0.28 | 0.00 % | ✅ |
| D3 con k4 [L] | esatta | 0.25333 | 0.25333 | 0.00 % | ✅ |

**Note**: Esempio simbolico, valori di prova.

*Codice*: `validation/books/cook/ch03.py`

<a id="cook-3-4-bar-end-load-and-heating"></a>

### Sez. 3.4 (Example: Bar Under Axial Load) - Barra uniforme con carico di estremita' e riscaldamento uniforme

✅ · pag. PDF 98 · bar, thermal

Con un solo gdl il funzionale da' D = P L/(A E) + alfa L T e sigma = P/A (il riscaldamento libero non produce tensioni).

**Modello**: Una barra L2 di feagent (L = 3, A = 2, E = 100, alfa = 2e-4, T = 25), incastrata a sinistra, P = 8 all'estremo libero.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| D [L] | esatta | 0.135 | 0.135 | 0.00 % | ✅ |
| sigma [F/L2] | esatta | 4 | 4 | 0.00 % | ✅ |

**Note**: Valori di prova.

*Codice*: `validation/books/cook/ch03.py`

<a id="cook-3-5-bar-linear-load-rayleigh-ritz"></a>

### Sez. 3.5, Fig. 3.5-1, Eq. 3.5-3..3.5-9 - Barra con carico assiale lineare q = cx: Rayleigh-Ritz classico a 1, 2, 3 termini

📐 · pag. PDF 100 · bar

Barra uniforme incastrata in x = 0 con carico distribuito q = c x. Ritz a 1 termine: u = c L^2 x/(3AE); a 2 termini: u = c L (7 L x - 3 x^2)/(12 AE); con 3 termini si ritrova l'esatta u = c (3 L^2 x - x^3)/(6 AE).

**Modello**: Ritz a 1 termine (u = a1 x) = una barra L2; Ritz a 2 termini (u = a1 x + a2 x^2) = una barra L3 con carico lineare consistente (add_bar_line_load con valori nodali); 3 termini (soluzione esatta) confrontati con 32 barre L3.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| u(L), Ritz a 1 termine [L] | esatta | 1.2 | 1.2 | 0.00 % | ✅ |
| sigma, Ritz a 1 termine [F/L2] | esatta | 4 | 4 | 0.00 % | ✅ |
| u(L/2), Ritz a 2 termini [L] | esatta | 0.825 | 0.825 | 0.00 % | ✅ |
| u(L), Ritz a 2 termini [L] | esatta | 1.2 | 1.2 | 0.00 % | ✅ |
| sigma(0), Ritz a 2 termini [F/L2] | esatta | 7 | 7 | 0.00 % | ✅ |
| u(L) = c L^3/(3AE), 3 termini (esatta) [L] | esatta | 1.2 | 1.2 | 0.00 % | ✅ |

**Note**: Un elemento finito con la stessa base polinomiale del Ritz classico (che soddisfa u(0) = 0) riproduce esattamente la soluzione di Ritz: si confrontano spostamenti e tensioni in x = L/2 e x = L. Formule ricostruite (OCR parziale).

*Codice*: `validation/books/cook/ch03.py`

<a id="cook-3-7-bar-variational-derivation"></a>

### Sez. 3.7, Eq. 3.7-8..3.7-15 - Barra con carico distribuito e tensione di estremita': equazione di Eulero e condizione naturale

📐 · pag. PDF 105 · bar

La stazionarieta' del funzionale della barra da' l'equazione di Eulero AE u'' + q = 0 e la condizione naturale E u'(L) = sigma_L. Con q uniforme la soluzione e' quadratica.

**Modello**: 4 barre L3 di feagent (A = 2, E = 50, L = 4) con q = 3 uniforme e tensione sigma_L = 5 all'estremo libero (forza sigma_L A).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| u(L) [L] | esatta | 0.64 | 0.64 | 0.00 % | ✅ |
| u(L/2) [L] | esatta | 0.38 | 0.38 | 0.00 % | ✅ |
| E u'(L) = sigma_L (condizione naturale) [F/L2] | esatta | 5 | 5 | 0.00 % | ✅ |
| AE u'' = -q (equazione di Eulero) [F/L] | esatta | -3 | -3 | 0.00 % | ✅ |

**Note**: La barra quadratica contiene la soluzione esatta: spostamenti nodali, tensione all'estremo (condizione naturale soddisfatta solo in forma debole, qui esatta) e curvatura u'' = -q/(AE) ricavata dagli spostamenti.

*Codice*: `validation/books/cook/ch03.py`

<a id="cook-3-9-bar-linear-load-three-elements"></a>

### Sez. 3.9, Fig. 3.9-1, Eq. 3.9-6..3.9-12 - Barra con carico lineare q = cx modellata con tre elementi lineari

✅ · pag. PDF 111 · bar

La barra dell'esempio di Ritz modellata con tre elementi lineari: i carichi consistenti sono c L^2/6 {1, 6, 12, 8}, gli spostamenti nodali c L^3/(3AE) {0, 13, 23, 27} coincidono con l'esatta, le tensioni sono costanti a tratti (13, 10, 4) c L^2/(3A) e a meta' dell'elemento 2 lo spostamento FE vale 6,0 contro 6,1875 c L^3/(AE).

**Modello**: Tre barre L2 di feagent di lunghezza L, incastro in x = 0, carico lineare q = c x con add_bar_line_load (valori nodali); c = L = A = E = 1.

![cook-3.9-bar-linear-load-three-elements](images/books/cook-3.9-bar-linear-load-three-elements.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| carico consistente al nodo 1 [F] | libro | 0.16667 | 0.16667 | 0.00 % | ✅ |
| carico consistente al nodo 2 [F] | libro | 1 | 1 | 0.00 % | ✅ |
| carico consistente al nodo 3 [F] | libro | 2 | 2 | 0.00 % | ✅ |
| carico consistente al nodo 4 [F] | libro | 1.3333 | 1.3333 | 0.00 % | ✅ |
| u2 [L] | libro | 4.3333 | 4.3333 | 0.00 % | ✅ |
| u3 [L] | libro | 7.6667 | 7.6667 | 0.00 % | ✅ |
| u4 [L] | libro | 9 | 9 | 0.00 % | ✅ |
| u FE a meta' dell'elemento 2 [cL3/AE] | libro | 6 | 6 | 0.00 % | ✅ |
| u esatta a meta' dell'elemento 2 [cL3/AE] | libro | 6.1875 | 6.1875 | 0.00 % | ✅ |
| sigma1 [F/L2] | libro | 4.3333 | 4.3333 | 0.00 % | ✅ |
| sigma2 [F/L2] | libro | 3.3333 | 3.3333 | 0.00 % | ✅ |
| sigma3 [F/L2] | libro | 1.3333 | 1.3333 | 0.00 % | ✅ |

**Note**: Il vettore dei carichi e' ricostruito da Eq. 3.9-6/3.9-9 (OCR parziale) e verificato: risultante c (3L)^2/2.

*Codice*: `validation/books/cook/ch03.py`

<a id="cook-3-10-heat-conduction-functional"></a>

### Sez. 3.10, Eq. 3.10-1..3.10-9 - Formulazione FE della conduzione piana da un funzionale

📐 · pag. PDF 113 · heat_steady

Dal funzionale della conduzione si ottengono [k] = int B^T k B dV, [c] = int N^T N rho c dV e {r_Q} = int N^T Q dV; per il triangolo lineare: [k] = k t A B^T B, [c] = rho c t A/12 [[2,1,1],[1,2,1],[1,1,2]], r_Q = Q t A/3.

**Modello**: Elemento T3 di FieldModel('heat') (k = 2,5, spessore 0,4, rho c = 3, Q = 7) su un triangolo generico.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| conduzione k11 | esatta | 0.54545 | 0.54545 | 0.00 % | ✅ |
| conduzione k12 | esatta | -0.22727 | -0.22727 | 0.00 % | ✅ |
| conduzione k23 | esatta | -0.32576 | -0.32576 | 0.00 % | ✅ |
| conduzione k33 | esatta | 0.64394 | 0.64394 | 0.00 % | ✅ |
| capacita' c11 | esatta | 0.264 | 0.264 | 0.00 % | ✅ |
| capacita' c12 | esatta | 0.132 | 0.132 | 0.00 % | ✅ |
| sorgente Q t A/3 per nodo | esatta | 1.232 | 1.232 | 0.00 % | ✅ |

**Note**: Il libro scrive le matrici in forma generale; i valori chiusi del triangolo lineare sono quelli classici dello stesso libro (Sez. 5.4).

*Codice*: `validation/books/cook/ch03.py`

<a id="cook-3-12-lagrange-shape-functions"></a>

### Sez. 3.12, Eq. 3.12-2, 3.12-3, 3.12-6, 3.12-10 - Funzioni di forma C0: lineari, quadratiche (Lagrange) e bilineari

📐 · pag. PDF 116 · shape_functions

Funzioni di Lagrange 1D lineari e quadratiche e funzioni bilineari del rettangolo N1 = (a - x)(b - y)/(4ab), ...

**Modello**: Barre L2 e L3 di feagent (nodo medio centrale) e PlaneQ4 rettangolare 2a x 2b (a = 1,5, b = 0,8) centrato nell'origine.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| L2 N1(1.7) | esatta | 0.76667 | 0.76667 | 0.00 % | ✅ |
| L2 N2(1.7) | esatta | 0.23333 | 0.23333 | 0.00 % | ✅ |
| L3 N1(1.7) | esatta | 0.40889 | 0.40889 | 0.00 % | ✅ |
| L3 N2(1.7) | esatta | -0.12444 | -0.12444 | 0.00 % | ✅ |
| L3 N3(1.7) | esatta | 0.71556 | 0.71556 | 0.00 % | ✅ |
| L2 N1(3.2) | esatta | 0.26667 | 0.26667 | 0.00 % | ✅ |
| L2 N2(3.2) | esatta | 0.73333 | 0.73333 | 0.00 % | ✅ |
| L3 N1(3.2) | esatta | -0.12444 | -0.12444 | 0.00 % | ✅ |
| L3 N2(3.2) | esatta | 0.34222 | 0.34222 | 0.00 % | ✅ |
| L3 N3(3.2) | esatta | 0.78222 | 0.78222 | 0.00 % | ✅ |
| bilineare N1(0,4; -0,3) | esatta | 0.25208 | 0.25208 | 0.00 % | ✅ |
| bilineare N2(0,4; -0,3) | esatta | 0.43542 | 0.43542 | 0.00 % | ✅ |
| bilineare N3(0,4; -0,3) | esatta | 0.19792 | 0.19792 | 0.00 % | ✅ |
| bilineare N4(0,4; -0,3) | esatta | 0.11458 | 0.11458 | 0.00 % | ✅ |

**Note**: Funzioni valutate in punti di prova; nel Q4 x = a xi, y = b eta.

*Codice*: `validation/books/cook/ch03.py`

<a id="cook-3-13-hermite-cubic-shape-functions"></a>

### Sez. 3.13, Eq. 3.13-1..3.13-4 - Interpolazione cubica di Hermite (funzioni di forma della trave)

📐 · pag. PDF 120 · beam, shape_functions

Funzioni di Hermite: N1 = 1 - 3x^2/L^2 + 2x^3/L^3, N2 = x - 2x^2/L + x^3/L^2, N3 = 3x^2/L^2 - 2x^3/L^3, N4 = -x^2/L + x^3/L^2.

**Modello**: BeamElement3D.shape_functions di feagent su una trave di lunghezza L = 2,5.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| N1(0.6) | esatta | 0.85485 | 0.85485 | 0.00 % | ✅ |
| N2(0.6) | esatta | 0.34656 | 0.34656 | 0.00 % | ✅ |
| N3(0.6) | esatta | 0.14515 | 0.14515 | 0.00 % | ✅ |
| N4(0.6) | esatta | -0.10944 | -0.10944 | 0.00 % | ✅ |
| N1(1.9) | esatta | 0.14515 | 0.14515 | 0.00 % | ✅ |
| N2(1.9) | esatta | 0.10944 | 0.10944 | 0.00 % | ✅ |
| N3(1.9) | esatta | 0.85485 | 0.85485 | 0.00 % | ✅ |
| N4(1.9) | esatta | -0.34656 | -0.34656 | 0.00 % | ✅ |

**Note**: Righe di N(x) di feagent relative a (v1, theta1, v2, theta2) nel piano x-y.

*Codice*: `validation/books/cook/ch03.py`

## Capitolo 4

<a id="cook-4-2-element-stiffness-overview"></a>

### Sez. 4.2, Eq. 4.2-1..4.2-19 - Matrici di rigidezza di barra, trave, telaio piano, CST, rettangolo bilineare, brick trilineare

📐 · pag. PDF 133 · beam, plane, solid

Formule generali [k] = int B^T E B dV per i principali elementi: trave di Hermite (Eq. 4.2-5), telaio piano 6x6 (Eq. 4.2-8), CST [k] = t A B^T E B, rettangolo bilineare e brick trilineare.

**Modello**: BeamElement3D (EA = 5, EI = 3, L = 2), PlaneT3, PlaneQ4 rettangolare 2a x 2b e Hex8 a parallelepipedo di feagent (E = 7, nu = 0,3, t = 0,5).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| telaio piano 6x6, scarto massimo da Eq. 4.2-8 | esatta | 0 | 0 | 0.00 % | ✅ |
| trave 4EI/L | esatta | 6 | 6 | 0.00 % | ✅ |
| CST, scarto massimo da t A B^T E B | esatta | 0 | 4.4409e-16 | 0.00 % | ✅ |
| rettangolo bilineare, scarto massimo | esatta | 0 | 1.7764e-15 | 0.00 % | ✅ |
| brick trilineare, scarto massimo relativo | esatta | 0 | 7.5527e-16 | 0.00 % | ✅ |

**Note**: Per rettangolo e brick il riferimento e' l'integrazione numerica indipendente (Gauss 4x4 e 4x4x4 con numpy) delle funzioni di forma scritte come nel libro.

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-3-bar-initial-strain-thermal"></a>

### Sez. 4.3, Fig. 4.3-1, Eq. 4.3-1, 4.3-2 - Carichi nodali di una barra troppo lunga di DeltaL e riscaldata di T

📐 · pag. PDF 138 · bar, thermal

Una barra troppo lunga di DeltaL e riscaldata di T ha deformazione iniziale eps0 = DeltaL/L + alfa T e carichi nodali {-F, F} con F = E A eps0; montata fra due incastri ha tensione -E eps0.

**Modello**: Barra L2 di feagent (A = 2, E = 100, L = 4, alfa = 1e-4, T = 30, DeltaL = 0,002) incastrata ai due estremi; la deformazione iniziale DeltaL/L si assegna come temperatura equivalente.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| carico nodale r1 = -F [F] | esatta | -0.7 | -0.7 | 0.00 % | ✅ |
| carico nodale r2 = F [F] | esatta | 0.7 | 0.7 | 0.00 % | ✅ |
| tensione fra due incastri [F/L2] | esatta | -0.35 | -0.35 | 0.00 % | ✅ |

**Note**: Valori di prova.

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-3-bar-consistent-loads"></a>

### Sez. 4.3 (Example: Bar Element), Eq. 4.3-6..4.3-9 - Carichi consistenti di una barra con carico lineare e forza concentrata a 2L/3

📐 · pag. PDF 140 · bar

Carico lineare da q1 a q2: {r} = L/6 {2q1 + q2, q1 + 2q2}; forza F in x = 2L/3: {r} = {F/3, 2F/3}.

**Modello**: Barra L2 di feagent (L = 3): equivalent_line_load con q1 = 2, q2 = 5 e funzioni di forma in x = 2L/3 per F = 9.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| r1 q [F] | esatta | 4.5 | 4.5 | 0.00 % | ✅ |
| r2 q [F] | esatta | 6 | 6 | 0.00 % | ✅ |
| r1 F [F] | esatta | 3 | 3 | 0.00 % | ✅ |
| r2 F [F] | esatta | 6 | 6 | 0.00 % | ✅ |

**Note**: Valori di prova.

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-3-plane-solid-consistent-loads"></a>

### Sez. 4.3, Fig. 4.3-3..4.3-5, Eq. 4.3-10, 4.3-11 - Carichi consistenti su lati lineari e quadratici, forza al centro di Q8, faccia caricata

📐 · pag. PDF 141 · plane

Carichi consistenti: lato lineare con pressione lineare L/6 (2p1 + p2, p1 + 2p2); lato quadratico con q uniforme qL/6, 2qL/3, qL/6; forza F al centro di un Q8: -F/4 ai vertici e F/2 ai nodi di meta' lato; faccia a 8 nodi con sigma uniforme: -sigma A/12 ai vertici e sigma A/3 ai lati; Q4 1/4 per nodo.

**Modello**: PlaneQ8 e PlaneQ4 rettangolari di feagent (3 x 2, t = 1): lato quadratico con trazione uniforme, funzioni di forma al centro del Q8, forza di volume uniforme su Q4 e Q8; barra L2 per il lato lineare con carico lineare.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| lato quadratico: qL/6 al vertice [F] | esatta | 1.3333 | 1.3333 | 0.00 % | ✅ |
| lato quadratico: 2qL/3 al nodo medio [F] | esatta | 5.3333 | 5.3333 | 0.00 % | ✅ |
| F al centro del Q8: -F/4 ai vertici [F/F] | esatta | -0.25 | -0.25 | 0.00 % | ✅ |
| F al centro del Q8: F/2 ai nodi medi [F/F] | esatta | 0.5 | 0.5 | 0.00 % | ✅ |
| faccia a 8 nodi: -sigma A/12 al vertice [F] | esatta | -3 | -3 | 0.00 % | ✅ |
| faccia a 8 nodi: sigma A/3 al lato [F] | esatta | 12 | 12 | 0.00 % | ✅ |
| Q4: 1/4 per nodo [F] | esatta | 9 | 9 | 0.00 % | ✅ |
| lato lineare, pressione lineare: L/6 (2p1 + p2) [F] | esatta | 2.55 | 2.55 | 0.00 % | ✅ |

**Note**: I valori P e Q della faccia a 8 nodi (Fig. 4.3-5) non sono nel testo OCR: -sigma A/12 e sigma A/3 sono l'integrale delle funzioni serendipity, identico a quello di una forza di volume uniforme sul Q8.

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-3-beam-consistent-loads"></a>

### Sez. 4.3, Fig. 4.3-6, Eq. 4.3-12 - Carichi consistenti di trave per q uniforme, P e M in mezzeria

📐 · pag. PDF 143 · beam

q uniforme: forze qL/2 e momenti qL^2/12; P in mezzeria: P/2 e PL/8; coppia M in mezzeria: forze -3M/(2L), +3M/(2L) e momenti -M/4, -M/4 (Eq. 4.3-12 con dN/dx in L/2).

**Modello**: BeamElement3D di feagent (L = 2): vettori equivalenti dei carichi DistributedLoad e ConcentratedElementLoad (forza e coppia in xi = 0,5).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| carico q: F1 | esatta | 3 | 3 | 0.00 % | ✅ |
| carico q: M1 | esatta | 1 | 1 | 0.00 % | ✅ |
| carico q: F2 | esatta | 3 | 3 | 0.00 % | ✅ |
| carico q: M2 | esatta | -1 | -1 | 0.00 % | ✅ |
| carico P: F1 | esatta | 2.5 | 2.5 | 0.00 % | ✅ |
| carico P: M1 | esatta | 1.25 | 1.25 | 0.00 % | ✅ |
| carico P: F2 | esatta | 2.5 | 2.5 | 0.00 % | ✅ |
| carico P: M2 | esatta | -1.25 | -1.25 | 0.00 % | ✅ |
| carico M: F1 | esatta | -5.25 | -5.25 | 0.00 % | ✅ |
| carico M: M1 | esatta | -1.75 | -1.75 | 0.00 % | ✅ |
| carico M: F2 | esatta | 5.25 | 5.25 | 0.00 % | ✅ |
| carico M: M2 | esatta | -1.75 | -1.75 | 0.00 % | ✅ |

**Note**: Gdl (v1, theta1, v2, theta2) del piano x-y locale; i segni dei momenti della coppia seguono dN/dx in L/2 (Fig. 4.3-6c).

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-3-cantilever-udl-lumped-vs-consistent"></a>

### Sez. 4.3 (Error Produced by Lumping), Fig. 4.3-7, Eq. 4.3-13, 4.3-14 - Mensola con carico uniforme: carichi consistenti contro concentrati

✅ · pag. PDF 143 · beam

Con i carichi consistenti freccia e rotazione all'estremo sono esatte per ogni n (qL^4/8EI, qL^3/6EI); trascurando i momenti nodali l'errore della freccia e' 33,3, 8,3, 3,7, 2,1 % e quello della rotazione 50, 12,5, 5,6, 3,1 %.

**Modello**: Mensola con n = 1, 2, 3, 4 travi di Hermite di feagent (EI = L = q = 1): carico distribuito (consistente) oppure sole forze nodali qL/2 per elemento.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| freccia, carichi consistenti, n = 1 [qL4/EI] | esatta | 0.125 | 0.125 | 0.00 % | ✅ |
| rotazione, carichi consistenti, n = 1 [qL3/EI] | esatta | 0.16667 | 0.16667 | 0.00 % | ✅ |
| errore della freccia con carichi concentrati, n = 1 [%] | libro | 33.3 | 33.333 | 0.10 % | ✅ |
| errore della rotazione con carichi concentrati, n = 1 [%] | libro | 50 | 50 | 0.00 % | ✅ |
| errore della freccia con carichi concentrati, n = 2 [%] | libro | 8.3 | 8.3333 | 0.40 % | ✅ |
| errore della rotazione con carichi concentrati, n = 2 [%] | libro | 12.5 | 12.5 | 0.00 % | ✅ |
| errore della freccia con carichi concentrati, n = 3 [%] | libro | 3.7 | 3.7037 | 0.10 % | ✅ |
| errore della rotazione con carichi concentrati, n = 3 [%] | libro | 5.6 | 5.5556 | 0.79 % | ✅ |
| freccia, carichi consistenti, n = 4 [qL4/EI] | esatta | 0.125 | 0.125 | 0.00 % | ✅ |
| rotazione, carichi consistenti, n = 4 [qL3/EI] | esatta | 0.16667 | 0.16667 | 0.00 % | ✅ |
| errore della freccia con carichi concentrati, n = 4 [%] | libro | 2.1 | 2.0833 | 0.79 % | ✅ |
| errore della rotazione con carichi concentrati, n = 4 [%] | libro | 3.1 | 3.125 | 0.81 % | ✅ |

**Note**: Errori percentuali confrontati con i valori stampati (1 cifra decimale).

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-4-equilibrium-compatibility"></a>

### Sez. 4.4, 4.5, Fig. 4.4-1, 4.4-2, 4.5-1..4.5-3 - Equilibrio e compatibilita' nella soluzione FE; requisiti di convergenza

📐 · pag. PDF 144 · plane

La soluzione FE rispetta l'equilibrio ai nodi ma non fra gli elementi (le tensioni saltano sui lati comuni); un elemento deve rappresentare i moti rigidi senza deformazioni (requisito di convergenza).

**Modello**: Mensola 4 x 1 di 16 CST (PlaneT3) caricata all'estremo; un CST isolato sottoposto a una piccola rotazione rigida.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| equilibrio al nodo interno: somma delle forze d'elemento [F] | esatta | 0 | 4.6407e-14 | 0.00 % | ✅ |
| salto di sigma_x fra due CST adiacenti (non nullo) [F/L2] | libro | - | 7.9282 | - | ✅ |
| deformazioni del CST in rotazione rigida | esatta | 0 | 1.0842e-19 | 0.00 % | ✅ |

**Note**: Il libro e' qualitativo: si verificano il residuo nodale (somma delle forze d'elemento = carico applicato), la deformazione nulla del CST in rotazione rigida e si riporta il salto di trazione normale su un lato interno.

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-6-patch-test-q4"></a>

### Sez. 4.6, Fig. 4.6-1 - Patch test di quattro quadrilateri a 4 nodi in tensione uniforme sigma_x

✅ · pag. PDF 150 · plane

Patch di quattro quadrilateri irregolari caricata con le forze consistenti di uno stato di tensione uniforme sigma_x = sigma_c: l'elemento supera il patch test se le tensioni calcolate sono esattamente sigma_x = sigma_c, sigma_y = tau_xy = 0 in ogni punto.

**Modello**: Quattro PlaneQ4 di feagent (spessore 1, E = 1000, nu = 0,3) con i nodi della Fig. 4.6-1; nodo 1 bloccato in x e y, nodo 2 in x; forze F ai nodi 7 e 9, 2F al nodo 8, 1,5F verso -x al nodo 3, F = sigma_c = 10. Variante: u3 = 0 al posto della forza 1,5F; ripetuto con i QM6 (incompatible=True).

![cook-4.6-patch-test-q4](images/books/cook-4.6-patch-test-q4.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| Q4: massimo scarto da sigma_x = sigma_c, sigma_y = tau = 0 | esatta | 0 | 1.0658e-14 | 0.00 % | ✅ |
| reazione al nodo 2 [F] | esatta | -20 | -20 | 0.00 % | ✅ |
| Q4 u3=0: massimo scarto da sigma_x = sigma_c, sigma_y = tau = 0 | esatta | 0 | 1.0658e-15 | 0.00 % | ✅ |
| QM6: massimo scarto da sigma_x = sigma_c, sigma_y = tau = 0 | esatta | 0 | 3.5527e-15 | 0.00 % | ✅ |

**Note**: Coordinate dalla figura; il nodo interno 5 non e' quotato e si pone in (2,3; 2,4) (ininfluente per il patch test). Le reazioni sono 0,5F al nodo 1 e 2F al nodo 2.

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-7-bar-self-weight-fixed-ends"></a>

### Sez. 4.7, Fig. 4.7-1, Eq. 4.7-3 - Barra verticale sotto peso proprio incastrata agli estremi: 1, 2, 4 elementi

✅ · pag. PDF 153 · bar

La tensione esatta varia linearmente da +sigma_c in alto a -sigma_c in basso (sigma_c = W/2A). Un elemento da' sigma = 0, due elementi +-sigma_c/2, quattro elementi 3/4, 1/4, -1/4, -3/4 sigma_c. Aggiungendo in ogni elemento la correzione di Eq. 4.7-3 si ritrova la soluzione esatta.

**Modello**: Barra verticale (asse Y) di 1, 2 e 4 elementi L2 di feagent, incastrata in alto e in basso, forza di volume -gamma in Y (add_solid_body_force); W = gamma A L = 2, A = 1.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| 1 elementi: tensione dell'elemento 1 (dall'alto) [sigma_c] | libro | 0 | 0 | 0.00 % | ✅ |
| 2 elementi: tensione dell'elemento 1 (dall'alto) [sigma_c] | libro | 0.5 | 0.5 | 0.00 % | ✅ |
| 2 elementi: tensione dell'elemento 2 (dall'alto) [sigma_c] | libro | -0.5 | -0.5 | 0.00 % | ✅ |
| 2 elementi + correzione di Eq. 4.7-3: sigma in alto [sigma_c] | esatta | 1 | 1 | 0.00 % | ✅ |
| 4 elementi: tensione dell'elemento 1 (dall'alto) [sigma_c] | libro | 0.75 | 0.75 | 0.00 % | ✅ |
| 4 elementi: tensione dell'elemento 2 (dall'alto) [sigma_c] | libro | 0.25 | 0.25 | 0.00 % | ✅ |
| 4 elementi: tensione dell'elemento 3 (dall'alto) [sigma_c] | libro | -0.25 | -0.25 | 0.00 % | ✅ |
| 4 elementi: tensione dell'elemento 4 (dall'alto) [sigma_c] | libro | -0.75 | -0.75 | 0.00 % | ✅ |

**Note**: Valori dei modelli a 2 e 4 elementi letti dalla figura.

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-7-cst-linear-temperature"></a>

### Sez. 4.7 (Thermal Stress), Fig. 4.7-2, Eq. 4.7-4, 4.7-5 - Tensioni spurie in un CST con campo di temperatura lineare

📐 · pag. PDF 154 · plane, thermal

Il CST ha deformazione costante: un campo termico lineare con media nulla da' carichi nodali nulli, quindi spostamenti nulli e tensioni sigma_x = sigma_y = -E alfa T/(1 - nu), spurie ovunque tranne al baricentro (la soluzione esatta di una lastra libera con T lineare e' a tensioni nulle).

**Modello**: Un PlaneT3 di feagent con baricentro nell'origine (E = 200, nu = 0,25, alfa = 1e-2) e temperature nodali T = c1 x + c2 y (c1 = 3, c2 = -2), add_solid_thermal con valori nodali; elemento sostenuto in modo isostatico.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| carichi nodali termici nulli [F] | esatta | 0 | 2.2204e-16 | 0.00 % | ✅ |
| sigma_x al vertice 2 = -E alfa T/(1-nu) [F/L2] | esatta | -12.267 | -12.267 | 0.00 % | ✅ |
| sigma_y vertice 2 [F/L2] | esatta | -12.267 | -12.267 | 0.00 % | ✅ |
| tau vertice 2 [F/L2] | esatta | 0 | 4.6259e-17 | 0.00 % | ✅ |
| sigma_x al baricentro (corretta) [F/L2] | esatta | 0 | 4.5334e-16 | 0.00 % | ✅ |

**Note**: Tensioni valutate al vertice 2 e al baricentro.

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-7-q4-beam-linear-temperature"></a>

### Sez. 4.7 (controesempio), Fig. 4.2-4 - Trave fra pareti rigide con temperatura lineare nello spessore (un elemento bilineare)

📐 · pag. PDF 154 · plane, thermal

Con i nodi bloccati il bilineare ha deformazioni nulle: usando la temperatura reale si ottiene sigma_x = sigma_y = -E alfa T, giusta per sigma_x (trave fra pareti rigide) ma errata per sigma_y, che dovrebbe essere nulla.

**Modello**: Un PlaneQ4 rettangolare 4 x 1 (E = 100, nu = 0, alfa = 1e-3) con tutti i nodi bloccati e temperature nodali T = T0 y (T0 = 20, y dal piano medio).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| sigma_x al punto di Gauss 1 = -E alfa T [F/L2] | esatta | 0.57735 | 0.57735 | 0.00 % | ✅ |
| sigma_y al punto di Gauss 1 (errata, esatta = 0) [F/L2] | libro | 0.57735 | 0.57735 | 0.00 % | ✅ |
| sigma_x al punto di Gauss 4 = -E alfa T [F/L2] | esatta | -0.57735 | -0.57735 | 0.00 % | ✅ |
| sigma_y al punto di Gauss 4 (errata, esatta = 0) [F/L2] | libro | -0.57735 | -0.57735 | 0.00 % | ✅ |

**Note**: Il libro discute anche una temperatura 'ridotta' coerente con il campo di deformazione dell'elemento (sigma_x = sigma_y = 0), proposta che feagent non adotta: si confronta il caso con la temperatura reale.

*Codice*: `validation/books/cook/ch04.py`

<a id="cook-4-7-plate-hole-stress-improvement"></a>

### Sez. 4.7, Fig. 4.7-3, Tabella 4.7-1, Eq. 4.7-8 - Piastra con foro centrale: tensione al bordo del foro, metodo standard e iterativo

🟡 · pag. PDF 155 · plane

Striscia tesa con foro di diametro pari a meta' larghezza: tensione sigma_x nel punto A al bordo del foro. Il libro confronta il metodo standard (2x2: 30,9; 4x4: 36,6) con un metodo iterativo di miglioramento delle tensioni e con l'esatto 43,2 (Kt = 2,16 sulla sezione netta), e corregge il caso di una piastra caricata da forze concentrate con il rapporto del caso secondario (Eq. 4.7-8: 103 e 112).

**Modello**: Quarto di striscia forata (larghezza 2, foro di diametro 1, semilunghezza 3, E = 1, nu = 0,3, sigma = 10 lontano dal foro): blocco mappato attorno al foro piu' blocco rettangolare; Q4 con maglie 2x2 e 4x4 nel quadrante del foro (tensione in A estrapolata dai 4 punti di Gauss) e Q9 16x16 come soluzione convergente.

![cook-4.7-plate-hole-stress-improvement](images/books/cook-4.7-plate-hole-stress-improvement.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| sigma_x in A, Q4 2x2 standard (libro 30.9, maglia diversa) [F/L2] | libro | - | 28.805 | - | ✅ |
| sigma_x in A, Q4 4x4 standard (libro 36.6, maglia diversa) [F/L2] | libro | - | 37.646 | - | ✅ |
| sigma_x in A, Q9 16x16 (esatto del libro 43,2) [F/L2] | libro | 43.2 | 43.546 | 0.80 % | ✅ |
| correzione col caso secondario, 2x2 [F/L2] | libro | 103 | 102.9 | 0.10 % | ✅ |
| correzione col caso secondario, 4x4 [F/L2] | libro | 112 | 112.49 | 0.43 % | ✅ |

**Note**: Il libro non da' dimensioni, lunghezza del modello ne' coordinate delle maglie: la geometria e' ricostruita (sigma lorda 10 per avere 43,2). I valori Q4 2x2 e 4x4 di feagent (circa 28,9 e 37,8) sono solo riportati, perche' la maglia del libro e' diversa. Il metodo iterativo (Eq. 4.7-7) non e' in feagent; il caso b (forze concentrate, P non dato, riferimento con Q9 8x8) non e' riproducibile. Si verificano la soluzione convergente contro 43,2 e l'aritmetica di Eq. 4.7-8. Un modello limitato al solo quadrato attorno al foro, con la trazione applicata a distanza b dal centro, convergerebbe a circa 64: il modello del libro deve comprendere un tratto di striscia piu' lungo.

*Codice*: `validation/books/cook/ch04.py`

## Capitolo 5

<a id="cook-5-1-natural-coordinates-bar"></a>

### Sez. 5.1, Eq. 5.1-1..5.1-12 - Coordinate naturali lineari e barra a 2 e 3 nodi

📐 · pag. PDF 167 · bar

Con le coordinate naturali lineari xi1, xi2 la barra ha [k] = AE/L [[1,-1],[-1,1]] e vale la formula int xi1^k xi2^l dL = k! l! L/(k + l + 1)!.

**Modello**: Barre L2 e L3 di feagent (L = 2,5, A = 1,2, E = 30); funzioni di forma usate come coordinate naturali xi1 = N1, xi2 = N2 e integrate con la quadratura di Gauss.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| k11 [F/L] | esatta | 14.4 | 14.4 | 0.00 % | ✅ |
| k12 [F/L] | esatta | -14.4 | -14.4 | 0.00 % | ✅ |
| int xi1^1 xi2^1 [L] | esatta | 0.41667 | 0.41667 | 0.00 % | ✅ |
| int xi1^2 xi2^1 [L] | esatta | 0.20833 | 0.20833 | 0.00 % | ✅ |
| int xi1^3 xi2^2 [L] | esatta | 0.041667 | 0.041667 | 0.00 % | ✅ |
| barra a 3 nodi: k11 = 7AE/(3L) [F/L] | esatta | 33.6 | 33.6 | 0.00 % | ✅ |

**Note**: Formula d'integrazione verificata per (k, l) = (1, 1), (2, 1), (3, 2).

*Codice*: `validation/books/cook/ch05.py`

<a id="cook-5-2-area-volume-coordinates"></a>

### Sez. 5.2, Eq. 5.2-1..5.2-13 - Coordinate d'area e di volume, derivate e formule di integrazione

📐 · pag. PDF 169 · shape_functions

Coordinate d'area: 2A = x21 y31 - x31 y21, int xi1^k xi2^l xi3^m dA = 2A k! l! m!/(2 + k + l + m)!; analoga formula di volume con 6V e (3 + ...)!.

**Modello**: PlaneT3 e Tet10 di feagent; quadrature feagent.solid.gauss_tri e gauss_tet.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| 2A = x21 y31 - x31 y21 [L2] | esatta | 3.17 | 3.17 | 0.00 % | ✅ |
| int L^(1,1,0) dA [L2] | esatta | 0.13208 | 0.13208 | 0.00 % | ✅ |
| int L^(2,1,1) dA [L2] | esatta | 0.0088056 | 0.0088056 | 0.00 % | ✅ |
| int L^(2,2,1) dA [L2] | esatta | 0.0025159 | 0.0025159 | 0.00 % | ✅ |
| int L^(1,1,1,0) dV [L3] | esatta | 0.0036111 | 0.0036111 | 0.00 % | ✅ |
| int L^(2,1,0,1) dV [L3] | esatta | 0.0010317 | 0.0010317 | 0.00 % | ✅ |
| volume Tet10 [L3] | esatta | 0.43333 | 0.43333 | 0.00 % | ✅ |

**Note**: Le coordinate d'area sono le funzioni di forma del T3; si integra sul triangolo reale con le regole di feagent.

*Codice*: `validation/books/cook/ch05.py`

<a id="cook-5-4-cst-formulation"></a>

### Sez. 5.4, Eq. 5.4-1..5.4-7 - Triangolo lineare: campo scalare e triangolo a deformazione costante (CST)

📐 · pag. PDF 174 · plane

[B] = 1/(2A) [[y23, 0, y31, 0, y12, 0], [0, x32, 0, x13, 0, x21], [x32, y23, x13, y31, x21, y12]] e [k] = t A [B]^T [E] [B].

**Modello**: PlaneT3 di feagent su un triangolo generico (E = 5, nu = 0,2, t = 0,3).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| scarto massimo di [B] [1/L] | esatta | 0 | 1.1102e-16 | 0.00 % | ✅ |
| scarto massimo di [k] [F/L] | esatta | 0 | 2.2204e-16 | 0.00 % | ✅ |

**Note**: Confronto di tutti i termini di [B] (righe exx, eyy, gxy) e di [k].

*Codice*: `validation/books/cook/ch05.py`

<a id="cook-5-4-cst-pure-bending"></a>

### Sez. 5.4, Fig. 5.4-1, Eq. 5.4-8, 5.4-9 - CST in flessione pura: deformazioni errate

📐 · pag. PDF 176 · plane

Campo esatto u = u_bar x y/(c l), v = u_bar (-x^2 - nu y^2)/(2 c l): eps_x = u_bar y/(c l), gamma = 0. Il CST con gli stessi spostamenti nodali da' eps_x = eps_y = 0 e uno scorrimento spurio gamma = (1/c - nu c/(4 l^2)) u_bar (Eq. 5.4-9): rappresenta la flessione con taglio invece che con deformazioni assiali.

**Modello**: PlaneT3 di feagent con nodi (l, -c), (l, c), (-l, 0) (l = 2, c = 0,5, nu = 0,3) e spostamenti nodali presi dal campo di flessione pura (u_bar = 0,01).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| eps_x del CST (esatta: u_bar y/(c l)) | esatta | 0 | 0 | 0.00 % | ✅ |
| eps_y CST | esatta | 0 | 0 | 0.00 % | ✅ |
| gamma_xy spuria del CST (Eq. 5.4-5 sugli spostamenti nodali) | esatta | 0.019906 | 0.019906 | 0.00 % | ✅ |
| gamma_xy = (1/c - nu c/(4 l^2)) u_bar (Eq. 5.4-9, esatta 0) | libro | 0.019906 | 0.019906 | 0.00 % | ✅ |

**Note**: Eq. 5.4-9 letta dall'immagine della pagina: gamma_xy = (1/c - nu c/(4 l^2)) u_bar; si confronta anche con la [B] del CST di Eq. 5.4-5 scritta a mano.

*Codice*: `validation/books/cook/ch05.py`

<a id="cook-5-4-cantilever-cst-vs-lst"></a>

### Sez. 5.4-5.5, Fig. 5.4-2 - Mensola con carico parabolico di estremita': CST contro LST

✅ · pag. PDF 176 · plane

Freccia v_A a meta' dell'estremo libero e tensione di flessione sigma_xB alla fibra superiore a distanza h dall'incastro, in rapporto alla teoria dell'elasticita': 128 CST 0,859 e 0,854; 512 CST 0,961 e 0,956; 32 LST 0,998 e 0,986. A parita' di gdl il triangolo quadratico e' molto piu' accurato.

**Modello**: Mensola 4h x h (h = 1, t = 1, E = 1, nu = 0,25) con tutti i nodi dell'estremo sinistro bloccati e carico parabolico P = 1 a destra: 128 CST (16 x 4 rettangoli con diagonali alternate, 160 gdl), 512 CST (32 x 8, 576 gdl), 32 LST (8 x 2 rettangoli, 160 gdl) e, come riferimento, 2048 LST (64 x 16).

![cook-5.4-cantilever-cst-vs-lst](images/books/cook-5.4-cantilever-cst-vs-lst.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| v_A / v_rif, 128 CST | libro | 0.859 | 0.86535 | 0.74 % | ✅ |
| sigma_xB / (Mc/I), 128 CST | libro | 0.854 | 0.85271 | 0.15 % | ✅ |
| v_A / v_rif, 512 CST | libro | 0.961 | 0.96184 | 0.09 % | ✅ |
| sigma_xB / (Mc/I), 512 CST | libro | 0.956 | 0.95574 | 0.03 % | ✅ |
| v_A / v_rif, 32 LST | libro | 0.998 | 0.9971 | 0.09 % | ✅ |
| sigma_xB / (Mc/I), 32 LST | libro | 0.986 | 0.98549 | 0.05 % | ✅ |

**Note**: Rapporti: v_A rispetto alla soluzione convergente con lo stesso incastro (2048 LST, v = 267,0 contro 256 della sola flessione e 268 della trave di Timoshenko), sigma_xB rispetto a M c/I = 18. Per i CST sigma_xB e' la media dei due triangoli con un lato sulla fibra superiore che concorrono in B (cosi' si ritrovano 0,854 e 0,956), per gli LST la media nodale. Disposizione delle diagonali letta dalla figura. Scarto massimo 0,7 % (v_A dei 128 CST).

*Codice*: `validation/books/cook/ch05.py`

<a id="cook-5-5-lst-formulation"></a>

### Sez. 5.5, Eq. 5.5-1..5.5-10 - Triangolo quadratico (LST) a lati retti

📐 · pag. PDF 177 · plane

Il libro scrive il campo quadratico completo u = a1 + a2 x + ... + a6 y^2, ricava [B] = [Q][H] e [k] = int [B]^T [E] [B] t dA; un carico di bordo uniforme si ripartisce sui tre nodi del lato come 1-4-1.

**Modello**: PlaneT6 di feagent su un triangolo generico a lati retti (E = 3, nu = 0,25, t = 0,5).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| scarto relativo massimo di [k] | esatta | 0 | 2.0353e-15 | 0.00 % | ✅ |
| carico di bordo: 1/6 al vertice [F] | esatta | 0.31136 | 0.31136 | 0.00 % | ✅ |
| carico di bordo: 4/6 al nodo medio [F] | esatta | 1.2454 | 1.2454 | 0.00 % | ✅ |

**Note**: Riferimento costruito con numpy dal metodo del libro (inversione della matrice dei monomi ai nodi, quadratura di Gauss di grado 4 sul triangolo).

*Codice*: `validation/books/cook/ch05.py`

<a id="cook-5-6-quadratic-tetrahedron"></a>

### Sez. 5.6, Fig. 5.6-1, Eq. 5.6-1 - Funzioni di forma del tetraedro quadratico a 10 nodi

📐 · pag. PDF 179 · solid

Funzioni di forma N_i = xi_i (2 xi_i - 1) ai vertici e 4 xi_i xi_j ai nodi di spigolo; la rigidezza e' 30 x 30 con 6 moti rigidi (rango 24).

**Modello**: Tet10 di feagent su un tetraedro generico (E = 1, nu = 0,3).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| N1 | esatta | -0.1218 | -0.1218 | 0.00 % | ✅ |
| N2 | esatta | -0.1218 | -0.1218 | 0.00 % | ✅ |
| N3 | esatta | -0.1122 | -0.1122 | 0.00 % | ✅ |
| N4 | esatta | -0.1122 | -0.1122 | 0.00 % | ✅ |
| N5 | esatta | 0.2436 | 0.2436 | 0.00 % | ✅ |
| N6 | esatta | 0.1428 | 0.1428 | 0.00 % | ✅ |
| N7 | esatta | 0.1972 | 0.1972 | 0.00 % | ✅ |
| N8 | esatta | 0.3828 | 0.3828 | 0.00 % | ✅ |
| N9 | esatta | 0.2772 | 0.2772 | 0.00 % | ✅ |
| N10 | esatta | 0.2244 | 0.2244 | 0.00 % | ✅ |
| dimensione [k] | esatta | 30 | 30 | 0.00 % | ✅ |
| rango di [k] (30 - 6 moti rigidi) | esatta | 24 | 24 | 0.00 % | ✅ |

**Note**: Funzioni valutate in un punto interno di prova; spigoli nell'ordine di feagent (1-2), (2-3), (3-1), (1-4), (2-4), (3-4).

*Codice*: `validation/books/cook/ch05.py`

## Capitolo 6

<a id="cook-6-2-isoparametric-bar-3-node"></a>

### Sez. 6.2, Fig. 6.2-1, Eq. 6.2-1..6.2-7 - Barra isoparametrica a 3 nodi e Jacobiano

📐 · pag. PDF 184 · bar

Funzioni di forma [-xi(1 - xi)/2, xi(1 + xi)/2, 1 - xi^2], geometria x = N x_i e Jacobiano J = dx/dxi = (x2 - x1)/2 + xi (x1 + x2 - 2 x3): costante e pari a L/2 solo con il nodo 3 al centro.

**Modello**: Barra L3 di feagent (add_bar con 3 nodi: estremi, poi nodo medio), x1 = 1, x2 = 4, nodo 3 centrale (x3 = 2,5) o spostato (x3 = 2), A = 2, E = 10.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| N1 x3=2.5 xi=-0.6 | esatta | 0.48 | 0.48 | 0.00 % | ✅ |
| Jacobiano, x3 = 2.5, xi = -0.6 [L] | esatta | 1.5 | 1.5 | 0.00 % | ✅ |
| N1 x3=2.5 xi=0.3 | esatta | -0.105 | -0.105 | 0.00 % | ✅ |
| Jacobiano, x3 = 2.5, xi = 0.3 [L] | esatta | 1.5 | 1.5 | 0.00 % | ✅ |
| rigidezza della barra a 3 nodi [F/L] | esatta | 0 | 1.4211e-14 | 0.00 % | ✅ |
| N1 x3=2.0 xi=-0.6 | esatta | 0.48 | 0.48 | 0.00 % | ✅ |
| Jacobiano, x3 = 2.0, xi = -0.6 [L] | esatta | 0.9 | 0.9 | 0.00 % | ✅ |
| N1 x3=2.0 xi=0.3 | esatta | -0.105 | -0.105 | 0.00 % | ✅ |
| Jacobiano, x3 = 2.0, xi = 0.3 [L] | esatta | 1.8 | 1.8 | 0.00 % | ✅ |

**Note**: Con nodo centrale si verifica anche la rigidezza AE/(3L) [[7, 1, -8], [1, 7, -8], [-8, -8, 16]].

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-3-q4-isoparametric"></a>

### Sez. 6.3-6.5, Eq. 6.3-1..6.3-21, Fig. 6.5-1, 6.5-2 - Quadrilatero bilineare isoparametrico: Jacobiano, [B], [k], subroutine SHAPE e QUAD4

📐 · pag. PDF 186 · plane

Funzioni di forma N_i = (1 +- xi)(1 +- eta)/4, Jacobiano [J] = [dN/dxi] [x y], [B] dalle derivate cartesiane [J]^-1 {dN/dxi, dN/deta}, rigidezza [k] = sum W_i W_j [B]^T [E] [B] t J e carichi di volume int [N]^T {F} t J.

**Modello**: PlaneQ4 di feagent su un quadrilatero generico (E = 2, nu = 0,3, t = 0,7) con forza di volume; riferimento: le subroutine SHAPE e QUAD4 del libro riscritte in numpy (Gauss 2x2).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| det J nel punto (0,3; -0,4) [L2] | esatta | 0.82775 | 0.82775 | 0.00 % | ✅ |
| [k] contro QUAD4 [F/L] | esatta | 0 | 2.2204e-16 | 0.00 % | ✅ |
| forze di volume contro QUAD4 [F] | esatta | 0 | 2.2204e-16 | 0.00 % | ✅ |

**Note**: Confronto di J in un punto di prova, di [k] e del vettore di forza di volume.

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-4-gauss-quadrature"></a>

### Sez. 6.4 (Example), Tabella 6.4-1, Eq. 6.4-3..6.4-8 - Quadratura di Gauss: esempio sul polinomio cubico, regole 2D e 3D

📐 · pag. PDF 190 · quadrature

Per phi = a1 + a2 xi + a3 xi^2 + a4 xi^3 l'integrale esatto su [-1, 1] e' 2a1 + 2a3/3: un punto da' 2a1, due punti l'esatto. Nella regola 3x3 i pesi sono 25/81, 40/81 e 64/81.

**Modello**: Regole di Gauss di feagent (feagent.solid._gl, regola 3x3 dei piani feagent.plane._quad_rule).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| regola a 1 punto = 2 a1 | esatta | 2.6 | 2.6 | 0.00 % | ✅ |
| regola a 2 punti = esatto | esatta | 4 | 4 | 0.00 % | ✅ |
| peso 3x3 angolo | esatta | 0.30864 | 0.30864 | 0.00 % | ✅ |
| peso 3x3 lato | esatta | 0.49383 | 0.49383 | 0.00 % | ✅ |
| peso 3x3 centro | esatta | 0.79012 | 0.79012 | 0.00 % | ✅ |

**Note**: Coefficienti di prova a = (1,3; -0,7; 2,1; 0,9).

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-6-quadratic-quadrilaterals"></a>

### Sez. 6.6, Eq. 6.6-1..6.6-8, Tabella 6.6-1 - Funzioni di forma di quadrilateri da 4 a 9 nodi (serendipity e Lagrange)

📐 · pag. PDF 196 · shape_functions

Funzioni serendipity: nodi di lato N5 = (1 - xi^2)(1 - eta)/2 ...; vertici N1 = (1 - xi)(1 - eta)/4 - (N5 + N8)/2 ...; il nono nodo ha N9 = (1 - xi^2)(1 - eta^2) e le funzioni del Q9 sono prodotti di Lagrange quadratici.

**Modello**: PlaneQ8 e PlaneQ9 di feagent; per gli elementi di transizione (5-7 nodi), assenti in feagent, le funzioni della Tabella 6.6-1 sono verificate con numpy.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| Q8 serendipity contro Eq. 6.6-1 | esatta | 0 | 6.9389e-17 | 0.00 % | ✅ |
| Q9: N9 = (1 - xi^2)(1 - eta^2) | esatta | 0.62972 | 0.62972 | 0.00 % | ✅ |
| Q9: N1 prodotto di Lagrange | esatta | -0.046061 | -0.046061 | 0.00 % | ✅ |
| 5 nodi: N_i(x_j) = delta_ij | esatta | 0 | 0 | 0.00 % | ✅ |
| 5 nodi: somma N_i = 1 | esatta | 1 | 1 | 0.00 % | ✅ |
| 6 nodi: N_i(x_j) = delta_ij | esatta | 0 | 0 | 0.00 % | ✅ |
| 6 nodi: somma N_i = 1 | esatta | 1 | 1 | 0.00 % | ✅ |
| 7 nodi: N_i(x_j) = delta_ij | esatta | 0 | 0 | 0.00 % | ✅ |
| 7 nodi: somma N_i = 1 | esatta | 1 | 1 | 0.00 % | ✅ |

**Note**: Elementi di transizione: delta di Kronecker ai nodi e partizione dell'unita'.

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-7-hexahedral-elements"></a>

### Sez. 6.7, Fig. 6.7-1, Eq. 6.7-1..6.7-6 - Elementi solidi isoparametrici esaedrici

📐 · pag. PDF 200 · solid, shape_functions

Funzioni di forma trilineari N_i = (1 +- xi)(1 +- eta)(1 +- zeta)/8 e Jacobiano 3x3; Hex20 e Hex27 sono solo citati.

**Modello**: Hex8 di feagent su un esaedro distorto.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| funzioni trilineari | esatta | 0 | 0 | 0.00 % | ✅ |
| det J [L3] | esatta | 0.36107 | 0.36107 | 0.00 % | ✅ |

**Note**: Hex20 e Hex27 non sono in feagent.

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-8-triangle-isoparametric-quadrature"></a>

### Sez. 6.8, Tabella 6.8-1, Eq. 6.8-1..6.8-8 - Triangoli isoparametrici e regole di integrazione su triangolo

📐 · pag. PDF 202 · quadrature, plane

La regola a 3 punti ha i punti (2/3, 1/6, 1/6) e permutazioni con peso 1/3 dell'area ed e' esatta per i polinomi di secondo grado; per il T6 a lati curvi il Jacobiano varia nell'elemento e l'area si ottiene integrando det J.

**Modello**: feagent.solid.gauss_tri (regole a 1 e 3 punti e regole coniche di ordine superiore) e PlaneT6 a lati curvi.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| regola a 3 punti: peso 1/3 dell'area | esatta | 0.33333 | 0.33333 | 0.00 % | ✅ |
| regola a 3 punti: coordinata 2/3 | esatta | 0.66667 | 0.66667 | 0.00 % | ✅ |
| int r^1 s^1 con gauss_tri(2) | esatta | 0.041667 | 0.041667 | 0.00 % | ✅ |
| int r^2 s^1 con gauss_tri(3) | esatta | 0.016667 | 0.016667 | 0.00 % | ✅ |
| int r^3 s^2 con gauss_tri(4) | esatta | 0.002381 | 0.002381 | 0.00 % | ✅ |
| area del T6 con un lato parabolico [L2] | esatta | 2.4 | 2.4 | 0.00 % | ✅ |

**Note**: Le regole a 4, 6, 7, 12, 13 punti della Tabella 6.8-1 non sono in feagent, che usa regole coniche: si verifica l'esattezza polinomiale. Area del T6 con un lato parabolico confrontata con la formula di Simpson.

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-9-lst-body-force"></a>

### Sez. 6.9, Eq. 6.9-1 - Forza di volume uniforme su triangolo quadratico: carico solo ai nodi di meta' lato

📐 · pag. PDF 205 · plane

Per il triangolo quadratico a lati retti una forza di volume uniforme da' carichi nulli ai vertici e F_x A t/3 a ciascun nodo di meta' lato.

**Modello**: PlaneT6 di feagent a lati retti (t = 0,4), equivalent_body_force con F_x = 5.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| vertice: 0 [F] | esatta | 0 | -2.4286e-16 | 0.00 % | ✅ |
| nodo di meta' lato: F A t/3 [F] | esatta | 0.71333 | 0.71333 | 0.00 % | ✅ |

**Note**: Triangolo generico.

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-10-isoparametric-validity"></a>

### Sez. 6.10, 6.11, Fig. 6.10-1, 6.10-2 - Validita' degli elementi isoparametrici; superparametrico che fallisce il patch test; ordine di quadratura

📐 · pag. PDF 206 · plane, shape_functions

Un elemento isoparametrico rappresenta esattamente gli stati di deformazione costante perche' sum N_i = 1 e sum N_i x_i = x; un elemento superparametrico (geometria di ordine superiore agli spostamenti) non riproduce il campo lineare e fallisce il patch test.

**Modello**: PlaneQ4, PlaneQ8, PlaneQ9 e PlaneT6 distorti di feagent; elemento superparametrico (geometria Q8 a lato curvo, spostamenti Q4) costruito con numpy dalle funzioni di forma di feagent.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| Q4: eps_x = 1 per u = x | esatta | 1 | 1 | 0.00 % | ✅ |
| Q8: eps_x = 1 per u = x | esatta | 1 | 1 | 0.00 % | ✅ |
| Q9: eps_x = 1 per u = x | esatta | 1 | 1 | 0.00 % | ✅ |
| superparametrico: eps_x per u = x (dovrebbe essere 1) | libro | - | 1.0303 | - | ✅ |
| il superparametrico non riproduce eps_x = 1 | esatta | 1 | 1 | 0.00 % | ✅ |

**Note**: Si impone u = x (deformazione eps_x = 1) e si misura la deformazione calcolata in un punto di prova.

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-12-zero-energy-modes"></a>

### Sez. 6.12, Fig. 6.12-1..6.12-3, Eq. 6.12-2..6.12-8 - Modi a energia nulla (hourglass) di Q4 con 1 punto e di Q8/Q9 con 2x2

📐 · pag. PDF 210 · plane

Con integrazione ridotta compaiono modi a energia nulla oltre ai 3 moti rigidi: Q4 a 1 punto 2 modi (u = v = xi eta), Q9 a 2x2 3 modi (fra cui u = 3 xi^2 eta^2 - xi^2 - eta^2 e u = xi(3 eta^2 - 1), v = eta(1 - 3 xi^2)), Q8 a 2x2 1 modo: rango 12 per entrambi. La stabilizzazione aggiunge una rigidezza lungo il vettore hourglass {1, -1, 1, -1}.

**Modello**: Elementi quadrati di feagent con integration='reduced': Q4 a 1 punto (anche con hourglass=True), Q8 e Q9 a 2x2; autovalori della rigidezza.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| autovalori nulli, Q4 1 punto | esatta | 5 | 5 | 0.00 % | ✅ |
| autovalori nulli, Q4 1 punto + hourglass | esatta | 3 | 3 | 0.00 % | ✅ |
| autovalori nulli, Q8 2x2 | esatta | 4 | 4 | 0.00 % | ✅ |
| rango, Q8 2x2 | esatta | 12 | 12 | 0.00 % | ✅ |
| autovalori nulli, Q9 2x2 | esatta | 6 | 6 | 0.00 % | ✅ |
| rango, Q9 2x2 | esatta | 12 | 12 | 0.00 % | ✅ |
| energia del modo Q4: u = v = xi eta | esatta | 0 | 0 | 0.00 % | ✅ |
| energia del modo Q9: u = 3xi2eta2 - xi2 - eta2 | esatta | 0 | 5.8287e-16 | 0.00 % | ✅ |
| energia del modo Q8: u = xi(3eta2-1), v = eta(1-3xi2) | esatta | 0 | 3.9690e-15 | 0.00 % | ✅ |

**Note**: Si contano gli autovalori nulli e si verifica che i campi del libro, imposti ai nodi, abbiano energia nulla.

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-12-near-mechanism-beam"></a>

### Sez. 6.12, Fig. 6.12-4 - Quasi-meccanismi con integrazione ridotta (mensola con carico concentrato, inclusione rigida)

🟡 · pag. PDF 212 · plane

Con integrazione ridotta una maglia senza meccanismi puo' comunque comportarsi male perche' i vincoli sui modi hourglass sono deboli: per la striscia con maglia 2 x 24 e carico concentrato lo spostamento sotto il carico puo' superare di oltre 500 volte P L/(AE).

**Modello**: Striscia 24 x 2 con maglia 2 x 24 di Q4 quadrati di feagent (E = 1, nu = 0,3, t = 1), nodi di sinistra bloccati, forza assiale P = 1 concentrata nel nodo centrale dell'estremo destro: integrazione piena, a 1 punto e a 1 punto con controllo hourglass.

![cook-6.12-near-mechanism-beam](images/books/cook-6.12-near-mechanism-beam.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| u/(PL/AE), integrazione piena [PL/AE] | libro | - | 1.0552 | - | ✅ |
| u/(PL/AE), Q4 a 1 punto [PL/AE] | libro | - | 771.87 | - | ✅ |
| Q4 a 1 punto: u > 500 PL/AE | libro | 1 | 1 | 0.00 % | ✅ |
| u/(PL/AE), Q4 a 1 punto con hourglass (rif. piena) [PL/AE] | altro | 1.0552 | 1.0717 | 1.57 % | ✅ |

**Note**: Il libro non da' dimensioni ne' materiale: si usa una striscia di elementi quadrati. Si riportano i rapporti u/(PL/AE); col Q4 a un punto non stabilizzato il rapporto e' di vari ordini di grandezza (la maglia e' quasi singolare), con il controllo hourglass torna vicino a quello dell'integrazione piena. Il caso dell'inclusione rigida (Fig. 6.12-4b) non ha dati.

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-13-gauss-point-stress-extrapolation"></a>

### Sez. 6.13, Fig. 6.13-1..6.13-3, Eq. 6.13-2..6.13-5 - Punti di superconvergenza ed estrapolazione delle tensioni dai punti di Gauss

📐 · pag. PDF 214 · plane

Le tensioni ai punti di Gauss 2x2 si estrapolano agli angoli con le funzioni bilineari in r = xi sqrt(3): sigma_A = 1,866 s1 - 0,500 s2 + 0,134 s3 - 0,500 s4. Nel Q4 in flessione pura lo scorrimento parassita e' a dente di sega e si annulla al centro, mentre sigma_x ai punti di Gauss e' quella esatta.

**Modello**: PlaneQ4 di feagent: funzioni di forma valutate in r = xi sqrt(3), s = eta sqrt(3); un Q4 rettangolare in flessione pura con spostamenti imposti.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| c1 | libro | 1.866 | 1.866 | 0.00 % | ✅ |
| c2 | libro | -0.5 | -0.5 | 0.00 % | ✅ |
| c3 | libro | 0.134 | 0.13397 | 0.02 % | ✅ |
| c4 | libro | -0.5 | -0.5 | 0.00 % | ✅ |
| scorrimento parassita nullo al centro | esatta | 0 | 0 | 0.00 % | ✅ |
| sigma_x al punto di Gauss = E k y | esatta | 0.0057735 | 0.0057735 | 0.00 % | ✅ |

**Note**: Coefficienti confrontati con quelli stampati (3 decimali).

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-14-cantilever-q4-aspect-distortion"></a>

### Sez. 6.14, Tabella 6.14-1 - Mensola con elementi bilineari: effetto dell'allungamento e della distorsione

✅ · pag. PDF 216 · plane

Rapporti rispetto alla teoria delle travi: un elemento allungato sigma_xB = 0,096, v_A = 0,091; cinque quadrati sigma_xC = 0,727, v_A = 0,682; cinque elementi distorti sigma_xC = 0,301, v_A = 0,494. Gli elementi quadrati sono migliori di quelli allungati e la distorsione irrigidisce.

**Modello**: PlaneQ4 di feagent (Gauss 2x2), mensola 10 x 2, nu = 0,25, E = 1, t = 1; nodi di sinistra bloccati, P = 1 ripartito a meta' sui due nodi di destra: (1) un elemento 10 x 2; (2) 5 quadrati; (3) 5 elementi distorti con nodi inferiori in x = 0, 1, 2, 4, 7, 10 e superiori in x = 0, 2, 4, 5, 6, 10.

![cook-6.14-cantilever-q4-aspect-distortion](images/books/cook-6.14-cantilever-q4-aspect-distortion.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| sigma_x / (Mc/I), 1 elemento | libro | 0.096 | 0.096386 | 0.40 % | ✅ |
| v_A / v_trave, 1 elemento | libro | 0.091 | 0.090508 | 0.54 % | ✅ |
| sigma_x / (Mc/I), 5 quadrati | libro | 0.727 | 0.72727 | 0.04 % | ✅ |
| v_A / v_trave, 5 quadrati | libro | 0.682 | 0.68293 | 0.14 % | ✅ |
| sigma_x / (Mc/I), 5 distorti | libro | 0.301 | 0.30069 | 0.10 % | ✅ |
| v_A / v_trave, 5 distorti | libro | 0.494 | 0.49269 | 0.27 % | ✅ |

**Note**: Coordinate della maglia distorta e posizione di B e C lette dalla figura: B a meta' del lato inferiore dell'elemento unico (x = 5), C a meta' del lato inferiore dell'elemento che va da x = 2 a x = 4 (x = 3). Riferimento della freccia P L^3/(3EI) + P L/(G A) (taglio 2,4 %; il libro dice 3 %, con fattore di taglio 1,2 gli scarti salgono a 0,4-1 %), tensione M c/I nel punto.

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-14-cantilever-q8-q9-distortion"></a>

### Sez. 6.14, Tabella 6.14-2 - Mensola a due elementi quadratici (8 e 9 nodi): regola di Gauss e distorsione

🟡 · pag. PDF 217 · plane

Rapporti sigma_xB (punto di Gauss 2x2 dell'elemento di sinistra) e v_A rispetto alla teoria delle travi senza taglio, per 8 e 9 nodi, regole 2x2 e 3x3, maglia rettangolare, a trapezi e con lato curvo. L'elemento a 8 nodi crolla con la distorsione (sigma_xB 0,05, v_A 0,16-0,43), quello a 9 nodi resta accettabile; la regola 2x2 e' preferibile. Quattro T6 sui trapezi danno v_A = 0,796.

**Modello**: Due PlaneQ8 o PlaneQ9 di feagent (integration='reduced' = 2x2, 'full' = 3x3), mensola 100 x 10, nu = 0,3, E = 1, nodi di sinistra bloccati, P = 1 ripartito 1-8-1 sui nodi di destra. Maglie: rettangoli 50 x 10; trapezi con C in x = 25 e D in x = 75; interfaccia curva con il nodo medio E spostato a sinistra di 5. Nodo centrale del Q9 nella posizione della mappa del Q8. Variante con 4 T6 sulla maglia a trapezi.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| sigma_xB, rettangoli, 8 nodi, 2x2 | libro | 1 | 1 | 0.00 % | ✅ |
| v_A, rettangoli, 8 nodi, 2x2 | libro | 0.968 | 0.96784 | 0.02 % | ✅ |
| sigma_xB, rettangoli, 8 nodi, 3x3 | libro | 1.129 | 1.1293 | 0.02 % | ✅ |
| v_A, rettangoli, 8 nodi, 3x3 | libro | 0.93 | 0.93013 | 0.01 % | ✅ |
| sigma_xB, rettangoli, 9 nodi, 2x2 | libro | 1 | 1 | 0.00 % | ✅ |
| v_A, rettangoli, 9 nodi, 2x2 | libro | 1.006 | 1.0059 | 0.01 % | ✅ |
| sigma_xB, rettangoli, 9 nodi, 3x3 | libro | 1.141 | 1.1407 | 0.03 % | ✅ |
| v_A, rettangoli, 9 nodi, 3x3 | libro | 0.954 | 0.9541 | 0.01 % | ✅ |
| sigma_xB, trapezi, 8 nodi, 2x2 | libro | 0.051 | 0.050826 | 0.34 % | ✅ |
| v_A, trapezi, 8 nodi, 2x2 | libro | 0.362 | 0.36235 | 0.10 % | ✅ |
| sigma_xB, trapezi, 8 nodi, 3x3 | libro | 0.048 | 0.047552 | 0.93 % | ✅ |
| v_A, trapezi, 8 nodi, 3x3 | libro | 0.161 | 0.16115 | 0.09 % | ✅ |
| sigma_xB, trapezi, 9 nodi, 2x2 (libro 1.125) | libro | - | 1.0601 | - | ✅ |
| v_A, trapezi, 9 nodi, 2x2 (libro 1.109) | libro | - | 1.0745 | - | ✅ |
| sigma_xB, trapezi, 9 nodi, 3x3 | libro | 0.687 | 0.68674 | 0.04 % | ✅ |
| v_A, trapezi, 9 nodi, 3x3 | libro | 0.791 | 0.79126 | 0.03 % | ✅ |
| sigma_xB, lato curvo, 8 nodi, 2x2 | libro | -0.048 | -0.047318 | 1.42 % | ✅ |
| v_A, lato curvo, 8 nodi, 2x2 | libro | 0.43 | 0.42969 | 0.07 % | ✅ |
| sigma_xB, lato curvo, 8 nodi, 3x3 | libro | 0.05 | 0.049875 | 0.25 % | ✅ |
| v_A, lato curvo, 8 nodi, 3x3 | libro | 0.221 | 0.22107 | 0.03 % | ✅ |
| sigma_xB, lato curvo, 9 nodi, 2x2 (libro 0.958) | libro | - | 0.96307 | - | ✅ |
| v_A, lato curvo, 9 nodi, 2x2 (libro 0.955) | libro | - | 0.95513 | - | ✅ |
| sigma_xB, lato curvo, 9 nodi, 3x3 | libro | 0.705 | 0.70519 | 0.03 % | ✅ |
| v_A, lato curvo, 9 nodi, 3x3 | libro | 0.737 | 0.73699 | 0.00 % | ✅ |
| v_A, 4 T6 sulla maglia a trapezi | libro | 0.796 | 0.79608 | 0.01 % | ✅ |

**Note**: 22 valori su 24 coincidono con la tabella alla terza cifra. Il Q9 2x2 sulle maglie distorte dipende dalla posizione del nodo centrale, che il libro non da': con il nodo nella mappa del Q8 feagent da' 1,060/1,074 (trapezi, libro 1,125/1,109) e 0,963/0,955 (lato curvo, libro 0,958/0,955); spostando il nodo centrale di qualche unita' si riproducono i valori del libro (il Q9 2x2 ha tre modi spuri ed e' sensibile a questo dato). Questi valori sono solo riportati.

*Codice*: `validation/books/cook/ch06.py`

<a id="cook-6-14-badly-shaped-elements"></a>

### Sez. 6.14, Fig. 6.14-1 - Elementi di forma degenere e Jacobiano negativo ai punti di Gauss

📐 · pag. PDF 218 · plane

Con un angolo di 180° il Jacobiano si annulla al nodo 3 (positivo altrove); spostando un vertice al centro o un nodo di lato su un vertice det J cambia segno nell'elemento, anche ai punti di Gauss: l'elemento e' inutilizzabile e va scartato con controlli geometrici.

**Modello**: PlaneQ4 e PlaneQ8 di feagent con le forme della Fig. 6.14-1: angolo di 180° al nodo 3, nodo 1 al centro, nodo 7 sul vertice 3 (nodi di lato fuori posto).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| angolo di 180°: det J al nodo 3 | esatta | 0 | 0 | 0.00 % | ✅ |
| angolo di 180°: det J > 0 al centro | esatta | 1 | 1 | 0.00 % | ✅ |
| Q8 con nodo 1 al centro: rifiutato (det J di segno variabile) | esatta | 1 | 1 | 0.00 % | ✅ |
| Q8 con nodo 7 sul vertice 3: rifiutato (det J di segno variabile) | esatta | 1 | 1 | 0.00 % | ✅ |

**Note**: feagent rifiuta l'elemento quando det J cambia segno ai punti di Gauss (ValueError): si conta il rifiuto come 1.

*Codice*: `validation/books/cook/ch06.py`

## Capitolo 7

<a id="cook-7-3-stress-strain-material-transformation"></a>

### Sez. 7.2-7.3, Eq. 7.2-1..7.3-11 - Trasformazione di vettori, deformazioni, tensioni e matrice dei materiali

📐 · pag. PDF 229 · plane

Le componenti di tensione e deformazione ruotano con le matrici [T_sigma], [T_eps] (nel piano il cerchio di Mohr, Eq. 7.3-11) e la matrice dei materiali con [E] = [T_eps]^T [E'] [T_eps].

**Modello**: Funzioni di feagent: tensioni principali (feagent.solid.principal_stresses), matrice costitutiva piana (plane_D) e tensore di conducibilita' ortotropo ruotato (FieldMaterial con angle); matrici di trasformazione del libro scritte con numpy.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| sigma1 dal cerchio di Mohr [F/L2] | esatta | 13.434 | 13.434 | 0.00 % | ✅ |
| sigma2 dal cerchio di Mohr [F/L2] | esatta | -5.434 | -5.434 | 0.00 % | ✅ |
| [E] isotropa invariante per rotazione | esatta | 0 | 4.4409e-16 | 0.00 % | ✅ |
| sigma^T eps invariante | esatta | 3.5984e-06 | 3.5984e-06 | 0.00 % | ✅ |
| tensore ortotropo ruotato | esatta | 0 | 0 | 0.00 % | ✅ |

**Note**: Verifiche: tensioni principali di feagent contro il cerchio di Mohr; invarianza della [E] isotropa per rotazione; conducibilita' ortotropa ruotata contro R k' R^T; energia sigma^T eps invariante.

*Codice*: `validation/books/cook/ch07.py`

<a id="cook-7-5-truss-frame-transformation"></a>

### Sez. 7.4-7.5, Fig. 7.5-1, 7.5-2, Eq. 7.5-1..7.5-7 - Trasformazione delle matrici di rigidezza di asta piana, asta spaziale e telaio piano

📐 · pag. PDF 234 · truss, beam

Telaio piano in coordinate globali (Fig. 7.5-2b): F = Z c^2 + K s^2, G = (Z - K) c s, H = -M s, P = Z s^2 + K c^2, Q = M c con Z = AE/L, K = 12EI/L^3, M = 6EI/L^2, A = 4EI/L, B = 2EI/L; asta spaziale AE/L [l m n]^T [l m n].

**Modello**: Trave di feagent inclinata di beta = 35° nel piano X-Y (A = 2, I = 0,5, E = 3, L = 4) e biella spaziale di coseni direttori (l, m, n).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| telaio piano: scarto massimo dalla Fig. 7.5-2b | esatta | 0 | 6.6613e-16 | 0.00 % | ✅ |
| asta spaziale | esatta | 0 | 0 | 0.00 % | ✅ |

**Note**: Confronto dell'intera 6x6 globale del telaio sui gdl (u, v, theta) dei due nodi.

*Codice*: `validation/books/cook/ch07.py`

<a id="cook-7-6-inclined-support"></a>

### Sez. 7.6, Fig. 7.6-1, Eq. 7.6-1..7.6-8 - Appoggio inclinato con trasformazione dei gdl del nodo

📐 · pag. PDF 236 · truss

Il nodo 3 scorre solo lungo la direzione r inclinata di beta: si trasformano i suoi gdl con [T] = [[c, s], [-s, c]], [K'] = [T]^T [K] [T] (Eq. 7.6-6), e si impone lo spostamento normale nullo.

**Modello**: Reticolare a tre aste di feagent (1-2, 2-3, 1-3; nodi 1 = (0,0), 2 = (4,3), 3 = (8,0), E A = 1000) con il nodo 3 su un carrello inclinato di beta = 30° (set_support_axes) e carico (5, -10) al nodo 2.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| u2 [L] | esatta | 0.031319 | 0.031319 | 0.00 % | ✅ |
| v2 [L] | esatta | -0.085161 | -0.085161 | 0.00 % | ✅ |
| u3 = r3 cos beta [L] | esatta | 0.041579 | 0.041579 | 0.00 % | ✅ |
| v3 = r3 sin beta [L] | esatta | 0.024006 | 0.024006 | 0.00 % | ✅ |
| componente della reazione lungo il piano [F] | esatta | 0 | 0 | 0.00 % | ✅ |

**Note**: Riferimento costruito con numpy applicando la trasformazione del libro alla [K] assemblata da feagent; verifica anche che la reazione sia normale al piano di scorrimento.

*Codice*: `validation/books/cook/ch07.py`

<a id="cook-7-7-dissimilar-elements"></a>

### Sez. 7.7, Fig. 7.7-1, Eq. 7.7-1..7.7-5 - Collegamento di un elemento telaio o di un'asta al lato di un quadrilatero

📐 · pag. PDF 238 · plane, beam

Il nodo 5 della trave non e' un nodo del quadrilatero: i suoi spostamenti si interpolano linearmente da quelli dei nodi 2 e 3 e la rotazione e' quella del lato, (u2 - u3)/L23 per un lato verticale; la matrice [T] 6x7 lega i gdl della trave ai gdl indipendenti.

**Modello**: Un PlaneQ4 di feagent e una trave piana il cui nodo 5 sta sul lato 2-3 del quadrilatero; la rigidezza della trave trasformata [T]^T [k] [T] (Eq. 7.7-4) e' inserita con add_superelement sui gdl (u, v) dei nodi 2 e 3 e sui gdl del nodo libero della trave.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| energia nulla per traslazione rigida | esatta | 0 | 0 | 0.00 % | ✅ |
| energia nulla per rotazione rigida | esatta | 0 | -5.5511e-16 | 0.00 % | ✅ |
| somma delle reazioni verticali = carico [F] | esatta | 1 | 1 | 0.00 % | ✅ |

**Note**: feagent non ha vincoli lineari generici: la trasformazione si applica alla matrice della trave di feagent e il risultato entra come superelemento. Si verificano l'energia nulla per i moti rigidi e l'equilibrio globale.

*Codice*: `validation/books/cook/ch07.py`

<a id="cook-7-8-rigid-link-stiffened-plate"></a>

### Sez. 7.8, Fig. 7.8-1, Eq. 7.8-1..7.8-4 - Trave eccentrica collegata alla piastra con link rigidi; errore di accoppiamento

🟡 · pag. PDF 240 · beam

Con link rigidi l'assiale lineare dell'irrigidimento non e' compatibile con la rotazione cubica della piastra: fra i nodi le due travi non lavorano insieme e la freccia e' sovrastimata del 69 %, 17 % e 4,3 % con 1, 2, 4 elementi rispetto alla trave composta.

**Modello**: Mensola modellata come due travi di Hermite di feagent (piastra e irrigidimento, A = 1, I = 1e-3, L = 1) a distanza e, collegate ai nodi con add_rigid_link; carico P in punta; n = 1, 2, 4 elementi.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| errore della freccia, n = 1 (taratura di e) [%] | libro | 69 | 69 | 0.00 % | ✅ |
| errore della freccia, n = 2 [%] | libro | 17 | 17.25 | 1.47 % | ✅ |
| errore della freccia, n = 4 [%] | libro | 4.3 | 4.3125 | 0.29 % | ✅ |

**Note**: Il caso del rif. [7.1] non e' descritto: l'eccentricita' e si tara perche' con un elemento l'errore sia 69 % (e = 0,1051); con n = 2 e 4 feagent da' allora 17,25 % e 4,31 % (libro 17 e 4,3): l'errore scala come 1/n^2 indipendentemente dalle proporzioni. La correzione di Miller (gdl assiale a meta' elemento) non e' in feagent.

*Codice*: `validation/books/cook/ch07.py`

<a id="cook-7-8-rigid-triangle"></a>

### Sez. 7.8, Fig. 7.8-2, Eq. 7.8-5 - Triangolo piano reso rigido con trasformazione a 3 gdl

📐 · pag. PDF 241 · plane

Un triangolo rigido ha 3 gdl: con theta = (u1 - u2)/b, v2 = v1 e lo spostamento del terzo nodo dato dalla rotazione rigida (Eq. 7.8-5). Qualunque combinazione dei 3 gdl e' un moto rigido: [k][T] = 0.

**Modello**: PlaneT3 di feagent con nodi 1 = (0, 0), 2 = (0, b), 3 = (a, b/2) (a = 1,5, b = 2); trasformazione del libro dai gdl indipendenti u1, v1, u2.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| forze nodali nulle per i moti del triangolo rigido | esatta | 0 | 5.5511e-17 | 0.00 % | ✅ |
| 3 gdl indipendenti | esatta | 3 | 3 | 0.00 % | ✅ |

**Note**: Geometria scelta col lato 1-2 verticale come nella figura.

*Codice*: `validation/books/cook/ch07.py`

## Capitolo 8

<a id="cook-8-1-static-condensation"></a>

### Sez. 8.1-8.2, Eq. 8.1-1..8.1-5, Fig. 8.1-1, 8.2-1, 8.2-2 - Condensazione statica, gdl interni e senza nodo, recupero

📐 · pag. PDF 248 · bar, beam

[k]_c = k_rr - k_rc k_cc^-1 k_cr e {r}_c = r_r - k_rc k_cc^-1 r_c: per le tre barre si ottiene 2 u4 = 8; il rilascio di cerniera in un telaio e' una condensazione della rotazione dell'estremo: k = 3EI/L^3 [[1, L, -1], ...].

**Modello**: (a) Problema 8.1: le tre bielle in serie della Fig. 2.11-1 (AE/L = 6, P = 24 al nodo 2) condensate sul gdl u4 con feagent.reduction.guyan; (b) trave di feagent con rilascio rz all'estremo j (Fig. 8.1-2, EI = 2, L = 3), rigidezza letta come reazioni.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| rigidezza condensata su u4 [F/L] | libro | 2 | 2 | 0.00 % | ✅ |
| carico condensato su u4 [F] | libro | 8 | 8 | 0.00 % | ✅ |
| u2 recuperato [L] | libro | 4 | 4 | 0.00 % | ✅ |
| trave con cerniera in j: k11 = 3EI/L^3 [F/L] | esatta | 0.22222 | 0.22222 | 0.00 % | ✅ |
| trave con cerniera in j: k21 = 3EI/L^2 [F] | esatta | 0.66667 | 0.66667 | 0.00 % | ✅ |

**Note**: Recupero: gli spostamenti interni ricostruiti dalla riduzione coincidono con quelli della soluzione completa.

*Codice*: `validation/books/cook/ch08.py`

<a id="cook-8-3-bilinear-parasitic-shear"></a>

### Sez. 8.3, Fig. 8.3-1, Eq. 8.3-1..8.3-5 - Taglio parassita del rettangolo bilineare in flessione pura

📐 · pag. PDF 252 · plane

Con le stesse rotazioni dei lati, il momento M1 richiesto dal bilineare supera quello esatto M2 per lo scorrimento parassita: M1/M2 = [1/(1 - nu) + (a/b)^2/2]/(1 + nu). L'elemento QM6 elimina l'errore (M1/M2 = 1).

**Modello**: PlaneQ4 rettangolare 2a x 2b di feagent (E = 1, t = 1) con spostamenti nodali u = u_bar xi eta, v = 0; QM6 (incompatible=True) per confronto; a/b = 0,5, 1, 4 e nu = 0,3.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| M1/M2, Q4, a/b = 0.5 | libro | 1.1951 | 1.1951 | 0.00 % | ✅ |
| M1/M2, QM6, a/b = 0.5 | esatta | 1 | 1 | 0.00 % | ✅ |
| M1/M2, Q4, a/b = 1.0 | libro | 1.4835 | 1.4835 | 0.00 % | ✅ |
| M1/M2, QM6, a/b = 1.0 | esatta | 1 | 1 | 0.00 % | ✅ |
| M1/M2, Q4, a/b = 4.0 | libro | 7.2527 | 7.2527 | 0.00 % | ✅ |
| M1/M2, QM6, a/b = 4.0 | esatta | 1 | 1 | 0.00 % | ✅ |

**Note**: M1/M2 = U_Q4/U_esatta a parita' di rotazione; U esatta della flessione pura (2/3) E u_bar^2 b t / a.

*Codice*: `validation/books/cook/ch08.py`

<a id="cook-8-3-cooks-membrane-qm6-q4"></a>

### Sez. 8.3, Fig. 8.3-3 - Membrana di Cook: QM6 contro bilineare

🟡 · pag. PDF 256 · plane

Freccia v_C a meta' del lato caricato, tensione massima in A (meta' del lato inferiore) e minima in B (meta' del lato superiore) in rapporto alla migliore soluzione nota: QM6 0,884/0,967 per v_C, bilineare 0,498/0,769. Il QM6 e' molto piu' accurato del bilineare.

**Modello**: Membrana trapezia (0,0), (48,44), (48,60), (0,44), E = 1, nu = 1/3, t = 1, lato sinistro incastrato, taglio uniforme di risultante 1 sul lato destro; maglie N x N (N = 2, 4) di PlaneQ4 bilineari e QM6; riferimento Q9 32 x 32.

![cook-8.3-cooks-membrane-qm6-q4](images/books/cook-8.3-cooks-membrane-qm6-q4.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| v_C convergente, Q9 32x32 (Bergan-Felippa 23,96) | altro | 23.96 | 23.961 | 0.00 % | ✅ |
| v_C, Q4 2x2 (benchmark pubblicato 11,85) | altro | 11.85 | 11.845 | 0.04 % | ✅ |
| v_C, QM6 2x2 (benchmark pubblicato 21,05) | altro | 21.05 | 21.05 | 0.00 % | ✅ |
| riferimento v_C implicito nel libro (da QM6 N = 2) | libro | - | 23.813 | - | ✅ |
| sigma_A / 0,2362, QM6, N = 2 (libro 0.84) | libro | - | 0.72468 | - | ✅ |
| sigma_B / -0,2023, QM6, N = 2 (libro 0.788) | libro | - | 0.92765 | - | ✅ |
| v_C / v_rif, QM6, N = 4 | libro | 0.967 | 0.96657 | 0.04 % | ✅ |
| sigma_A / 0,2362, QM6, N = 4 (libro 0.978) | libro | - | 0.92792 | - | ✅ |
| sigma_B / -0,2023, QM6, N = 4 (libro 0.926) | libro | - | 0.9267 | - | ✅ |
| v_C / v_rif, Q4, N = 2 | libro | 0.498 | 0.49743 | 0.11 % | ✅ |
| sigma_A / 0,2362, Q4, N = 2 (libro 0.558) | libro | - | 0.48716 | - | ✅ |
| sigma_B / -0,2023, Q4, N = 2 (libro 0.457) | libro | - | 0.39233 | - | ✅ |
| v_C / v_rif, Q4, N = 4 | libro | 0.769 | 0.76847 | 0.07 % | ✅ |
| sigma_A / 0,2362, Q4, N = 4 (libro 0.83) | libro | - | 0.77699 | - | ✅ |
| sigma_B / -0,2023, Q4, N = 4 (libro 0.753) | libro | - | 0.7086 | - | ✅ |
| sigma_max in A, Q9 32x32 | altro | 0.2362 | 0.237 | 0.34 % | ✅ |
| sigma_min in B, Q9 32x32 | altro | -0.2023 | -0.20358 | 0.63 % | ✅ |

**Note**: Il punto C e' a meta' del lato destro (48, 52): con questo punto Q4 2x2 e QM6 2x2 danno 11,85 e 21,05 come nei benchmark pubblicati e il Q9 32x32 converge a 23,96 (Bergan e Felippa). I quattro rapporti v_C del libro implicano pero' un riferimento di 23,81 (la 'best known' dell'epoca, rif. [8.3]): si ricava dal QM6 N = 2 e si verificano gli altri tre rapporti. Le tensioni in A e B del libro dipendono dal modo di calcolo (estrapolazione, media, uso dei modi incompatibili) che non e' descritto: i valori di feagent (media nodale, tensioni principali) sono solo riportati.

*Codice*: `validation/books/cook/ch08.py`

<a id="cook-8-4-drilling-dof-triangle"></a>

### Sez. 8.4, Fig. 8.4-1, Eq. 8.4-1..8.4-9 - Triangolo piano con rotazioni 'drilling' ottenuto dal LST

📐 · pag. PDF 256 · plane

Ai vertici si tengono u, v e la rotazione omega; lo spostamento normale dei nodi di meta' lato e' delta = L (omega_j - omega_i)/8 (lato parabolico). Il triangolo a 9 gdl ha un modo spurio (omega uguali, u = v = 0) da stabilizzare con una piccola rigidezza (gamma = 1e-6), e rappresenta esattamente i moti rigidi e gli stati di deformazione costante.

**Modello**: PlaneT6 di feagent (E = 1, nu = 0,25, t = 1) su un triangolo generico; trasformazione 12x9 del libro scritta con numpy, [k] = [T]^T [k_LST] [T].

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| autovalori nulli di [k] 9x9 (3 rigidi + 1 spurio) | esatta | 4 | 4 | 0.00 % | ✅ |
| rotazione rigida con omega = theta | esatta | 0 | 7.2506e-22 | 0.00 % | ✅ |
| modo spurio (omega uguali) | esatta | 0 | 0 | 0.00 % | ✅ |
| stato a deformazione costante | esatta | 0.001 | 1.0000e-03 | 0.00 % | ✅ |

**Note**: Verifiche: 4 autovalori nulli (3 moti rigidi + modo spurio), rotazione rigida con omega = theta a energia nulla, deformazione costante riprodotta.

*Codice*: `validation/books/cook/ch08.py`

<a id="cook-8-5-hybrid-stress-rectangle"></a>

### Sez. 8.5 (Example), Fig. 8.5-1, Eq. 8.5-1..8.5-16 - Elemento ibrido a tensioni assunte (rettangolo 5-beta)

📐 · pag. PDF 259 · plane

Campo di tensione sigma_x = b1 + b4 y, sigma_y = b2 + b5 x, tau = b3, spostamenti bilineari sul contorno: [k] = [G]^T [H]^-1 [G] con [H] = int P^T E^-1 P dV, [G] = int P^T B dV. Per il rettangolo coincide con il QM6.

**Modello**: Rettangolo 2a x 2b (a = 1,5, b = 0,6, E = 1, nu = 0,3, t = 1): [H] e [G] costruiti con numpy dal campo a 5 parametri e dalla [B] del PlaneQ4 di feagent; confronto con il QM6 di feagent.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| ibrido 5-beta contro QM6 (rettangolo) | esatta | 0 | 5.9459e-16 | 0.00 % | ✅ |

**Note**: Coordinate x, y misurate dal centro del rettangolo; Gauss 2x2 esatto.

*Codice*: `validation/books/cook/ch08.py`

<a id="cook-8-6-hybrid-drilling-triangle-membrane"></a>

### Sez. 8.6, Fig. 8.6-1, 8.6-2 - Membrana di Cook con triangoli a rotazioni drilling (Eq. 8.4-5 e macroelemento ibrido)

🟡 · pag. PDF 264 · plane, shell

Il libro risolve la membrana di Cook con il triangolo drilling di Eq. 8.4-5 (v_C 0,840/0,950 per N = 2/4) e con un macroelemento ibrido di tre sottotriangoli (0,900/0,969): le rotazioni drilling migliorano molto il triangolo a deformazione costante.

**Modello**: Membrana di Cook (come cook-8.3) con triangoli guscio di feagent nel piano X-Y (add_shell_tri): formulation='thin' (membrana GT9 con drilling, Long e Xu) e 'membrane' (triangolo senza drilling); ogni quadrilatero N x N diviso lungo una diagonale; gdl fuori piano bloccati.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| v_C/23,96, GT9 con drilling, N = 2 (libro 0,840 / 0,900) | libro | - | 0.70889 | - | ✅ |
| v_C/23,96, GT9 con drilling, N = 4 (libro 0,950 / 0,969) | libro | - | 0.89368 | - | ✅ |
| v_C/23,96, senza drilling, N = 2 (libro 0,840 / 0,900) | libro | - | 0.28141 | - | ✅ |
| v_C/23,96, senza drilling, N = 4 (libro 0,950 / 0,969) | libro | - | 0.46962 | - | ✅ |
| v_C, GT9 con drilling, N = 32 | altro | 23.96 | 23.891 | 0.29 % | ✅ |

**Note**: Gli elementi del libro non sono in feagent: il triangolo con drilling di feagent e' il GT9, diverso. Si riportano i rapporti v_C/23,96 con N = 2, 4 per GT9 e per il triangolo senza drilling, e si verifica che il GT9 converga (N = 32) al riferimento; lo schema di suddivisione del libro non e' noto.

*Codice*: `validation/books/cook/ch08.py`

<a id="cook-8-7-elastic-kernel-beam"></a>

### Sez. 8.7 (Example), Eq. 8.7-1..8.7-9 - Nucleo elastico: ricostruzione della [k] completa della trave

📐 · pag. PDF 264 · beam

Dalla rigidezza k_EE dei soli gdl 'elastici' di un elemento vincolato in modo isostatico e dalla matrice dei moti rigidi si ricostruisce la [k] 4x4 completa (Eq. 4.2-5).

**Modello**: Mensola di feagent (EI = 5, L = 2) incastrata a sinistra: flessibilita' all'estremo libero da due soluzioni con carichi unitari, k_EE = flessibilita'^-1 e matrice cinematica [T] = [[1, -L], [0, 1]] dei moti rigidi.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| [k] ricostruita dal nucleo elastico | esatta | 0 | 1.7764e-15 | 0.00 % | ✅ |

**Note**: [k] = [[T^T k_EE T, -T^T k_EE], [-k_EE T, k_EE]] sui gdl (v1, th1, v2, th2).

*Codice*: `validation/books/cook/ch08.py`

<a id="cook-8-9-quarter-point-crack"></a>

### Sez. 8.9, Fig. 8.9-1..8.9-4, Eq. 8.9-1..8.9-8 - Elementi a quarto di lato per la singolarita' all'apice della fessura

🟡 · pag. PDF 267 · bar, plane, fracture

Spostando il nodo medio a un quarto del lato la mappa isoparametrica produce deformazioni proporzionali a r^-1/2, la singolarita' dell'apice di una fessura. Per la piastra con fessura centrale il libro riporta K_I = sigma sqrt(pi a) [1 - 0,5 (a/c) + 0,326 (a/c)^2]/sqrt(1 - a/c) e la formula per K_I dagli spostamenti dei nodi sui lembi.

**Modello**: (a) Barra L3 di feagent con il nodo medio a L/4 dall'estremo 1; (b) quarto di piastra con fessura centrale (a/c = 0,5, altezza 2H = 6c, sigma = 1, E = 1, nu = 0,3, tensione piana) di Q8 con maglia graduata e nodi a un quarto sui lati che escono dall'apice; K_I dagli spostamenti dei lembi (Eq. 8.9-8) e dalla variazione dell'energia di deformazione con la lunghezza di fessura.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| eps * sqrt(x) = 1/2 (u = sqrt(x)), xi = -0.5 | esatta | 0.5 | 0.5 | 0.00 % | ✅ |
| eps * sqrt(x) = 1/2 (u = sqrt(x)), xi = 0.4 | esatta | 0.5 | 0.5 | 0.00 % | ✅ |
| K_I/K_tab, nodi a un quarto, spostamenti dei lembi | libro | - | 0.95635 | - | ✅ |
| K_I/K_tab, nodi a meta' lato, spostamenti dei lembi | libro | - | 1.4905 | - | ✅ |
| K_I = sqrt(E dU/dA) contro la formula tabulata | libro | 1.4738 | 1.4865 | 0.86 % | ✅ |

**Note**: feagent non ha il post-processo di K_I: lo si calcola dagli spostamenti (Eq. 8.9-8, K_I = E/4 sqrt(2 pi/l) (4 v_B - v_C)) e, per controllo, da G = dU/dA (K_I = sqrt(E G)). Con quadrilateri non collassati il metodo degli spostamenti resta circa 4-5 % sotto la formula tabulata (si riporta), mentre il metodo energetico la ritrova entro l'1 %. Senza nodi a un quarto lo stesso calcolo dagli spostamenti sbaglia di circa il 50 %.

*Codice*: `validation/books/cook/ch08.py`

<a id="cook-8-10-winkler-foundation"></a>

### Sez. 8.10, Fig. 8.10-1, Eq. 8.10-1, 8.10-2 - Matrice di rigidezza di una fondazione alla Winkler

📐 · pag. PDF 270 · beam, foundation

La fondazione aggiunge [k_f] = int beta [N]^T [N] dA (consistente); con le funzioni di Hermite vale beta L/420 [[156, 22L, 54, -13L], ...]; la versione concentrata assegna beta A/n a ciascun nodo.

**Modello**: feagent.foundation: beam_foundation_stiffness su una trave (L = 2, beta = 3) e add_beam_foundation su una trave lunga (EI = 1, k = 4, L = 40, 80 elementi) con carico concentrato in mezzeria.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| fondazione consistente di Hermite | esatta | 0 | 4.4409e-16 | 0.00 % | ✅ |
| traslazione rigida: risultante beta L (concentrata beta L/2 per nodo) [F/L] | esatta | 6 | 6 | 0.00 % | ✅ |
| trave lunga su Winkler: w = P lambda/(2k) [L] | esatta | 0.125 | 0.12497 | 0.03 % | ✅ |

**Note**: Controllo aggiuntivo: freccia sotto il carico di una trave lunga su suolo elastico, P lambda/(2k) con lambda = (k/4EI)^(1/4) (Hetenyi).

*Codice*: `validation/books/cook/ch08.py`

<a id="cook-8-11-infinite-elements"></a>

### Sez. 8.11, Fig. 8.11-1..8.11-3, Eq. 8.11-1..8.11-8 - Elementi infiniti mappati 1D e 2D

⛔ · pag. PDF 272 · infinite_elements

Elementi infiniti con mappatura M1 = -2 xi/(1 - xi), M2 = (1 + xi)/(1 - xi) e polo a distanza a dal nodo 1: il campo decresce come a/r e (a/r)^2.

**Modello**: Nessuno.

**Note**: feagent non ha elementi infiniti.

*Codice*: `validation/books/cook/ch08.py`

<a id="cook-8-14-substructuring-symmetry"></a>

### Sez. 8.12-8.16, Fig. 8.14-1, 8.15-1, 8.16-1 - Differenze finite, rianalisi, sottostrutture, simmetria e simmetria ciclica

📐 · pag. PDF 276 · plane

Il libro discute sottostrutture (condensazione dei gdl interni di una parte), simmetria e antisimmetria (condizioni sull'asse), simmetria ciclica e rianalisi; e' qualitativo.

**Modello**: Lastra 4 x 2 di Q8 di feagent appoggiata agli spigoli inferiori e caricata in mezzeria: modello intero, mezzo modello con condizioni di simmetria (u = 0 sull'asse) e modello con la meta' destra condensata come superelemento di Guyan (feagent.reduction.guyan + add_superelement).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| freccia in mezzeria, mezzo modello simmetrico [L] | esatta | -0.605 | -0.605 | 0.00 % | ✅ |
| freccia in mezzeria, meta' destra condensata (Guyan) [L] | esatta | -0.605 | -0.605 | 0.00 % | ✅ |

**Note**: Si verifica che mezzo modello simmetrico e sottostruttura condensata diano la stessa freccia in mezzeria del modello intero.

*Codice*: `validation/books/cook/ch08.py`

## Capitolo 9

<a id="cook-9-1-bars-constraint-transformation"></a>

### Sez. 9.1 (Example), Fig. 9.1-2, Eq. 9.1-7..9.1-9 - Tre barre con vincolo u2 = u3 imposto per trasformazione

✅ · pag. PDF 293 · bar, constraints

Con la trasformazione {u1, u2, u3} = [T]{u1, u2} il sistema diventa [[2k, -k], [-k, k]]{u1, u2} = {P, 2P}: u1 = 3P/k, u2 = u3 = 5P/k.

**Modello**: Tre bielle di feagent in serie (k = AE/L = 10), nodo 0 incastrato, P = 1 ai nodi 1, 2, 3; vincolo u2 = u3 con add_equal_dof (eliminazione master-slave).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| u1 [L] | libro | 0.3 | 0.3 | 0.00 % | ✅ |
| u2 [L] | libro | 0.5 | 0.5 | 0.00 % | ✅ |
| u3 [L] | libro | 0.5 | 0.5 | 0.00 % | ✅ |

**Note**: Valori di prova k = 10, P = 1.

*Codice*: `validation/books/cook/ch09.py`

<a id="cook-9-1-rigid-bar-two-springs"></a>

### Sez. 9.1, Fig. 9.1-3, Eq. 9.1-10 - Barra rigida su due molle con vincolo v1 = v2

✅ · pag. PDF 294 · spring, constraints

Il vincolo v1 = v2 rende la barra rigida in traslazione: v1 = v2 = P/(2k) e ciascuna molla porta P/2; le sole forze elastiche non equilibrano i momenti: la differenza la porta il vincolo.

**Modello**: Due nodi a distanza 2 su molle a terra k = 50 (add_elastic_support), forza P = 4 al nodo 1, vincolo v1 = v2 (add_equal_dof).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| v1 [L] | esatta | -0.04 | -0.04 | 0.00 % | ✅ |
| v2 [L] | esatta | -0.04 | -0.04 | 0.00 % | ✅ |
| forza nella molla 2 = P/2 [F] | esatta | -2 | -2 | 0.00 % | ✅ |

**Note**: Valori di prova.

*Codice*: `validation/books/cook/ch09.py`

<a id="cook-9-2-bars-lagrange-multiplier"></a>

### Sez. 9.2 (Example), Eq. 9.2-1..9.2-4 - Tre barre con vincolo u2 = u3 imposto con moltiplicatore di Lagrange

✅ · pag. PDF 295 · bar, constraints

Sistema aumentato [[K, C^T], [C, 0]]{u, lambda} = {P, 0} con [C] = [0, 1, -1]: u1 = 3P/k, u2 = u3 = 5P/k e lambda = -P (forza nel collegamento rigido 2-3).

**Modello**: Come cook-9.1 (tre bielle, k = 10, P = 1): feagent elimina il vincolo per trasformazione; il moltiplicatore lambda = forza del vincolo al nodo 3 si ricava dall'equilibrio del nodo con lo sforzo della biella 2-3. Confronto anche con il sistema aumentato del libro risolto con numpy sulla [K] di feagent.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| u1 dal sistema aumentato contro feagent [L] | esatta | 0.3 | 0.3 | 0.00 % | ✅ |
| u3 [L] | libro | 0.5 | 0.5 | 0.00 % | ✅ |
| lambda dal sistema aumentato [F] | libro | -1 | -1 | 0.00 % | ✅ |
| forza del vincolo al nodo 3 (feagent) [F] | libro | -1 | -1 | 0.00 % | ✅ |

**Note**: Il segno di lambda dipende dalla convenzione di [C]: con [C] = [0, 1, -1] si ha lambda = -P.

*Codice*: `validation/books/cook/ch09.py`

<a id="cook-9-3-penalty-two-bars"></a>

### Sez. 9.3 (Example), Fig. 9.3-1, Eq. 9.3-4..9.3-6 - Due barre con vincolo u1 = u2 imposto con funzione di penalita'

✅ · pag. PDF 297 · bar, constraints

u1 = 2P/k per ogni alfa e u2 = (3 + 2 alfa) P/(k (1 + alfa)): con alfa = 0 u2 = 3P/k (nessun vincolo), con alfa -> infinito u2 -> 2P/k (vincolo rispettato).

**Modello**: Due molle assiali di feagent in serie (k = 10), P = 1 ai nodi 1 e 2; la penalita' [C] = [sqrt(k), -sqrt(k)] con numero alfa equivale a una molla di rigidezza alfa k fra i nodi 1 e 2 (alfa = 0, 1, 1e3, 1e6).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| u2, alfa = 0 [L] | libro | 0.3 | 0.3 | 0.00 % | ✅ |
| u2, alfa = 1 [L] | libro | 0.25 | 0.25 | 0.00 % | ✅ |
| u1 [L] | libro | 0.2 | 0.2 | 0.00 % | ✅ |
| u2, alfa = 1000 [L] | libro | 0.2001 | 0.2001 | 0.00 % | ✅ |
| u2, alfa = 1e+06 [L] | libro | 0.2 | 0.2 | 0.00 % | ✅ |

**Note**: Valori di prova.

*Codice*: `validation/books/cook/ch09.py`

<a id="cook-9-4-mindlin-beam-locking"></a>

### Sez. 9.4-9.5, Fig. 9.4-1, Eq. 9.4-1..9.4-5 - Locking a taglio di un elemento trave di Mindlin lineare

🟡 · pag. PDF 299 · beam, locking

Con il taglio integrato esattamente w2 = [12 (t/L)^2 + 20]/[12 (t/L)^2 + 5] 1,2 PL/(GA): per la trave snella tende a 4,8 PL/(GA), un valore minuscolo rispetto alla flessione (locking); con un punto di Gauss (rapporto di vincolo r = 2) il risultato migliora molto.

**Modello**: Elemento lineare di Mindlin (w e theta lineari) scritto con numpy, taglio integrato con 2 punti (esatto) o 1 punto; riferimento la trave di Timoshenko esatta di feagent (shear=True, Asy = A/1,2). Mensola b = 1, nu = 0 (E = 2G), fattore di forma 1,2, P all'estremo, t/L = 1, 0,1, 0,01.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| w2 integrazione piena, t/L = 1.0 (Eq. 9.4-5) [L] | libro | 2.2588 | 2.2588 | 0.00 % | ✅ |
| trave di Timoshenko di feagent, t/L = 1.0 [L] | esatta | 3.2 | 3.2 | 0.00 % | ✅ |
| w2 piena / esatta, t/L = 1.0 | libro | - | 0.70588 | - | ✅ |
| w2 con 1 punto = PL^3/(4EI) + 1,2 PL/(GA), t/L = 1.0 [L] | esatta | 2.7 | 2.7 | 0.00 % | ✅ |
| w2 con 1 punto / esatta, t/L = 1.0 | libro | - | 0.84375 | - | ✅ |
| w2 integrazione piena, t/L = 0.1 (Eq. 9.4-5) [L] | libro | 47.156 | 47.156 | 0.00 % | ✅ |
| trave di Timoshenko di feagent, t/L = 0.1 [L] | esatta | 2012 | 2012 | 0.00 % | ✅ |
| w2 piena / esatta, t/L = 0.1 | libro | - | 0.023438 | - | ✅ |
| w2 con 1 punto = PL^3/(4EI) + 1,2 PL/(GA), t/L = 0.1 [L] | esatta | 1512 | 1512 | 0.00 % | ✅ |
| w2 con 1 punto / esatta, t/L = 0.1 | libro | - | 0.75149 | - | ✅ |
| w2 integrazione piena, t/L = 0.01 (Eq. 9.4-5) [L] | libro | 479.91 | 479.91 | 0.00 % | ✅ |
| trave di Timoshenko di feagent, t/L = 0.01 [L] | esatta | 2.0001e+06 | 2.0001e+06 | 0.00 % | ✅ |
| w2 piena / esatta, t/L = 0.01 | libro | - | 2.3994e-04 | - | ✅ |
| w2 con 1 punto = PL^3/(4EI) + 1,2 PL/(GA), t/L = 0.01 [L] | esatta | 1.5001e+06 | 1.5001e+06 | 0.00 % | ✅ |
| w2 con 1 punto / esatta, t/L = 0.01 | libro | - | 0.75001 | - | ✅ |

**Note**: feagent non ha l'elemento lineare di Mindlin (la sua trave di Timoshenko e' esatta e non va in locking): l'elemento del libro e' scritto con numpy e si confronta con la formula di Eq. 9.4-5 e con la trave di feagent. Il libro non stampa la formula con un punto: con un punto vale w2 = PL^3/(4EI) + 1,2 PL/(GA), cioe' 3/4 della flessione esatta per la trave snella, senza locking.

*Codice*: `validation/books/cook/ch09.py`

<a id="cook-9-4-incompressibility-constraint-counting"></a>

### Sez. 9.4-9.6, Fig. 9.5-1, 9.5-2, 9.6-1 - Materiali quasi incomprimibili, conteggio dei vincoli e penalita' consistente

🟡 · pag. PDF 300 · plane, locking

Con nu -> 0,5 la condizione di incomprimibilita' vincola il campo di spostamento: se i vincoli (punti di integrazione del termine volumetrico) sono troppi rispetto ai gdl (r <= 1) la maglia va in locking. Valori di r del libro: Q4 a 1 punto 2, Q8 3x3 6/9, Q8 2x2 6/4, Q9 2x2 8/4, coppie di T6 a 3 punti 8/6.

**Modello**: Mensola 10 x 2 in deformazione piana (E = 1, nu = 0,3 e 0,4999, carico parabolico P = 1 all'estremo) con maglie 20 x 4 di PlaneQ4 (integrazione piena e 'volumetric'), PlaneQ8 (3x3 e 'reduced') e PlaneQ9 ('volumetric'); rapporto di vincolo r contato su una maglia 60 x 60.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| rapporto di vincolo r, Q4 1 punto | libro | 2 | 2.0672 | 3.36 % | ✅ |
| rapporto di vincolo r, Q8 3x3 | libro | 0.66667 | 0.68154 | 2.23 % | ✅ |
| rapporto di vincolo r, Q8 2x2 | libro | 1.5 | 1.5335 | 2.23 % | ✅ |
| rapporto di vincolo r, Q9 2x2 | libro | 2 | 2.0335 | 1.67 % | ✅ |
| rapporto di vincolo r, T6 3 punti | libro | 1.3333 | 1.3556 | 1.67 % | ✅ |
| freccia / riferimento, Q4 pieno, nu = 0,4999 | libro | - | 0.047158 | - | ✅ |
| freccia / riferimento, Q4 volumetrico, nu = 0,4999 | libro | - | 0.9514 | - | ✅ |
| freccia / riferimento, Q8 3x3, nu = 0,4999 | libro | - | 0.96222 | - | ✅ |
| freccia / riferimento, Q8 2x2, nu = 0,4999 | libro | - | 0.96682 | - | ✅ |
| freccia / riferimento, Q9 volumetrico, nu = 0,4999 | libro | - | 0.98561 | - | ✅ |
| Q4 a integrazione piena in locking (rapporto < 0,5) | libro | 1 | 1 | 0.00 % | ✅ |
| Q4 con termine volumetrico ridotto senza locking (rapporto > 0,9) | libro | 1 | 1 | 0.00 % | ✅ |

**Note**: Il libro e' qualitativo: si contano r (gdl per punto di vincolo, maglia grande) e si mostra il locking della freccia: rapporto con la freccia a nu = 0,4999 calcolata con Q9 'volumetric' 40 x 8. La penalita' consistente (Sez. 9.6) non e' riprodotta.

*Codice*: `validation/books/cook/ch09.py`

## Capitolo 10

<a id="cook-10-4-square-wave-fourier"></a>

### Sez. 10.4, Fig. 10.4-1, Eq. 10.4-4 - Serie di Fourier di un'onda quadra (carico uniforme su una luce)

📐 · pag. PDF 318 · other

Funzione -phi0 per -L < x < 0 e +phi0 per 0 < x < L, periodo 2L: i coefficienti sono nulli per n pari e q_n = 4 phi0/(n pi) per n dispari.

**Modello**: Coefficienti calcolati con integrazione numerica (numpy) e confrontati con la formula del libro; nessun elemento finito.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| coefficiente q_1 | esatta | 2.5465 | 2.5465 | 0.00 % | ✅ |
| coefficiente q_2 | esatta | 0 | 0 | 0.00 % | ✅ |
| coefficiente q_3 | esatta | 0.84883 | 0.84883 | 0.00 % | ✅ |
| coefficiente q_5 | esatta | 0.5093 | 0.5093 | 0.00 % | ✅ |

**Note**: E' il carico uniforme sviluppato in serie per l'esempio della trave appoggiata. Dati di prova phi0 = 2, L = 3.

*Codice*: `validation/books/cook/ch10.py`

<a id="cook-10-4-point-load-fourier"></a>

### Sez. 10.4, Eq. 10.4-5 - Serie di Fourier di un carico concentrato in mezzeria e freccia della trave

📐 · pag. PDF 319 · other, beam

Il carico P in mezzeria di una luce L ha q_n = (2P/L) sin(n pi/2): la serie del carico non converge, ma quella degli spostamenti si', e la freccia in mezzeria tende a P L^3/(48 EI).

**Modello**: Somma della serie del libro per la freccia e trave appoggiata di 40 travi feagent con carico concentrato in mezzeria.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| freccia dalla serie (3999 termini) | esatta | 0.020833 | 0.020833 | 0.00 % | ✅ |
| trave feagent contro la serie | altro | 0.020833 | 0.020833 | 0.00 % | ✅ |

**Note**: Con 4000 termini la serie della freccia coincide con la trave di feagent (esatta ai nodi). Dati: P = L = EI = 1.

*Codice*: `validation/books/cook/ch10.py`

<a id="cook-10-4-ss-beam-sine-load"></a>

### Sez. 10.4, Eq. 10.4-6..10.4-12, Fig. 10.4-2 - Trave appoggiata con carico sinusoidale: soluzione armonica disaccoppiata

✅ · pag. PDF 319 · beam

Ogni armonica del carico produce solo la stessa armonica degli spostamenti: w_n = (q_n/EI)(L/(n pi))^4 e M = -q_n L^2/(n pi)^2 sin(n pi x/L).

**Modello**: 80 travi feagent con carico trapezoidale per elemento che approssima q_n sin(n pi x/L), per n = 1, 3, 5.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| freccia all'ascissa 0.500 L, armonica n = 1 | esatta | 0.010266 | 0.010265 | 0.01 % | ✅ |
| freccia all'ascissa 0.500 L, armonica n = 3 | esatta | -1.2674e-04 | -1.2659e-04 | 0.12 % | ✅ |
| freccia all'ascissa 0.100 L, armonica n = 5 | esatta | 1.6426e-05 | 1.6373e-05 | 0.32 % | ✅ |

**Note**: Il carico a tratti lineari introduce uno scarto O(h^2) che cresce con n (0,3 % per n = 5 con 80 elementi). Dati: L = EI = q_n = 1.

*Codice*: `validation/books/cook/ch10.py`

## Capitolo 11

<a id="cook-11-3-mindlin-clamped-square-plate"></a>

### Sez. 11.3, Fig. 11.3-2, Tabella 11.3-1 - Piastra quadrata incastrata con elementi di Mindlin: locking al variare di L/t

🟡 · pag. PDF 346 · plate

Il libro confronta, con mesh 8x8, la freccia al centro calcolata e quella della piastra sottile al variare di L/t per elementi bilineari, quadratici, serendipity ed heterosis con integrazione ridotta, selettiva e completa: il bilineare completo si blocca, il bilineare selettivo e il lagrangiano ridotto restano vicini a 1 fino a L/t = 1e6.

**Modello**: Mesh 8x8 di gusci Q4 di Mindlin di feagent (add_shell) sull'intera piastra, carico uniforme, L/t da 30 a 1e6.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| w calcolata / w piastra sottile, L/t = 100 | esatta | 1 | 0.99064 | 0.94 % | ✅ |
| w calcolata / w piastra sottile, L/t = 1000 | esatta | 1 | 0.98871 | 1.13 % | ✅ |
| w calcolata / w piastra sottile, L/t = 10000 | esatta | 1 | 0.98869 | 1.13 % | ✅ |
| w calcolata / w piastra sottile, L/t = 1e+06 | esatta | 1 | 0.98863 | 1.14 % | ✅ |

**Note**: feagent non ha le piastre di Mindlin a 8 e 9 nodi ne' l'heterosis: si rifa' la curva del solo Q4, che non si blocca (rapporto 0,989 sulla piastra sottile da L/t = 100 a 1e6, come il bilineare selettivo del libro; quello di feagent ha il taglio assunto MITC4). I valori del libro sono solo in grafico. Riferimento w = 0,001265 q L^4/D (Timoshenko).

*Codice*: `validation/books/cook/ch11.py`

<a id="cook-11-4-dkt-square-plates"></a>

### Sez. 11.4, Tabella 11.4-1, Fig. 11.4-2 - Elemento DKT: freccia al centro di piastre quadrate

✅ · pag. PDF 351 · plate

Il libro tabula il rapporto fra la freccia al centro DKT e quella esatta di piastra sottile (nu = 0,3) per piastre appoggiate o incastrate con carico uniforme o concentrato: per esempio 1,025, 0,999, 1,001, 1,001 (appoggiata, uniforme) e 1,500, 1,228, 1,069, 1,021 (incastrata, uniforme).

**Modello**: Triangoli sottili di feagent (add_shell_tri, formulation='thin': flessione DKT) su un quarto di piastra con simmetria, diagonali verso il centro come in Fig. 11.4-2, N_es = 1, 2, 4, 8; appoggio classico (w = 0 e rotazione tangente nulla) o incastro; carico distribuito concentrato per un terzo ai vertici di ogni elemento.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| appoggiata, carico uniforme, N_es = 1 | libro | 1.025 | 1.025 | 0.00 % | ✅ |
| appoggiata, carico uniforme, N_es = 2 | libro | 0.999 | 0.99898 | 0.00 % | ✅ |
| appoggiata, carico uniforme, N_es = 4 | libro | 1.001 | 1.0012 | 0.02 % | ✅ |
| appoggiata, carico uniforme, N_es = 8 | libro | 1.001 | 1.0009 | 0.01 % | ✅ |
| incastrata, carico uniforme, N_es = 1 | libro | 1.5 | 1.4997 | 0.02 % | ✅ |
| incastrata, carico uniforme, N_es = 2 | libro | 1.228 | 1.2281 | 0.01 % | ✅ |
| incastrata, carico uniforme, N_es = 4 | libro | 1.069 | 1.0694 | 0.03 % | ✅ |
| incastrata, carico uniforme, N_es = 8 | libro | 1.021 | 1.0211 | 0.01 % | ✅ |
| appoggiata, carico concentrato, N_es = 1 | libro | 1.076 | 1.0762 | 0.02 % | ✅ |
| appoggiata, carico concentrato, N_es = 2 | libro | 1.008 | 1.0076 | 0.04 % | ✅ |
| appoggiata, carico concentrato, N_es = 4 | libro | 1.003 | 1.003 | 0.00 % | ✅ |
| appoggiata, carico concentrato, N_es = 8 | libro | 1.001 | 1.0012 | 0.02 % | ✅ |
| incastrata, carico concentrato, N_es = 1 | libro | 1.012 | 1.0123 | 0.03 % | ✅ |
| incastrata, carico concentrato, N_es = 2 | libro | 1.046 | 1.0456 | 0.04 % | ✅ |
| incastrata, carico concentrato, N_es = 4 | libro | 1.019 | 1.0192 | 0.02 % | ✅ |
| incastrata, carico concentrato, N_es = 8 | libro | 1.007 | 1.0073 | 0.03 % | ✅ |

**Note**: Riferimenti esatti come nel libro: 0,00406, 0,00126 q a^4/D e 0,01160, 0,00560 P a^2/D (Timoshenko). Le 16 celle coincidono entro l'arrotondamento a tre decimali.

*Codice*: `validation/books/cook/ch11.py`

<a id="cook-11-4-dkt-stiffness-code"></a>

### Sez. 11.4, Fig. 11.4-3 - Matrice di rigidezza 9x9 dell'elemento DKT

📐 · pag. PDF 352 · plate

Il libro riporta la subroutine Fortran della rigidezza DKT, integrata esattamente con tre punti; non stampa valori numerici.

**Modello**: Triangolo sottile di feagent (ShellT3Thin, flessione DKT): parte flessionale sui GdL w, rx, ry dei tre vertici.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| tre autovalori nulli (moti rigidi) | esatta | 0 | 2.4408e-16 | 0.00 % | ✅ |
| nessun altro modo a energia nulla | libro | 1 | 1 | 0.00 % | ✅ |
| energia della curvatura costante w,xx = 1 | esatta | 0.024038 | 0.024038 | 0.00 % | ✅ |
| energia della torsione costante w,xy = 1 | esatta | 0.033654 | 0.033654 | 0.00 % | ✅ |

**Note**: Senza il listato eseguibile si verificano le proprieta' che la matrice deve avere: tre autovalori nulli (moti rigidi w, rx, ry) e l'energia esatta di curvature costanti (w = x^2/2: U = D A/2; w = x y: U = D (1 - nu) A). Triangolo di prova (0,0), (1,2, 0,1), (0,3, 0,9).

*Codice*: `validation/books/cook/ch11.py`

<a id="cook-11-5-rhombic-plate"></a>

### Sez. 11.5, Fig. 11.5-2 - Piastra romboidale appoggiata (Morley): condizioni d'appoggio classiche e 'agli elementi finiti'

✅ · pag. PDF 354 · plate

Rombo di lato a, angolo acuto 30 gradi, nu = 0,3, carico uniforme. Il libro osserva che con una mesh 14 x 14 e appoggio classico la freccia al centro e' sottostimata di oltre il 20 %, mentre con l'appoggio 'agli elementi finiti' l'errore scende sotto il 3 %; la Fig. 11.5-2b da' i momenti al centro (Morley: 0,0191 e 0,0108 q a^2).

**Modello**: Rombo con angolo di 30 gradi suddiviso in N x N parallelogrammi divisi in due triangoli sottili DKT di feagent; appoggio 'agli elementi finiti' (solo w = 0) o classico (anche rotazione tangente nulla, con assi d'appoggio ruotati sui lati obliqui).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| appoggio classico, 14 x 14: w / w Morley (libro: oltre il 20 % in meno) | libro | 0.8 | 0.79606 | 0.49 % | ✅ |
| appoggio 'agli elementi finiti', 14 x 14: w / w Morley (libro: errore < 3 %) | libro | 1 | 1.0293 | 2.93 % | ✅ |
| momento principale maggiore al centro [q a^2] | libro | 0.0191 | 0.019127 | 0.14 % | ✅ |
| momento principale minore al centro [q a^2] | libro | 0.0108 | 0.010974 | 1.61 % | ✅ |

**Note**: Il libro parla di 'molti elementi' senza indicarne uno. Con il DKT di feagent e i parallelogrammi divisi lungo la diagonale corta, su 14 x 14 l'appoggio classico da' w = 0,796 volte il valore di Morley (-20,4 %) e quello 'agli elementi finiti' 1,029 (+2,9 %), come dice il libro. Con la diagonale lunga l'appoggio 'agli elementi finiti' sovrastima del 9 % su 14 x 14 (2,7 % su 32 x 32). Freccia esatta di Morley 0,408e-3 q a^4/D; i momenti principali al centro sono la media dei triangoli che toccano il nodo centrale (riferimento letto in Fig. 11.5-2b).

*Codice*: `validation/books/cook/ch11.py`

<a id="cook-11-5-patch-test-curvature"></a>

### Sez. 11.5, Fig. 11.5-3 - Patch test di curvatura costante per elementi di piastra

✅ · pag. PDF 354 · plate

Il patch e' sollecitato da Mx = 2 M_n/H; con w,y = 0 imposto sul contorno per evitare l'incurvamento dei bordi dovuto a nu, ogni elemento deve mostrare la stessa curvatura w,xx e quindi Mx = 2 M_n/H e My = nu Mx.

**Modello**: Quattro triangoli DKT di feagent attorno a un nodo interno 5 in posizione generica; lato sinistro incastrato, coppie nodali M_n ai nodi 2 e 3 del lato destro, w,y = 0 (rx) imposto ai nodi 1-4.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| Mx (principale) nell'elemento 1 | esatta | 2 | 2 | 0.00 % | ✅ |
| My = nu Mx nell'elemento 1 | esatta | 0.6 | 0.6 | 0.00 % | ✅ |
| Mx (principale) nell'elemento 2 | esatta | 2 | 2 | 0.00 % | ✅ |
| My = nu Mx nell'elemento 2 | esatta | 0.6 | 0.6 | 0.00 % | ✅ |
| Mx (principale) nell'elemento 3 | esatta | 2 | 2 | 0.00 % | ✅ |
| My = nu Mx nell'elemento 3 | esatta | 0.6 | 0.6 | 0.00 % | ✅ |
| Mx (principale) nell'elemento 4 | esatta | 2 | 2 | 0.00 % | ✅ |
| My = nu Mx nell'elemento 4 | esatta | 0.6 | 0.6 | 0.00 % | ✅ |

**Note**: Dimensioni libere: W = 2, H = 1, nodo interno (0,7, 0,6), nu = 0,3, M_n = 1.

*Codice*: `validation/books/cook/ch11.py`

<a id="cook-11-5-twisted-ribbon"></a>

### Sez. 11.5, Fig. 11.5-4 - Nastro torto: mensola sottile caricata da forze d'angolo al variare della lunghezza

🟡 · pag. PDF 355 · plate

Nastro largo 1, spesso 0,05, E = 1e7, nu = 0,25, lunghezza fino a 12: il libro mostra in grafico la freccia dell'angolo 3 contro L per il DKT a due triangoli (circa 40e-3 per L = 12), per un riferimento di 16 elementi di Kirchhoff (circa 33e-3) e per un elemento troppo rigido.

**Modello**: Due triangoli DKT di feagent (diagonale 1-3) e una mesh fine di DKT (24 x 4 rettangoli divisi); incastro sul lato 1-4, forza +1 al nodo 3 e -1 al nodo 2.

![cook-11.5-twisted-ribbon](images/books/cook-11.5-twisted-ribbon.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| DKT a due triangoli, L = 4 (letto in figura) | libro | 0.0128 | 0.012889 | 0.70 % | ✅ |
| DKT a due triangoli, L = 8 (letto in figura) | libro | 0.0262 | 0.026162 | 0.15 % | ✅ |
| DKT a due triangoli, L = 12 (letto in figura) | libro | 0.0397 | 0.039337 | 0.91 % | ✅ |
| mesh fine contro la torsione di Saint-Venant, L = 12 | esatta | 0.036 | 0.034902 | 3.05 % | ✅ |

**Note**: Valori letti in figura (precisione circa 1 %): il DKT di feagent a due triangoli segue la curva DKT del libro. La mesh fine (34,9e-3 a L = 12) si confronta con la torsione di Saint-Venant della striscia sottile, w3 = T L/(2 G J) con J = b t^3/3 (36,0e-3; lo scarto del 3 % e' l'ingobbamento impedito all'incastro); il riferimento a 16 elementi del libro si legge a circa 33e-3.

*Codice*: `validation/books/cook/ch11.py`

## Capitolo 12

<a id="cook-12-2-semicircular-arch"></a>

### Sez. 12.2, Fig. 12.2-1a - Arco semicircolare incastrato con carico in chiave: elementi rettilinei

🟡 · pag. PDF 366 · frame, shell

Arco semicircolare con R/t = 40, estremi incastrati, carico P in chiave: con 4 elementi rettilinei di lunghezza pari all'arco l'errore sullo spostamento del carico e' circa il 2 %, con la lunghezza della corda circa il 10 %; gli elementi curvi semplici si bloccano.

**Modello**: 4 travi feagent rettilinee con nodi sull'arco: con la lunghezza della corda (travi normali) oppure con la lunghezza dell'arco pi R/4 (matrice della trave di quella lunghezza ruotata sulla corda, come superelemento); riferimento con 400 elementi.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| riferimento a 400 elementi contro la sola flessione | esatta | 1 | 1.0053 | 0.53 % | ✅ |
| errore con 4 elementi di lunghezza pari all'arco (circa 2 %) | libro | 0.02 | 0.027337 | 36.69 % | ✅ |
| errore con 4 elementi di lunghezza pari alla corda (circa 10 %) | libro | 0.1 | 0.099609 | 0.39 % | ✅ |

**Note**: Il libro non da' E, sezione, P ne' il valore esatto: si usa R = 40, sezione rettangolare t = 1, E = P = 1 e come riferimento 400 elementi, che differisce dello 0,5 % dalla sola flessione (Castigliano) per la deformabilita' assiale. Con 4 elementi feagent sbaglia del 2,7 % (arco) e del 10,0 % (corda). Gli elementi d'arco curvi non esistono in feagent.

*Codice*: `validation/books/cook/ch12.py`

<a id="cook-12-2-curved-element-rigid-body"></a>

### Sez. 12.2, Eq. 12.2-12 - Elemento d'arco curvo con modo interno: energia spuria di moto rigido

⛔ · pag. PDF 368 · shell

Il libro riporta che i campi di Eq. 12.2-12 (con un modo interno condensato) riducono l'energia spuria del moto rigido rispetto a Eq. 12.2-7 di 122 000 volte per un elemento di 12 gradi e di 18 000 volte per 20 gradi (rif. 12.4).

**Note**: feagent non ha elementi d'arco curvi. Una ricostruzione con numpy dei campi del libro non riproduce i fattori stampati senza i dati del rif. 12.4 (moto rigido considerato, rapporto fra rigidezza membranale e flessionale, forma esatta del modo interno, leggibile solo in parte nel testo): con la sola energia membranale e la traslazione tangente si ottengono 4,4e6 e 5,7e5, quindi il caso resta non riproducibile.

*Codice*: `validation/books/cook/ch12.py`

<a id="cook-12-3-drilling-stiffness"></a>

### Sez. 12.3, Eq. 12.3-4 - Rigidezza fittizia per la rotazione normale (drilling) nei gusci piani

📐 · pag. PDF 372 · shell

Per il triangolo piano il libro aggiunge sulle rotazioni normali dei vertici la matrice alpha E V [[1,-1/2,-1/2],[-1/2,1,-1/2],[-1/2,-1/2,1]] (alpha circa 0,3), che non resiste alla rotazione rigida theta_z1 = theta_z2 = theta_z3.

**Modello**: Matrice del libro valutata con numpy; triangolo sottile di feagent (membrana GT9 con drilling) e triangolo 'cst' (drilling a penalita') per la rotazione rigida nel piano.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| autovalore nullo (rotazione rigida) | esatta | 0 | -5.5511e-17 | 0.00 % | ✅ |
| altri due autovalori 1,5 | esatta | 3 | 3 | 0.00 % | ✅ |
| triangolo sottile: forze della rotazione rigida al livello della stabilizzazione (1e-8) | libro | 1 | 1 | 0.00 % | ✅ |
| triangolo sottile: rz accoppiata alla membrana (vera rotazione di drilling) | libro | 1 | 1 | 0.00 % | ✅ |
| triangolo 'cst': forze della rotazione rigida al livello della stabilizzazione (1e-8) | libro | 1 | 1 | 0.00 % | ✅ |
| triangolo 'cst': rz accoppiata alla membrana (vera rotazione di drilling) | libro | 0 | 0 | 0.00 % | ✅ |

**Note**: La matrice del libro ha autovalori 0, 1,5, 1,5 (per alpha E V = 1) con autovettore nullo (1, 1, 1). In feagent il triangolo sottile (membrana GT9) ha una vera rotazione di drilling accoppiata a u e v, per cui la rotazione rigida nel piano non produce forze oltre la piccola stabilizzazione numerica (1e-8 della diagonale); il triangolo 'cst' ha la sola stabilizzazione, senza accoppiamento.

*Codice*: `validation/books/cook/ch12.py`

<a id="cook-12-4-cylinder-internal-pressure"></a>

### Sez. 12.4, Fig. 12.4-4 - Cilindro a estremi aperti sotto pressione interna: momento meridiano vicino al bordo

🟡 · pag. PDF 377 · shell, axisym

Cilindro R = 10 in, lunghezza 10 in, spessore 0,1 in, p = 200 psi, E = 200 000 psi, nu = 0,3, estremi aperti con appoggio radiale. Il libro mostra M_s lungo s: nullo fino a circa 7,5 in, massimo di circa 20 in-lb/in verso s = 9,4 e nullo all'estremo; 8 elementi conici di Mindlin seguono la teoria dei gusci sottili.

**Modello**: Mezzo cilindro (s = 5..10) con 80 x 2 elementi Q8 assialsimmetrici di feagent nello spessore (gusci di rivoluzione assenti in feagent); simmetria a s = 5, appoggio radiale del nodo medio a s = 10; momento meridiano integrando le tensioni assiali nello spessore.

![cook-12.4-cylinder-internal-pressure](images/books/cook-12.4-cylinder-internal-pressure.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| momento meridiano massimo (teoria 19,51) [in-lb/in] | esatta | 19.512 | 19.37 | 0.73 % | ✅ |
| ascissa del massimo (s = 9,39) [in] | esatta | 9.389 | 9.4062 | 0.18 % | ✅ |
| momento a s = 9,0 in [in-lb/in] | esatta | 16.06 | 15.947 | 0.70 % | ✅ |

**Note**: Il grafico e' confrontato con la teoria dei gusci sottili (bordo appoggiato): M = 2 beta^2 D w_p exp(-beta x) sin(beta x), massimo 19,51 in-lb/in a x = pi/(4 beta) = 0,611 in dal bordo (s = 9,39). Il catalogo aveva letto circa 22: il grafico ingrandito mostra un massimo poco sotto 20.

*Codice*: `validation/books/cook/ch12.py`

## Capitolo 13

<a id="cook-13-3-bar-beam-mass-matrices"></a>

### Sez. 13.3, Eq. 13.3-1..13.3-3, Fig. 13.3-1 - Matrici di massa consistente e concentrata di barra e trave

📐 · pag. PDF 391 · bar, beam, dynamics_modal

Il libro ricava la massa consistente della barra che si muove nel piano, m/6 [[2,0,1,0],[0,2,0,1],[1,0,2,0],[0,1,0,2]], e quella della trave di Hermite m/420 [[156,22L,54,-13L],...]; le masse concentrate sono m/2 per nodo (con inerzia rotazionale a L^2/210 opzionale, a = 17,5).

**Modello**: Biella feagent (TrussElement.consistent_mass_global), trave feagent (BeamElement3D.consistent_mass_local, GdL uy e rz nel piano X-Y), massa concentrata da Model.assemble_mass.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| barra: max |M feagent - M libro| | esatta | 0 | 0 | 0.00 % | ✅ |
| trave: max |M feagent - M libro| | esatta | 0 | 2.7756e-16 | 0.00 % | ✅ |
| trave: massa concentrata traslazionale m/2 | libro | 1.5 | 1.5 | 0.00 % | ✅ |
| coefficiente a dell'inerzia rotazionale concentrata | esatta | 17.5 | 17.5 | 0.00 % | ✅ |

**Note**: Dati di prova: L = 2, rho A = 1,5 (m = 3). La massa concentrata di feagent e' solo traslazionale (caso a = 0); il valore a = 17,5 e' l'inerzia di meta' trave attorno all'estremo, (m/2)(L/2)^2/3, verificata in forma chiusa.

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-3-cst-q4-consistent-mass"></a>

### Sez. 13.3, Fig. 13.3-2 - Matrici di massa consistenti di CST e rettangolo bilineare

📐 · pag. PDF 392 · plane_stress, dynamics_modal

Per elementi piani che si muovono nelle tre direzioni il libro da' blocchi uguali per u, v, w: CST rho A t/12 [[2,1,1],[1,2,1],[1,1,2]], rettangolo bilineare rho A t/36 [[4,2,1,2],[2,4,2,1],[1,2,4,2],[2,1,2,4]].

**Modello**: PlaneT3 e PlaneQ4 di feagent (consistent_mass), GdL ux, uy, uz per nodo.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| CST: max scarto dei blocchi u, v, w | esatta | 0 | 2.7756e-17 | 0.00 % | ✅ |
| rettangolo bilineare: max scarto dei blocchi | esatta | 0 | 8.3267e-17 | 0.00 % | ✅ |

**Note**: Dati di prova: rettangolo 2 x 1,5, triangolo qualunque, rho = 2, t = 0,3.

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-3-hrz-lumping"></a>

### Sez. 13.3, Eq. 13.3-4, 13.3-5, Fig. 13.3-3 - Masse diagonali HRZ di barra, trave, Q8 e Q9

📐 · pag. PDF 392 · bar, beam, plane_stress, dynamics_modal

Lo schema HRZ conserva la diagonale della massa consistente e la scala alla massa totale: barra m/2, m/2; trave m/78 diag[39, L^2, 39, L^2]; Q8 angoli 1/36 e lati 8/36 con quadratura 2x2, 3/76 e 16/76 con 3x3; Q9 1/36, 4/36, 16/36 con entrambe.

**Modello**: Masse consistenti di feagent (barra, trave, PlaneQ8, PlaneQ9) e lumped_masses (HRZ) degli elementi piani; la variante con quadratura 2x2 e' integrata con numpy sulle funzioni di forma di feagent.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| trave HRZ: massa su w (m 39/78) | libro | 1 | 1 | 0.00 % | ✅ |
| trave HRZ: inerzia su theta (m L^2/78) | libro | 0.10256 | 0.10256 | 0.00 % | ✅ |
| barra HRZ: m/2 | libro | 1 | 1 | 0.00 % | ✅ |
| Q8 HRZ 3x3: angolo 3/76 | libro | 0.039474 | 0.039474 | 0.00 % | ✅ |
| Q8 HRZ 3x3: lato 16/76 | libro | 0.21053 | 0.21053 | 0.00 % | ✅ |
| Q8 HRZ 2x2: angolo 1/36 | libro | 0.027778 | 0.027778 | 0.00 % | ✅ |
| Q8 HRZ 2x2: lato 8/36 | libro | 0.22222 | 0.22222 | 0.00 % | ✅ |
| Q9 HRZ: angolo | libro | 0.027778 | 0.027778 | 0.00 % | ✅ |
| Q9 HRZ 2x2: angolo | libro | 0.027778 | 0.027778 | 0.00 % | ✅ |
| Q9 HRZ: lato | libro | 0.11111 | 0.11111 | 0.00 % | ✅ |
| Q9 HRZ 2x2: lato | libro | 0.11111 | 0.11111 | 0.00 % | ✅ |
| Q9 HRZ: centro | libro | 0.44444 | 0.44444 | 0.00 % | ✅ |
| Q9 HRZ 2x2: centro | libro | 0.44444 | 0.44444 | 0.00 % | ✅ |

**Note**: feagent integra la massa del Q8 con una regola esatta, quindi lumped_masses restituisce i valori 3x3 del libro (3/76, 16/76).

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-3-thick-plate-frequencies-mass"></a>

### Sez. 13.3, Tabella 13.3-1 - Frequenze di una piastra spessa appoggiata: massa consistente e concentrata

🟡 · pag. PDF 393 · plate, dynamics_modal

Il libro confronta gli errori percentuali delle frequenze di una piastra spessa appoggiata (meta' piastra, mesh 4x2 di elementi di Mindlin a 8 nodi a integrazione ridotta) con massa consistente, HRZ e concentrata ad hoc: per esempio il modo (1,1) -0,11 %, +0,32 %, +0,32 % e il modo (3,3) +13,78 %, -5,18 %, -19,42 %.

**Modello**: Piastra intera con mesh 8x8 di gusci Q4 di Mindlin di feagent (add_shell), w = 0 sui lati e rotazione tangente nulla (appoggio di Navier); massa consistente traslazionale (assemble_mass_matrix) e massa concentrata a particelle uguali (somma delle righe, che per il Q4 rettangolare coincide con HRZ).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| modo (1,1), massa consistente | esatta | 0.58117 | 0.59095 | 1.68 % | ✅ |
| modo (1,1), massa concentrata | esatta | 0.58117 | 0.58304 | 0.32 % | ✅ |
| modo (1,2), massa consistente | esatta | 1.398 | 1.4822 | 6.02 % | ✅ |
| modo (1,2), massa concentrata | esatta | 1.398 | 1.4281 | 2.15 % | ✅ |
| modo (2,2), massa consistente | esatta | 2.1583 | 2.2952 | 6.34 % | ✅ |
| modo (2,2), massa concentrata | esatta | 2.1583 | 2.1587 | 0.02 % | ✅ |
| modo (1,1): la massa concentrata abbassa la frequenza | libro | 1 | 1 | 0.00 % | ✅ |

**Note**: feagent non ha la piastra di Mindlin a 8 nodi: la tabella non e' riproducibile cella per cella, e il libro non da' lo spessore (rimanda al rif. 13.5). Si usa t/a = 0,1, nu = 0,3 e si confrontano i modi bassi con la soluzione esatta di Mindlin senza inerzia rotazionale (la massa dei gusci di feagent e' solo traslazionale). Con la mesh grossolana di Q4 la massa consistente sovrastima di 1,7 % il modo (1,1) e di circa 6 % i modi (2,1) e (2,2); la massa concentrata compensa la rigidezza in eccesso ed e' piu' precisa (0,3 %, 2 %, 0,0 %), la stessa tendenza della colonna HRZ del libro.

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-3-optimal-lumping-quadratic-bar"></a>

### Sez. 13.3, Eq. 13.3-6..13.3-9, Fig. 13.3-4 - Concentrazione ottimale delle masse: barra quadratica, T6, Q8, Q9

📐 · pag. PDF 394 · bar, dynamics_modal

La massa concentrata 'ottimale' usa una quadratura con i punti nei nodi: per la barra quadratica Simpson da' rho A L/6 diag[1, 1, 4]; per T6 vertici 0 e lati A/3, per Q8 angoli -A/12 e lati A/3, per Q9 angoli A/36, lati A/9 e centro 4A/9.

**Modello**: Barra L3 di feagent (add_bar a 3 nodi), PlaneT6, PlaneQ8, PlaneQ9: pesi nodali int N_i dA calcolati con le funzioni di forma di feagent e quadratura esatta di numpy.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| barra L3: nodo estremo | libro | 0.16667 | 0.16667 | 0.00 % | ✅ |
| barra L3: nodo medio | libro | 0.66667 | 0.66667 | 0.00 % | ✅ |
| T6: vertice 0 | libro | 0 | -1.4537e-16 | 0.00 % | ✅ |
| T6: lato A/3 | libro | 0.33333 | 0.33333 | 0.00 % | ✅ |
| Q8: angolo -A/12 | libro | -0.083333 | -0.083333 | 0.00 % | ✅ |
| Q8: lato A/3 | libro | 0.33333 | 0.33333 | 0.00 % | ✅ |
| Q9: angolo | libro | 0.027778 | 0.027778 | 0.00 % | ✅ |
| Q9: lato | libro | 0.11111 | 0.11111 | 0.00 % | ✅ |
| Q9: centro | libro | 0.44444 | 0.44444 | 0.00 % | ✅ |

**Note**: Con nodi nelle posizioni di quadratura i pesi coincidono con gli integrali delle funzioni di forma, che si verificano direttamente. Elemento unitario (A = 1, L = 1).

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-4-rayleigh-damping"></a>

### Sez. 13.4, Eq. 13.4-1..13.4-3, Fig. 13.4-1 - Smorzamento di Rayleigh: coefficienti da due rapporti di smorzamento

📐 · pag. PDF 396 · dynamics_transient

Con [C] = a[K] + b[M] il rapporto di smorzamento e' xi = (a omega + b/omega)/2; imponendo xi1 e xi2 a omega1 e omega2 il libro ricava a e b in forma chiusa.

**Modello**: feagent.moving_dynamics.RayleighDamping.from_frequencies (C = a M + b K, con i nomi dei coefficienti scambiati rispetto al libro).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| coefficiente di [K] (a del libro) [s] | libro | 5.2403e-04 | 5.2403e-04 | 0.00 % | ✅ |
| coefficiente di [M] (b del libro) [1/s] | libro | 0.23064 | 0.23064 | 0.00 % | ✅ |
| xi a omega1 | libro | 0.02 | 0.02 | 0.00 % | ✅ |
| xi a omega2 | libro | 0.05 | 0.05 | 0.00 % | ✅ |

**Note**: Dati di prova: xi1 = 0,02 a 1 Hz, xi2 = 0,05 a 30 Hz.

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-5-free-bar-eigen"></a>

### Sez. 13.5, Eq. 13.5-8..13.5-10, Fig. 13.5-1 - Barra libera con un elemento: autovalori con massa consistente e concentrata

✅ · pag. PDF 400 · bar, dynamics_modal

Barra uniforme libera con un elemento: con massa consistente omega1 = 0 (moto rigido) e omega2 = (2/L) sqrt(3E/rho), il 10 % sopra l'esatto pi/L sqrt(E/rho); con massa concentrata omega2 = (2/L) sqrt(E/rho), sotto l'esatto.

**Modello**: Una barra L2 di feagent libera-libera (add_bar, rho), K e M dal modello (assemble_stiffness, assemble_mass_matrix) e massa HRZ (lumped_masses).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| omega1, massa consistente (moto rigido) [rad/s] | libro | 0 | 0 | 0.00 % | ✅ |
| omega2, massa consistente [rad/s] | libro | 3.4641 | 3.4641 | 0.00 % | ✅ |
| omega2, massa concentrata [rad/s] | libro | 2 | 2 | 0.00 % | ✅ |

**Note**: Dati: L = E = rho = A = 1.

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-7-cantilever-plate-guyan"></a>

### Sez. 13.7, Fig. 13.7-1 - Piastra quadrata a mensola: prime 4 frequenze, sistema completo e riduzione di Guyan

🟡 · pag. PDF 409 · plate, dynamics_modal

Piastra sottile quadrata incastrata su un lato, 90 GdL. Il libro da' omega = 3,469, 8,535, 21,450, 27,059 per il sistema completo e 3,473, 8,604, 22,690, 29,490 con 6 GdL master (frequenze adimensionali omega a^2 sqrt(rho t/D)).

**Modello**: Mesh 5x5 di quadrati divisi in due triangoli (diagonale come in figura), triangoli sottili DKT di feagent (add_shell_tri, formulation='thin') con massa consistente traslazionale; rotazioni prive di massa condensate; riduzione di Guyan sugli spostamenti laterali dei 6 nodi cerchiati in figura.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| omega1, sistema completo | libro | 3.469 | 3.4612 | 0.22 % | ✅ |
| omega2, sistema completo | libro | 8.535 | 8.5888 | 0.63 % | ✅ |
| omega3, sistema completo | libro | 21.45 | 22.068 | 2.88 % | ✅ |
| omega4, sistema completo | libro | 27.059 | 28.251 | 4.40 % | ✅ |
| omega1: rapporto Guyan / completo | libro | 1.0012 | 1.001 | 0.01 % | ✅ |
| omega2: rapporto Guyan / completo | libro | 1.0081 | 1.0085 | 0.04 % | ✅ |
| omega3: rapporto Guyan / completo | libro | 1.0578 | 1.0561 | 0.16 % | ✅ |
| omega4: rapporto Guyan / completo | libro | 1.0898 | 1.0926 | 0.25 % | ✅ |

**Note**: Il triangolo del libro (rif. 13.37) ha massa consistente completa, quello di feagent e' il DKT con massa solo traslazionale: le prime due frequenze coincidono entro l'1 %, la terza e la quarta escono il 3-5 % piu' alte. L'effetto della riduzione di Guyan (rapporto ridotto/completo, che dipende dalla scelta dei master) coincide invece entro lo 0,3 %. nu = 0,3 assunto.

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-7-beam-guyan"></a>

### Sez. 13.7, Eq. 13.7-8..13.7-13, Fig. 13.7-2 - Trave a un elemento: autovalori, riduzione di Guyan e massa concentrata

✅ · pag. PDF 410 · beam, dynamics_modal

Con due GdL il libro trova omega^2 = 6,1362 e 758,17 EI/(m L^3) (continuo 6,0881 e 493,13) e il modo theta2 = -1,5704/L; la riduzione di Guyan su w1 da' 6,1765 con recupero theta2 = -1,5000/L (statico) o -1,5709/L (con la massa), e il quoziente di Rayleigh col vettore recuperato torna a 6,1362; con m/2 concentrata in w1 si ha 6.

**Modello**: Una trave feagent (EI = 1, rho A = 1, L = 1) con w1 e theta2 liberi (theta1 = 0, w2 = 0); massa consistente; feagent.reduction.guyan con master w1.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| omega^2 modo 1 [EI/mL^3] | libro | 6.1362 | 6.1362 | 0.00 % | ✅ |
| omega^2 modo 2 [EI/mL^3] | libro | 758.17 | 758.17 | 0.00 % | ✅ |
| modo 1: theta2 con w1 = 1 [1/L] | libro | -1.5704 | -1.5704 | 0.00 % | ✅ |
| omega^2 ridotto di Guyan [EI/mL^3] | libro | 6.1765 | 6.1765 | 0.00 % | ✅ |
| theta2 recuperato (Eq. 13.7-2) [1/L] | libro | -1.5 | -1.5 | 0.00 % | ✅ |
| theta2 recuperato (Eq. 13.7-7) [1/L] | libro | -1.5709 | -1.5709 | 0.00 % | ✅ |
| quoziente di Rayleigh col vettore recuperato [EI/mL^3] | libro | 6.1362 | 6.1362 | 0.00 % | ✅ |
| omega^2 con m/2 concentrata in w1 [EI/mL^3] | libro | 6 | 6 | 0.00 % | ✅ |

**Note**: Il testo OCR scrive omega ma i valori sono omega^2 (6,0881 = (pi/2)^4).

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-8-cms-bar"></a>

### Sez. 13.8, Eq. 13.8-6..13.8-14, Tabella 13.8-1, Fig. 13.8-1 - Sintesi modale (CMS1 e Craig-Bampton) di una barra a gradino

✅ · pag. PDF 413 · bar, dynamics_modal

Il libro divide la barra in due sottostrutture (nodi 1-2 e 4-5, interfaccia 3), ne calcola i modi e confronta le prime tre frequenze 0,2651, 0,6156, 1,000 con quelle del metodo CMS1 (0,2656, 0,8794, 1,182) e di Craig-Bampton (0,2719, 0,9927, 1,268).

**Modello**: 5 barre feagent (aree A, A, 2A, 2A, 2A; AE/L = 1) con masse nodali 1, 2, 3, 4, 4 (rho A L/2 = 1), nodo 6 incastrato; Craig-Bampton con feagent.reduction.craig_bampton (interfaccia nodo 3, 2 modi interni); CMS1 con i vettori di Ritz K^-1 R.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| omega1, sistema completo | libro | 0.2651 | 0.26513 | 0.01 % | ✅ |
| omega2, sistema completo | libro | 0.6156 | 0.61563 | 0.00 % | ✅ |
| omega3, sistema completo | libro | 1 | 1 | 0.00 % | ✅ |
| sottostruttura 1: lambda1 | libro | 0.29289 | 0.29289 | 0.00 % | ✅ |
| sottostruttura 2: lambda1 | libro | 0.5 | 0.5 | 0.00 % | ✅ |
| CMS1: W(1,1) | libro | 6.768 | 6.7678 | 0.00 % | ✅ |
| CMS1: Kr(1,1) | libro | 15.91 | 15.907 | 0.02 % | ✅ |
| CMS1: Mr(1,1) | libro | 216.7 | 216.69 | 0.01 % | ✅ |
| omega1, CMS1 | libro | 0.2656 | 0.26567 | 0.03 % | ✅ |
| omega2, CMS1 | libro | 0.8794 | 0.8794 | 0.00 % | ✅ |
| omega3, CMS1 | libro | 1.182 | 1.1815 | 0.04 % | ✅ |
| omega1, CMS2 del libro | libro | 0.2719 | 0.2719 | 0.00 % | ✅ |
| omega2, CMS2 del libro | libro | 0.9927 | 0.99268 | 0.00 % | ✅ |
| omega3, CMS2 del libro | libro | 1.268 | 1.2679 | 0.01 % | ✅ |
| omega1, Craig-Bampton di feagent (un modo per sottostruttura) contro l'originale | altro | 0.2651 | 0.2653 | 0.08 % | ✅ |
| Craig-Bampton: modo di vincolo al nodo 4 | libro | 0.66667 | 0.66667 | 0.00 % | ✅ |

**Note**: Il metodo CMS2 del libro somma in una sola colonna i modi di pari ordine delle due sottostrutture (ampiezze vincolate fra loro): non e' il Craig-Bampton standard di feagent, che tiene un modo per sottostruttura in colonne separate e da' 0,2653, 0,6161, 1,051, molto piu' vicino all'originale. Il CMS2 del libro e' ricostruito con le matrici e il modo di vincolo di feagent. I vettori di Ritz di CMS1 sono le risposte statiche alle forme modali delle sottostrutture usate come carichi (modi di pari ordine sommati) e a un carico unitario all'interfaccia: riproducono [W], [Kr] e [Mr] stampati.

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-10-bar-wave-propagation"></a>

### Sez. 13.10, Fig. 13.10-2..13.10-5 - Propagazione d'onda in una barra con le differenze centrali

✅ · pag. PDF 422 · bar, dynamics_transient

Barra d'acciaio L = 20 in, A = 1 in^2, E = 30e6 psi, rho = 7,4e-4 lb s^2/in^4, forza a gradino P0 = 100 lb all'estremo libero. Il libro ricava omega_max = 8,0523e5 rad/s e dt_crit = 2,483e-6 s, integra 83 passi con dt = 2,4e-6 s e mostra la tensione dell'elemento 20 (x = 9,75 in): circa -100 psi con oscillazioni spurie, poi -200 psi dopo la riflessione; con dt = 2,5e-6 s lo schema e' instabile.

**Modello**: 40 barre L2 di feagent con massa concentrata, estremo destro incastrato; Model.solve_time_history(method='central_difference').

![cook-13.10-bar-wave-propagation](images/books/cook-13.10-bar-wave-propagation.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| omega_max dell'elemento 2c/L [rad/s] | libro | 8.0539e+05 | 8.0539e+05 | 0.00 % | ✅ |
| omega_max della mesh [rad/s] | libro | 8.0523e+05 | 8.0523e+05 | 0.00 % | ✅ |
| passo critico 2/omega_max [s] | libro | 2.4830e-06 | 2.4838e-06 | 0.03 % | ✅ |
| tensione media all'elemento 20 dopo l'arrivo dell'onda [psi] | esatta | -100 | -99.778 | 0.22 % | ✅ |
| tensione media dopo la riflessione [psi] | esatta | -200 | -199.96 | 0.02 % | ✅ |
| dt = 2,5e-6 s riconosciuto instabile | libro | 1 | 1 | 0.00 % | ✅ |

**Note**: dt_crit = 2,4838e-6 s: il libro tronca a 2,483e-6. La storia di tensione e' solo in grafico: si confrontano le medie nel tempo sui due plateau (60-140 us e dopo 165 us) con i valori esatti -100 e -200 psi. L'instabilita' con dt = 2,5e-6 s e' segnalata dall'integratore di feagent (errore dt > 2/omega_max).

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-12-newmark-stability-table"></a>

### Sez. 13.12, Eq. 13.12-5..13.12-7, Tabella 13.12-1 - Famiglia di Newmark: limiti di stabilita'

📐 · pag. PDF 429 · dynamics_transient

Il libro tabula i limiti dei metodi di Newmark: accelerazione media (1/4, 1/2) incondizionatamente stabile, accelerazione lineare (1/6, 1/2) Omega_crit = 2 sqrt3, Fox-Goodwin (1/12, 1/2) sqrt6, differenze centrali (0, 1/2) 2; per 2 beta >= gamma >= 1/2 la stabilita' e' incondizionata.

**Modello**: Matrice di amplificazione di un oscillatore costruita con un passo di feagent.dynamics.newmark_integrate e central_difference_integrate; raggio spettrale e ricerca per bisezione di Omega_crit = omega dt.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| Omega_crit, accelerazione lineare | libro | 3.4641 | 3.4641 | 0.00 % | ✅ |
| Omega_crit, Fox-Goodwin | libro | 2.4495 | 2.4495 | 0.00 % | ✅ |
| Omega_crit, differenze centrali | libro | 2 | 2 | 0.00 % | ✅ |
| accelerazione media: raggio spettrale massimo (Omega fino a 500) | libro | 1 | 1 | 0.00 % | ✅ |
| gamma = 0,6, beta = (gamma + 1/2)^2/4: raggio < 1 (dissipativo) | libro | 1 | 1 | 0.00 % | ✅ |

**Note**: Le differenze centrali sono la Newmark con beta = 0, che l'integratore implicito non accetta: si usa l'integratore esplicito di feagent. La variante HHT non e' in feagent.

*Codice*: `validation/books/cook/ch13.py`

<a id="cook-13-13-period-error"></a>

### Sez. 13.13, Eq. 13.13-1..13.13-26, Fig. 13.13-2 - Errore di periodo delle differenze centrali e della regola trapezia

📐 · pag. PDF 431 · dynamics_transient

Il libro ricava che le differenze centrali accorciano il periodo (cos Omega_bar = 1 - Omega^2/2) e la regola trapezia lo allunga (tan(Omega_bar/2) = Omega/2), entrambe senza errore d'ampiezza.

**Modello**: Oscillatore Z'' + omega^2 Z = 0 integrato con central_difference_integrate e newmark_integrate (1/4, 1/2) di feagent; periodo numerico dall'argomento degli autovalori della matrice di amplificazione.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| differenze centrali: periodo numerico/esatto, Omega = 0.5 | esatta | 0.98939 | 0.98939 | 0.00 % | ✅ |
| differenze centrali: ampiezza, Omega = 0.5 | esatta | 1 | 1 | 0.00 % | ✅ |
| regola trapezia: periodo numerico/esatto, Omega = 0.5 | esatta | 1.0205 | 1.0205 | 0.00 % | ✅ |
| regola trapezia: ampiezza, Omega = 0.5 | esatta | 1 | 1 | 0.00 % | ✅ |
| differenze centrali: periodo numerico/esatto, Omega = 1.5 | esatta | 0.88437 | 0.88437 | 0.00 % | ✅ |
| differenze centrali: ampiezza, Omega = 1.5 | esatta | 1 | 1 | 0.00 % | ✅ |
| regola trapezia: periodo numerico/esatto, Omega = 1.5 | esatta | 1.1655 | 1.1655 | 0.00 % | ✅ |
| regola trapezia: ampiezza, Omega = 1.5 | esatta | 1 | 1 | 0.00 % | ✅ |

**Note**: Verifica a Omega = omega dt = 0,5 e 1,5.

*Codice*: `validation/books/cook/ch13.py`

## Capitolo 14

<a id="cook-14-1-beam-column-energy"></a>

### Sez. 14.1, Eq. 14.1-1..14.1-9, Fig. 14.1-1 - Trave-colonna appoggiata: irrigidimento tensionale e carico euleriano

📐 · pag. PDF 449 · beam, buckling

Con w = wc sin(pi x/L) il libro ottiene (k + k_sigma) wc = 2 qc L/pi con k = pi^4 EI/(2L^3) e k_sigma = pi^2 P/(2L): wc = qc L^4/(pi^4 EI + pi^2 P L^2) e P_cr = -pi^2 EI/L^2.

**Modello**: Trave appoggiata di 20 travi feagent con carico laterale sinusoidale (carichi trapezoidali per elemento) e forza assiale di trazione o compressione; analisi del secondo ordine (solve_pdelta) e buckling lineare.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| freccia in mezzeria, P = +0.5 pi^2 EI/L^2 | esatta | 0.006844 | 0.0068405 | 0.05 % | ✅ |
| freccia in mezzeria, P = -0.5 pi^2 EI/L^2 | esatta | 0.020532 | 0.020521 | 0.05 % | ✅ |
| freccia in mezzeria, P = +0.0 pi^2 EI/L^2 | esatta | 0.010266 | 0.010261 | 0.05 % | ✅ |
| carico euleriano [EI/L^2] | esatta | 9.8696 | 9.8696 | 0.00 % | ✅ |

**Note**: Per il carico sinusoidale la funzione a un parametro e' la soluzione esatta: la formula del libro e' esatta e feagent la raggiunge al raffinamento. Dati: L = EI = qc = 1, P = +-0,5 pi^2 EI/L^2; il carico sinusoidale e' approssimato a tratti lineari su 40 elementi (scarto 0,05 %).

*Codice*: `validation/books/cook/ch14.py`

<a id="cook-14-2-stress-stiffness-beam-bar"></a>

### Sez. 14.2, Eq. 14.2-9, 14.2-11; Sez. 14.4, Eq. 14.4-9 - Matrici di rigidezza geometrica di trave e barra

📐 · pag. PDF 454 · beam, truss, buckling

La trave di Hermite con forza assiale P ha [k_sigma] = P/(30L) [[36, 3L, -36, 3L], [3L, 4L^2, -3L, -L^2], ...]; la barra (w1, w2) ha P/L [[1, -1], [-1, 1]].

**Modello**: BeamElement3D.geometric_stiffness_local di feagent, GdL uy, rz nel piano X-Y.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| trave: max |k_sigma feagent - libro| | esatta | 0 | 5.5511e-17 | 0.00 % | ✅ |
| energia della rotazione rigida: trave = barra | esatta | 6.0000e-04 | 6.0000e-04 | 0.00 % | ✅ |

**Note**: La matrice della barra non e' un oggetto di feagent: si verifica che la trave di feagent dia la stessa energia P L theta^2 per la rotazione rigida, che e' il contenuto della matrice di barra. Dati di prova: L = 2, P = 3.

*Codice*: `validation/books/cook/ch14.py`

<a id="cook-14-4-one-bar-large-rotation"></a>

### Sez. 14.4, Eq. 14.4-10..14.4-16, Fig. 14.4-1 - Barra a un elemento con deformazioni di Green-Lagrange: rotazione rigida e linearizzazione

📐 · pag. PDF 459 · truss, nonlinear_geometric

Barra incernierata al nodo 1 con forze Rx, Rz al nodo 2: con le deformazioni di Green-Lagrange l'equilibrio e' cubico negli spostamenti, la rotazione rigida da' forze nulle per ogni angolo e la linearizzazione attorno a una forza assiale P da' (AE/L)[[1,0],[0,0]] + (P/L)[[3,0],[0,1]].

**Modello**: Equazioni del libro valutate con numpy (rotazione rigida, jacobiano numerico); barra feagent con analisi corotazionale (solve_corotational) per la rigidezza trasversale di una barra tesa.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| forze per rotazione rigida (0,3, 1,0, 2,5 rad) | esatta | 0 | 1.7789e-13 | 0.00 % | ✅ |
| termine assiale AE/L + 3P/L (a meno di O(u0^2)) | esatta | 1006 | 1006 | 0.00 % | ✅ |
| termine trasversale P/L | esatta | 1.996 | 1.996 | 0.00 % | ✅ |
| feagent corotazionale: rigidezza trasversale della barra tesa = N/L (L deformata) | esatta | 1.996 | 1.996 | 0.00 % | ✅ |

**Note**: Il termine trasversale P/L e' lo stesso dell'analisi corotazionale di feagent (che usa la deformazione ingegneristica): lo si misura applicando un piccolo carico trasversale alla barra tesa (con una molla verticale di servizio, poi sottratta). Il fattore 3 sul termine assiale e' proprio di Green-Lagrange. Dati: L = 1, AE = 1000, P = 2.

*Codice*: `validation/books/cook/ch14.py`

<a id="cook-14-5-shallow-two-bar-snap"></a>

### Sez. 14.5, Fig. 14.5-1 - Asta elastica inclinata incernierata: carico critico linearizzato e collasso non lineare

🟡 · pag. PDF 462 · frame, buckling, nonlinear_geometric

Asta con proiezione 400 e rialzo H = 35,0 o 3,49, E = 2000, I = 2000, A = 391. Il libro confronta il carico critico con [K_sigma] (21,35 e 0,520), con [N1] (20,58 e 0,173) e il collasso non lineare (20,47 e 0,100).

**Modello**: Asta di 8-16 travi feagent nel piano X-Y, cerniera a sinistra, estremo destro guidato verticalmente (cerniera), carico P verticale: buckling lineare (Model.buckling) e analisi corotazionale a spostamento imposto (solve_corotational, control='displacement').

![cook-14.5-shallow-two-bar-snap](images/books/cook-14.5-shallow-two-bar-snap.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| carico critico con [K_sigma], H = 35.0 | libro | 21.35 | 21.345 | 0.02 % | ✅ |
| carico di collasso non lineare, H = 35.0 | libro | 20.47 | 20.484 | 0.07 % | ✅ |
| carico critico con [K_sigma], H = 3.49 | libro | 0.52 | 0.51938 | 0.12 % | ✅ |
| carico di collasso non lineare, H = 3.49 | libro | 0.1 | 0.099951 | 0.05 % | ✅ |

**Note**: Con H = 35 il carico critico e' l'instabilita' euleriana dell'asta (pi^2 EI/L^2 sin alfa), con H = 3,49 lo scatto della biella AE sin^3 alfa/cos^2 alfa. Il collasso con H = 35 e' una biforcazione: per seguirla si aggiunge in mezzeria un'imperfezione di forza trasversale 1e-4 P (16 elementi). Con H = 3,49 la forma chiusa AE sin^3 alfa/cos^2 alfa vale 0,5194 e il libro stampa 0,520. La variante con [N1] (rigidezza degli spostamenti iniziali) non e' in feagent.

*Codice*: `validation/books/cook/ch14.py`

<a id="cook-14-5-cantilever-column-buckling"></a>

### Sez. 14.5, Eq. 14.5-8..14.5-10, Fig. 14.5-2 - Colonna a mensola: carico critico con un elemento di trave

✅ · pag. PDF 463 · beam, buckling

Con un elemento e la [k_sigma] consistente il libro trova lambda_cr = 2,4860 EI/L^2 (esatto pi^2/4 = 2,4674) e il modo w2 = 0,6379 L theta2; con la [k_sigma] di barra otterrebbe 3 EI/L^2.

**Modello**: Una trave feagent verticale (EI = L = 1) incastrata alla base, carico di punta, Model.buckling; poi 10 elementi.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| carico critico, 1 elemento [EI/L^2] | libro | 2.486 | 2.486 | 0.00 % | ✅ |
| modo: w2 / theta2 [L] | libro | 0.6379 | 0.63785 | 0.01 % | ✅ |
| carico critico, 10 elementi (esatto pi^2/4) [EI/L^2] | esatta | 2.4674 | 2.4674 | 0.00 % | ✅ |

**Note**: La variante con la [k_sigma] di barra si ricava dalla stessa condensazione con la sola parte P/L: verificata in forma chiusa nel caso della condensazione.

*Codice*: `validation/books/cook/ch14.py`

<a id="cook-14-5-buckling-condensation"></a>

### Sez. 14.5, Eq. 14.5-11, 14.5-12 - Condensazione della rotazione nel buckling della mensola

✅ · pag. PDF 464 · beam, buckling

Eliminando theta2 con la trasformazione statica T = [1; 3/(2L)] (come nella riduzione di Guyan, con k_sigma al posto della massa) il carico critico sale a 2,5 EI/L^2.

**Modello**: K e k_sigma dell'elemento trave di feagent (stiffness_local, geometric_stiffness_local) ridotte su w2 con T = [1; 3/(2L)].

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| carico critico con theta2 condensata [EI/L^2] | libro | 2.5 | 2.5 | 0.00 % | ✅ |
| carico critico con la k_sigma di barra [EI/L^2] | libro | 3 | 3 | 0.00 % | ✅ |

**Note**: Con la sola parte P/L di barra al posto della k_sigma consistente, lo stesso problema senza condensazione da' 3 EI/L^2 come dice il libro.

*Codice*: `validation/books/cook/ch14.py`

## Capitolo 15

<a id="cook-15-3-weighted-residual-bar"></a>

### Sez. 15.3, Eq. 15.3-1..15.3-14, Tabella 15.3-1, Fig. 15.3-1 - Metodi dei residui pesati su una barra con carico assiale lineare

📐 · pag. PDF 479 · bar

Per u,xx + c x = 0 con u(0) = 0 e u,x(L) = b (b = 0, c = L = 1) il libro confronta la soluzione esatta (u(L/2) = 0,2292, u(L) = 0,3333, u,x(0) = 0,5) con collocazione, sottodominio, minimi quadrati e Galerkin; Galerkin da' gli spostamenti esatti nei nodi ma u,x(0) = 0,5833 e u,x(L) = 0,0833.

**Modello**: Colonna esatta: 32 barre L2 di feagent con carico q = c x (add_bar_line_load con valori nodali). Colonna di Galerkin: una sola barra L3 di feagent (lo spazio di prova a1 x + a2 x^2 con u(0) = 0 e' quello dell'elemento quadratico). Collocazione, sottodominio e minimi quadrati: algebra del libro valutata con numpy.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| esatto: u(L/2) | libro | 0.2292 | 0.22917 | 0.01 % | ✅ |
| esatto: u(L) | libro | 0.3333 | 0.33333 | 0.01 % | ✅ |
| Galerkin (barra L3): u(L/2) | libro | 0.2292 | 0.22917 | 0.01 % | ✅ |
| Galerkin (barra L3): u(L) | libro | 0.3333 | 0.33333 | 0.01 % | ✅ |
| Galerkin (barra L3): u,x(0) | libro | 0.5833 | 0.58333 | 0.01 % | ✅ |
| Galerkin (barra L3): u,x(L/2) | libro | 0.3333 | 0.33333 | 0.01 % | ✅ |
| Galerkin (barra L3): u,x(L) | libro | 0.0833 | 0.083333 | 0.04 % | ✅ |
| collocazione: u(L/2) | libro | 0.125 | 0.125 | 0.00 % | ✅ |
| collocazione: u(L) | libro | 0.1667 | 0.16667 | 0.02 % | ✅ |
| collocazione: u,x(0) | libro | 0.3333 | 0.33333 | 0.01 % | ✅ |
| sottodominio: u(L/2) | libro | 0.1875 | 0.1875 | 0.00 % | ✅ |
| sottodominio: u(L) | libro | 0.25 | 0.25 | 0.00 % | ✅ |
| sottodominio: u,x(0) | libro | 0.5 | 0.5 | 0.00 % | ✅ |

**Note**: Dai coefficienti stampati si ricavano i punti usati: collocazione in x = L/3, sottodominio e minimi quadrati con l'integrale del residuo su tutta la barra.

*Codice*: `validation/books/cook/ch15.py`

<a id="cook-15-4-galerkin-bar-beam"></a>

### Sez. 15.4, Eq. 15.4-1..15.4-20 - Galerkin per la barra e per la dinamica della trave: le matrici standard

📐 · pag. PDF 481 · bar, beam, dynamics_modal

Il libro mostra che il metodo di Galerkin restituisce [k] = int B^T AE B dx e {r} = int N^T q dx per la barra e [K]{D} + [M]{D''} = {R} con le matrici consistenti per la trave, identiche a quelle di Rayleigh-Ritz.

**Modello**: Integrali di Galerkin calcolati con numpy (funzioni di forma lineari e di Hermite) contro la barra L2 di feagent (stiffness, carico add_bar_line_load) e la trave feagent (stiffness_local, consistent_mass_local).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| barra: int B^T AE B dx | esatta | 0 | 0 | 0.00 % | ✅ |
| barra: int N^T q dx | esatta | 0 | 4.4409e-16 | 0.00 % | ✅ |
| trave: int B^T EI B dx | esatta | 0 | 4.4409e-15 | 0.00 % | ✅ |
| trave: int N^T rho A N dx | esatta | 0 | 1.1102e-16 | 0.00 % | ✅ |

**Note**: Dati di prova: L = 2, AE = 3, q lineare da 1 a 4; trave EI = 5, rho A = 0,7.

*Codice*: `validation/books/cook/ch15.py`

<a id="cook-15-4-heat-bar-galerkin"></a>

### Sez. 15.4, Eq. 15.4-21..15.4-24, Fig. 15.4-3 - Conduzione stazionaria in una barra: elemento di Galerkin a 2 nodi

📐 · pag. PDF 485 · heat_steady

Barra a superficie laterale isolata: (A k/L)[[1,-1],[-1,1]]{T} = {A q1, A q2}; con T1 = T0 e flusso q_L uscente a destra T2 = T0 - q_L L/k; scambiando le condizioni T1 = T_L + q0 L/k.

**Modello**: FieldModel('heat') con un elemento L2 (area A, conducibilita' k), temperatura imposta a un estremo e flusso uscente all'altro (add_flux negativo = uscente).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| (A k/L)[[1,-1],[-1,1]] | esatta | 0 | 0 | 0.00 % | ✅ |
| T2 = T0 - q_L L/k | esatta | 76 | 76 | 0.00 % | ✅ |
| condizioni scambiate: T1 = T_L + q0 L/k | esatta | 124 | 124 | 0.00 % | ✅ |

**Note**: Dati di prova: A = 0,02, k = 50, L = 0,4, T0 = 100, q = 3000.

*Codice*: `validation/books/cook/ch15.py`

<a id="cook-15-6-quasiharmonic-galerkin"></a>

### Sez. 15.6, Eq. 15.6-1..15.6-7 - Equazione quasi-armonica 2D: matrice di conduttivita' e vettori di Galerkin

📐 · pag. PDF 488 · heat_steady, potential_flow

Per (kx phi,x),x + (ky phi,y),y + Q = 0 il libro ricava [k] = int ([N,x]^T kx [N,x] + [N,y]^T ky [N,y]) dA e {r} = int [N]^T Q dA + int [N]^T q_B dS.

**Modello**: FieldElement Q4 di feagent distorto con conducibilita' ortotropa kx, ky (conduction, source, add_flux) contro gli integrali del libro calcolati con numpy.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| matrice di conduttivita' | esatta | 0 | 2.2204e-16 | 0.00 % | ✅ |
| vettore dei termini noti (sorgente + flusso di bordo) | esatta | 0 | 8.8818e-16 | 0.00 % | ✅ |

**Note**: Dati di prova: quadrilatero (0,0), (2,0,2), (1,8, 1,5), (0,1, 1,2), kx = 3, ky = 0,5, spessore 1, Q = 7, q_B = 2 sul lato 2-3; integrali con Gauss 2x2 come l'elemento.

*Codice*: `validation/books/cook/ch15.py`

## Capitolo 16

<a id="cook-16-2-heat-bar-convection"></a>

### Sez. 16.2, Eq. 16.2-1..16.2-8, Fig. 16.2-1 - Barra con conduzione, convezione laterale e sorgente: matrici e soluzione

📐 · pag. PDF 495 · heat_steady

Per un elemento lineare il libro ricava [k] = (A k/L)[[1,-1],[-1,1]], [h_ls] = (h p L/6)[[2,1],[1,2]], {r_Q} = (Q A L/2){1,1} e {r_ls} = (h p T_f L/2){1,1}.

**Modello**: FieldModel('heat') con elementi L2 (area A, perimetro p), convezione laterale (add_lateral_convection) e sorgente per unita' di volume (add_source); matrici assemblate confrontate con le formule del libro, poi aletta di 40 elementi contro la soluzione esatta.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| [k] + [h_ls] | esatta | 0 | 0 | 0.00 % | ✅ |
| {r_Q} + {r_ls} | esatta | 0 | 5.6843e-14 | 0.00 % | ✅ |
| temperatura in punta, 40 elementi | esatta | 50.106 | 50.103 | 0.01 % | ✅ |

**Note**: Dati del Probl. 16.2 del libro (k = 180, h = 12, A = 0,1, p = 1, L = 2), che non ha risposta stampata; per la soluzione si assumono T0 = 100 alla base, punta isolata, T_f = 20, Q = 0: esatto T_f + (T0 - T_f) cosh(m(L - x))/cosh(mL).

*Codice*: `validation/books/cook/ch16.py`

<a id="cook-16-5-q4-heat-matrices"></a>

### Sez. 16.5, Eq. 16.5-5..16.5-10, Fig. 16.5-1 - Elemento Q4 per la conduzione: convezione di bordo e sorgente puntuale

📐 · pag. PDF 502 · heat_steady

Con convezione h verso T_f sul lato 3-4 di lunghezza L34 il libro ottiene [h] = (tau h L34/6)[[2,1],[1,2]] sui nodi 3 e 4 e {r_h} = (tau h T_f L34/2){0,0,1,1}; una sorgente puntuale Q_P da' {r_Q} = [N]_P^T Q_P.

**Modello**: Un FieldElement Q4 di feagent di spessore tau con convezione sul lato 3-4 (add_convection, lato 2 nella numerazione di feagent); la matrice e il vettore di bordo si ottengono come differenza fra assemblaggi con e senza convezione.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| matrice di convezione del lato 3-4 | esatta | 0 | 2.2204e-16 | 0.00 % | ✅ |
| vettore di convezione del lato 3-4 | esatta | 0 | 1.4211e-14 | 0.00 % | ✅ |
| sorgente puntuale: somma delle quote = Q_P | esatta | 1 | 1 | 0.00 % | ✅ |
| sorgente puntuale: momento delle quote nel punto P | esatta | 0 | 0 | 0.00 % | ✅ |

**Note**: Dati di prova: quadrilatero (0,0), (3,0), (2,5, 2), (0,5, 1,5), tau = 0,2, h = 8, T_f = 30. La sorgente puntuale interna non e' un carico di feagent (solo nodale): la sua forma [N]_P^T Q_P si verifica con le funzioni di forma dell'elemento (somma Q_P, riproduzione del punto).

*Codice*: `validation/books/cook/ch16.py`

<a id="cook-16-6-thermal-transient-trapezoid"></a>

### Sez. 16.6, Eq. 16.6-6..16.6-9 - Transitori termici: regola trapezia generalizzata e passo critico

📐 · pag. PDF 504 · heat_transient

Per [K]{T} + [C]{T'} = {R} con la regola trapezia generalizzata il libro ricava il passo critico dt_cr = 2/((1 - 2 beta) lambda_max) per beta < 1/2; beta = 1/2 (Crank-Nicolson), 2/3 (Galerkin) e 1 (Eulero all'indietro) sono incondizionatamente stabili.

**Modello**: Barra di 10 elementi L2 di FieldModel con capacita' (rho c = 1), estremo a T = 0, condizione iniziale T = 1; FieldModel.eigenvalues per lambda_max e FieldModel.solve_transient(theta) con passi attorno al critico.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| beta = 0.0: dt = 0,98 dt_cr limitato | libro | 1 | 1 | 0.00 % | ✅ |
| beta = 0.0: dt = 1,02 dt_cr divergente | libro | 1 | 1 | 0.00 % | ✅ |
| beta = 0.25: dt = 0,98 dt_cr limitato | libro | 1 | 1 | 0.00 % | ✅ |
| beta = 0.25: dt = 1,02 dt_cr divergente | libro | 1 | 1 | 0.00 % | ✅ |
| beta = 0.500: stabile con dt = 50/lambda_max | libro | 1 | 1 | 0.00 % | ✅ |
| beta = 0.667: stabile con dt = 50/lambda_max | libro | 1 | 1 | 0.00 % | ✅ |
| beta = 1.000: stabile con dt = 50/lambda_max | libro | 1 | 1 | 0.00 % | ✅ |

**Note**: Il beta del libro e' il theta di feagent. Con theta = 0 e theta = 0,25 si integra a 0,98 e 1,02 volte il passo critico: la soluzione resta limitata nel primo caso e cresce nel secondo.

*Codice*: `validation/books/cook/ch16.py`

<a id="cook-16-7-potential-flow-cylinder"></a>

### Sez. 16.7, Fig. 16.7-1 - Flusso potenziale attorno a un cilindro in un canale: potenziale e funzione di corrente

🟡 · pag. PDF 507 · potential_flow

Il libro imposta il flusso uniforme u0 che entra in un canale con un ostacolo cilindrico: per il potenziale phi,n = u0 all'ingresso, phi = 0 sulla mezzeria CD, flusso nullo su pareti e cilindro; per la funzione di corrente psi = u0 y all'ingresso, psi = 0 su AB e sul cilindro, psi = u0 H sulla parete, psi,x = 0 su CD.

**Modello**: Quadrante a monte modellato con Q8 di FieldModel('potential') e FieldModel('stream') su una mesh mappata propria (blocco attorno al cilindro piu' blocco d'ingresso), con le condizioni al contorno della Fig. 16.7-1b e c.

![cook-16.7-potential-flow-cylinder](images/books/cook-16.7-potential-flow-cylinder.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| portata attraverso CD dal potenziale (conservazione: u0 H) | esatta | 4 | 4.0013 | 0.03 % | ✅ |
| velocita' sopra il cilindro: potenziale contro funzione di corrente | altro | 2.1118 | 2.1356 | 1.12 % | ✅ |

**Note**: Il libro non da' dimensioni ne' risultati: si assumono raggio a = 1, semialtezza H = 4, lunghezza a monte 10. Si verificano la portata u0 H attraverso CD e la coincidenza della velocita' sopra il cilindro calcolata con phi e con psi; per un canale largo questa velocita' supera 2 u0 (cilindro isolato) per effetto dell'ingombro: 2,112 u0 col potenziale. Il gradiente di psi sul cilindro (dove psi e' imposto) converge piu' lentamente: 1,1 % di scarto con la mesh usata (24 x 24 Q8 attorno al cilindro), 2 % con quella dimezzata.

*Codice*: `validation/books/cook/ch16.py`

<a id="cook-16-9-slosh-one-element"></a>

### Sez. 16.9, Eq. 16.9-7, 16.9-8, Fig. 16.9-2b - Oscillazione del liquido in un serbatoio con un elemento 'mock-fluid'

✅ · pag. PDF 514 · dynamics_modal, other

Serbatoio piano largo a e profondo b con un solo elemento: la rigidezza volumetrica sotto-integrata impone w3 = -w4 e resta il modo di sciabordio con omega^2 = 3 g/b per a = b (teoria (pi g/a) tanh(pi b/a) = 3,13 g/b).

**Modello**: Un PlaneQ4 di feagent in deformazione piana con modulo di comprimibilita' B e taglio quasi nullo (nu -> 0,5, E = 3B(1 - 2nu)), integrazione ridotta a un punto, massa consistente; rigidezza della superficie libera rho g a/6 [[2,1],[1,2]] come superelemento (add_superelement); liberi solo w3 e w4.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| omega^2, integrazione ridotta [g/b] | libro | 3 | 3 | 0.00 % | ✅ |
| teoria (pi g/a) tanh(pi b/a) [g/b] | libro | 3.13 | 3.1299 | 0.00 % | ✅ |
| integrazione completa: modo bloccato | libro | 1 | 1 | 0.00 % | ✅ |

**Note**: Dati: a = b = 1, rho = 1, g = 1, B = 1e4. Con integrazione completa 2x2 la rigidezza volumetrica blocca il modo (omega^2 molto piu' grande): e' il motivo della sotto-integrazione nel libro.

*Codice*: `validation/books/cook/ch16.py`

<a id="cook-16-9-acoustic-pipe-spring"></a>

### Sez. 16.9, Eq. 16.9-9, 16.9-10, Fig. 16.9-2c - Modi acustici dell'aria in un tubo con due elementi

✅ · pag. PDF 514 · bar, dynamics_modal

Tubo di lunghezza L_T = 2L chiuso agli estremi rigidi: con due elementi 'mock-fluid' il libro trova omega^2 = 3B/(rho L^2) = 12 c^2/L_T^2, cioe' omega = 3,46 c/L_T contro l'esatto pi c/L_T.

**Modello**: Spostamenti: 2 barre L2 di feagent con E = B e massa consistente; pressione: FieldModel con capacita' 1/c^2 (autovalori K x = lambda C x, modi acustici); poi 40 elementi.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| omega, barre (2 elementi) [c/L_T] | libro | 3.4641 | 3.4641 | 0.00 % | ✅ |
| omega, pressione (2 elementi) [c/L_T] | libro | 3.4641 | 3.4641 | 0.00 % | ✅ |
| omega, 40 elementi (esatto pi) [c/L_T] | esatta | 3.1416 | 3.1424 | 0.03 % | ✅ |

**Note**: Dati: B = 1,4e5, rho = 1,2, L_T = 2. La formulazione in pressione (FieldModel) e quella in spostamento (barre) danno lo stesso primo modo; il caso con molla sul tappo non ha valori numerici nel libro.

*Codice*: `validation/books/cook/ch16.py`

## Capitolo 17

<a id="cook-17-2-nonlinear-spring-methods"></a>

### Sez. 17.2, Eq. 17.2-1..17.2-16, Fig. 17.2-1..17.2-9 - Molla non lineare: sostituzione diretta, Newton-Raphson, Newton modificato

📐 · pag. PDF 522 · spring, nonlinear_material

Il libro presenta i metodi per una molla di rigidezza k0 + kN(u) e propone (Probl. 17.2) la molla addolcente k = 0,2 - u con P = 0,006, il cui equilibrio (0,2 - u) u = P ha radice u = (0,2 - sqrt(0,016))/2 = 0,036754.

**Modello**: Molla feagent con legge N(Delta) non lineare a tratti (NonlinearElastic, 400 tratti sulla curva) risolta con solve_inelastic; gli schemi iterativi del libro su una molla scritti in numpy.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| spostamento, molla NonlinearElastic di feagent | esatta | 0.036754 | 0.036754 | 0.00 % | ✅ |
| Newton-Raphson | esatta | 0.036754 | 0.036754 | 0.00 % | ✅ |
| Newton modificato | esatta | 0.036754 | 0.036754 | 0.00 % | ✅ |
| sostituzione diretta | esatta | 0.036754 | 0.036754 | 0.00 % | ✅ |

**Note**: Il libro non stampa risultati numerici: il riferimento e' la radice esatta. Newton converge in poche iterazioni, la sostituzione diretta e il Newton modificato in molte di piu', come descritto nel testo.

*Codice*: `validation/books/cook/ch17.py`

<a id="cook-17-3-tapered-bar-plasticity"></a>

### Sez. 17.3, Eq. 17.3-1..17.3-7, Fig. 17.3-1..17.3-3 - Barra rastremata elasto-plastica: metodo incrementale con rigidezza tangente

📐 · pag. PDF 531 · bar, nonlinear_material

Il libro descrive per una barra rastremata la legge bilineare E, E_t, sigma_Y con H = E E_t/(E - E_t), il metodo della rigidezza tangente e quello delle rigidezze iniziali, e lo scarico elastico con incrudimento isotropo o cinematico.

**Modello**: 4 barre L2 di feagent ad area decrescente (2, 1,6, 1,2, 0,8) incastrate a sinistra, carico assiale in punta; legge bilineare J2Plasticity.bilinear (E, E_t, sigma_Y) con incrudimento isotropo; solve_inelastic con carico, poi scarico.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| spostamento di punta a P = 1,4 | esatta | 0.063708 | 0.063708 | 0.00 % | ✅ |
| deformazione plastica dell'elemento 4 | esatta | 0.03375 | 0.03375 | 0.00 % | ✅ |
| spostamento permanente dopo lo scarico | esatta | 0.04125 | 0.04125 | 0.00 % | ✅ |

**Note**: Il libro non da' numeri: la barra e' isostatica, quindi lo spostamento di punta esatto si ottiene elemento per elemento dalla legge bilineare. Dati liberi: E = 200, E_t = 20, sigma_Y = 1, L_e = 1, P = 1,4 (snervano gli elementi 3 e 4), poi scarico a zero: resta lo spostamento permanente sum eps_p L.

*Codice*: `validation/books/cook/ch17.py`

<a id="cook-17-4-uniaxial-plasticity"></a>

### Sez. 17.4, Eq. 17.4-16..17.4-19 - Plasticita' di von Mises monoassiale con incrudimento cinematico

📐 · pag. PDF 538 · nonlinear_material

Specializzando la plasticita' di von Mises al caso monoassiale il libro ritrova d eps_p = d eps E/(E + H) e il modulo elasto-plastico E_ep = E H/(E + H) = E_t, coincidenti con la legge bilineare della Sez. 17.3.

**Modello**: Aggiornamento monoassiale J2Plasticity.update_1d di feagent con incrudimento cinematico (H_kin = H).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| incremento plastico d eps E/(E + H) | esatta | 0.0088889 | 0.0088889 | 0.00 % | ✅ |
| modulo elasto-plastico E H/(E + H) | esatta | 22.222 | 22.222 | 0.00 % | ✅ |
| tensione dopo l'incremento | esatta | 1.2222 | 1.2222 | 0.00 % | ✅ |
| all'inversione lo snervamento riprende a sigma_max - 2 sigma_Y | libro | 1 | 1 | 0.00 % | ✅ |

**Note**: Dati di prova: E = 200, H = 25, sigma_Y = 1; incremento di deformazione oltre lo snervamento e inversione (effetto Bauschinger: nuovo snervamento a sigma_max - 2 sigma_Y).

*Codice*: `validation/books/cook/ch17.py`

<a id="cook-17-6-bar-dynamic-plasticity"></a>

### Sez. 17.6, Fig. 17.6-1, 17.6-2 - Onda elastica e onda plastica in una barra elasto-plastica (differenze centrali)

✅ · pag. PDF 544 · bar, dynamics_transient, nonlinear_material

La barra della Sez. 13.10 (L = 20 in, A = 1 in^2, E = 30e6 psi, rho = 7,4e-4) con sigma_Y = 40 ksi, E_t = E/4 e P0 = 2 A sigma_Y = 80 000 lb, dt = 2,4e-6 s per 83 passi. A x = 9,75 in arriva prima l'onda elastica (c_e, t = 0,0484 ms) che porta la tensione a -40 ksi, poi quella plastica (c_e/2, t = 0,0968 ms) che la porta a -80 ksi.

**Modello**: 40 barre L2 di feagent con masse concentrate e legge bilineare isotropa (J2Plasticity.bilinear, E_t = E/4), incastro a destra, forza a gradino a sinistra; solve_inelastic_explicit (differenze centrali).

![cook-17.6-bar-dynamic-plasticity](images/books/cook-17.6-bar-dynamic-plasticity.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| tensione media fra onda elastica e onda plastica [psi] | esatta | -40000 | -40518 | 1.30 % | ✅ |
| tensione media dopo l'onda plastica [psi] | esatta | -80000 | -81021 | 1.28 % | ✅ |
| arrivo dell'onda elastica (t = 0,0484 ms) [s] | libro | 4.8424e-05 | 5.0400e-05 | 4.08 % | ✅ |
| arrivo dell'onda plastica (t = 0,0968 ms) [s] | libro | 9.6848e-05 | 1.0080e-04 | 4.08 % | ✅ |

**Note**: Le storie sono in grafico: si confrontano le medie della tensione sui due plateau (fra i due arrivi e dopo l'onda plastica) con i valori esatti -40 e -80 ksi e i tempi di arrivo (meta' del gradino) con 9,75/c_e e 9,75/(c_e/2): il campionamento a passo dt = 2,4 us (5 % del primo tempo) limita la precisione dei tempi d'arrivo.

*Codice*: `validation/books/cook/ch17.py`

<a id="cook-17-7-cantilever-tip-moment"></a>

### Sez. 17.7, Eq. 17.7-2..17.7-7, Fig. 17.7-1a - Mensola con momento di punta in grandi rotazioni (corotazionale piano)

✅ · pag. PDF 551 · frame, nonlinear_geometric

Sotto momento di punta la mensola si incurva ad arco di cerchio: v = (EI/M)(1 - cos(M L/EI)), u = (EI/M) sin(M L/EI) - L, rotazione M L/EI; con M = 2 pi EI/L si chiude ad anello.

**Modello**: Mensola di 20 travi feagent nel piano X-Y, momento in punta, analisi corotazionale (solve_corotational) in 40 passi fino a M = 2 pi EI/L.

![cook-17.7-cantilever-tip-moment](images/books/cook-17.7-cantilever-tip-moment.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| freccia di punta, rotazione 1.571 rad | esatta | 6.3662 | 6.3678 | 0.03 % | ✅ |
| spostamento assiale di punta, rotazione 1.571 rad | esatta | -3.6338 | -3.6322 | 0.05 % | ✅ |
| freccia di punta, rotazione 3.142 rad | esatta | 6.3662 | 6.3727 | 0.10 % | ✅ |
| spostamento assiale di punta, rotazione 3.142 rad | esatta | -10 | -10 | 0.00 % | ✅ |
| M = 2 pi EI/L: la punta torna all'incastro (u = -L) | esatta | -10 | -10 | 0.00 % | ✅ |

**Note**: Gli elementi rettilinei approssimano l'arco con le corde: scarto O(1/n^2) (0,1 % con 20 elementi). Dati: E = 1e4, I = 1/12, L = 10.

*Codice*: `validation/books/cook/ch17.py`

## Capitolo 18

<a id="cook-18-2-ill-conditioning-springs"></a>

### Sez. 18.2, Eq. 18.2-1..18.2-3, Fig. 18.2-1 - Mal condizionamento: sistema quasi singolare e molla rigida su molla cedevole

📐 · pag. PDF 563 · spring

Il libro mostra che il sistema x - y = 4, -x + 1,02 y = -2 cambia soluzione del 100 % se 1,02 diventa 1,01, e che con k1 = 40 appoggiata su k2 = 0,0014 il coefficiente ridotto K22 = (k1 + k2) - k1 vale 0,0010 invece di 0,0014 con 5 cifre e la matrice diventa singolare con 4 cifre.

**Modello**: Due molle feagent in serie (k2 = 0,0014 a terra, k1 = 40 fra i due nodi) per la matrice K; eliminazione di Gauss con mantissa decimale di 4, 5 e 6 cifre emulata in Python.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| y con coefficiente 1,02 | esatta | 100 | 100 | 0.00 % | ✅ |
| rapporto delle soluzioni (raddoppio) | libro | 2 | 2 | 0.00 % | ✅ |
| K22 ridotto, mantissa di 6 cifre | libro | 0.0014 | 0.0014 | 0.00 % | ✅ |
| K22 ridotto, mantissa di 5 cifre | libro | 0.001 | 0.001 | 0.00 % | ✅ |
| K22 ridotto, 4 cifre (matrice singolare) | libro | 0 | 0 | 0.00 % | ✅ |

**Note**: In doppia precisione feagent non mostra il fenomeno: la mantissa corta e' emulata arrotondando ogni operazione, sulla matrice assemblata da feagent.

*Codice*: `validation/books/cook/ch18.py`

<a id="cook-18-3-condition-bar"></a>

### Sez. 18.3, Fig. 18.3-1, Eq. 18.3-1..18.3-10 - Numero di condizionamento: barra a due elementi e stime di crescita

📐 · pag. PDF 567 · bar, beam

Il libro definisce C(K) = lambda_max/lambda_min della matrice scalata e ne stima la crescita come (h_max/h_min)^(2m-1) N^(2m/n): per la trave (m = 2, n = 1) un elemento dieci volte piu' corto moltiplica C per circa 1000 e il raddoppio di N per 16.

**Modello**: Barra a 2 elementi di rigidezza 6AE/L e AE/L; mensole di travi feagent e catene di barre feagent con N elementi; numero di condizionamento della matrice scalata a diagonale unitaria.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| C(K) della barra a due elementi | esatta | 2.2153 | 2.2153 | 0.00 % | ✅ |
| trave: crescita di C raddoppiando N (64 -> 128) | libro | 16 | 15.993 | 0.05 % | ✅ |
| barra: crescita di C raddoppiando N | esatta | 4 | 4.0003 | 0.01 % | ✅ |
| trave: elemento corto 10 -> 100 volte (fattore ~1000) | libro | 1000 | 984.65 | 1.54 % | ✅ |

**Note**: La stima e' asintotica: il fattore 1000 si raggiunge fra rapporti di lunghezza 10 e 100 (fra 1 e 10 il fattore e' solo 34). Per la barra (m = 1) il raddoppio di N da' 4. La barra a due elementi del libro ha C = (1 + 1/sqrt7)/(1 - 1/sqrt7).

*Codice*: `validation/books/cook/ch18.py`

<a id="cook-18-4-diagonal-decay-cantilever"></a>

### Sez. 18.4, Tabella 18.4-1 - Rapporti di decadimento diagonale e numerazione dei nodi in una mensola lunga

🟡 · pag. PDF 571 · beam

Con la numerazione dalla punta all'incastro gli ultimi rapporti valgono 8, 2, 8 per ogni N e la freccia e' esatta; con la numerazione dall'incastro alla punta i rapporti crescono come N^3 e N (1e3, 1e6, 2e8; 4e1, 4e2, 2e3) e su un calcolatore a circa 11 cifre la freccia per N = 1000 vale 0,1197 volte quella teorica.

**Modello**: Mensola di N = 10, 100, 1000 travi feagent uguali (EI = 8e6, lunghezza 1000, carico in punta); eliminazione di Gauss senza pivot sulla matrice di feagent nelle due numerazioni, rapporti r_i = K_ii / P_ii.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| w/w teorica, numerazione dalla punta, N = 10 | libro | 1 | 1 | 0.00 % | ✅ |
| r_2N-2, numerazione dalla punta, N = 10 | libro | 8 | 8 | 0.00 % | ✅ |
| r_2N-1, numerazione dalla punta, N = 10 | libro | 2 | 2 | 0.00 % | ✅ |
| r_2N, numerazione dalla punta, N = 10 | libro | 8 | 8 | 0.00 % | ✅ |
| w/w teorica, numerazione dalla punta, N = 100 | libro | 1 | 1 | 0.00 % | ✅ |
| r_2N-2, numerazione dalla punta, N = 100 | libro | 8 | 8 | 0.00 % | ✅ |
| r_2N-1, numerazione dalla punta, N = 100 | libro | 2 | 2 | 0.00 % | ✅ |
| r_2N, numerazione dalla punta, N = 100 | libro | 8 | 8 | 0.00 % | ✅ |
| w/w teorica, numerazione dalla punta, N = 1000 | libro | 1 | 1 | 0.00 % | ✅ |
| r_2N-2, numerazione dalla punta, N = 1000 | libro | 8 | 8 | 0.00 % | ✅ |
| r_2N-1, numerazione dalla punta, N = 1000 | libro | 2 | 2 | 0.00 % | ✅ |
| r_2N, numerazione dalla punta, N = 1000 | libro | 8 | 8 | 0.00 % | ✅ |
| w/w teorica, numerazione dall'incastro, N = 10 | libro | 1 | 1 | 0.00 % | ✅ |
| r_2N-2, numerazione dall'incastro, N = 10 | libro | 5.3 | 5.256 | 0.83 % | ✅ |
| r_2N-1, numerazione dall'incastro, N = 10 | libro | 1000 | 1000 | 0.00 % | ✅ |
| r_2N, numerazione dall'incastro, N = 10 | libro | 40 | 40 | 0.00 % | ✅ |
| r_2N-2, numerazione dall'incastro, N = 100 | libro | 7.7 | 7.6848 | 0.20 % | ✅ |
| r_2N-1, numerazione dall'incastro, N = 100 | libro | 1.0000e+06 | 1e+06 | 0.00 % | ✅ |
| r_2N, numerazione dall'incastro, N = 100 | libro | 400 | 400 | 0.00 % | ✅ |

**Note**: feagent lavora a 16 cifre: tutte le frecce risultano esatte (rapporto 1,0000), quindi i valori 0,9993 e 0,1197 del libro (perdita di cifre della macchina a 11 cifre) non sono riproducibili. Per lo stesso motivo con N = 1000 e numerazione incastro-punta il calcolo a 16 cifre da' r = 1,0e9 e 4,0e3 invece dei 2e8 e 2e3 stampati: i pivot del libro sono gia' corrotti dall'arrotondamento. Gli altri rapporti coincidono.

*Codice*: `validation/books/cook/ch18.py`

<a id="cook-18-6-bar-discretization-error"></a>

### Sez. 18.6, Eq. 18.6-1..18.6-9, Fig. 18.6-1 - Errore di discretizzazione nella barra con carico distribuito

📐 · pag. PDF 573 · bar

Il libro mostra che con carichi consistenti gli spostamenti nodali della barra sono esatti, che l'errore di deformazione e' limitato da h max|u''| ed e' nullo al centro dell'elemento, e che l'errore di spostamento fra i nodi e' al piu' h^2/8 max|u''|.

**Modello**: Barra feagent incastrata a sinistra, 4 elementi L2, carico assiale uniforme q con forze nodali consistenti (add_bar_line_load).

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| massimo errore sugli spostamenti nodali | esatta | 0 | 3.5527e-15 | 0.00 % | ✅ |
| errore di spostamento a meta' elemento = h^2/8 |u''| | esatta | 0.125 | 0.125 | 0.00 % | ✅ |
| deformazione al centro dell'elemento 2 (esatta) | esatta | 2.5 | 2.5 | 0.00 % | ✅ |
| errore di deformazione all'estremo = (h/2)|u''| <= h|u''| | esatta | 0.5 | 0.5 | 0.00 % | ✅ |

**Note**: Dati liberi: L = 4, EA = 1, q = 1 (u'' = -q/EA costante). Le funzioni di forma lineari danno in mezzo all'elemento l'errore massimo h^2/8 |u''|.

*Codice*: `validation/books/cook/ch18.py`

<a id="cook-18-7-laplacian-mesh-estimate"></a>

### Sez. 18.7, Eq. 18.7-3, Fig. 18.7-1 - Stima a priori dell'errore relativo su una mesh graduata

⛔ · pag. PDF 579 · plane_stress

Il libro valuta una stima a priori dell'errore (N = 72 elementi bilineari, rapporti di forma 2 e di dimensione 8): circa 0,22 sugli spostamenti e 1,9 sulle tensioni.

**Note**: E' una stima d'ordine senza una mesh ne' un problema definiti nodo per nodo: non c'e' un modello da rifare con feagent.

*Codice*: `validation/books/cook/ch18.py`

<a id="cook-18-7-cantilever-regular-refinement"></a>

### Sez. 18.7, Fig. 18.7-4, Tabella 18.7-1 - Mensola con elementi bilineari: raffinamento regolare ed estrapolazione di Richardson

✅ · pag. PDF 582 · plane_stress

Mensola L = 2,40, h = 0,40, E = 1e4, nu = 0,25, P = 100 in punta. Il libro tabula la freccia per le tre integrazioni e le tre mesh e la estrapola con p = 2; la teoria della trave con taglio da' 8,640 + 0,180 = 8,820.

**Modello**: Q4 di feagent in tensione piana su mesh 4x2, 8x4 e 16x8 (N = 8, 32, 128): integrazione 2x2 (Full), un punto (Reduced) e un punto con controllo hourglass (integration='reduced', hourglass=True); incastro a sinistra, P = 100 nello spigolo superiore libero.

![cook-18.7-cantilever-regular-refinement](images/books/cook-18.7-cantilever-regular-refinement.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| freccia, Full (2x2), N = 8 | libro | 4.562 | 4.5624 | 0.01 % | ✅ |
| freccia, Full (2x2), N = 32 | libro | 7.124 | 7.124 | 0.00 % | ✅ |
| freccia, Full (2x2), N = 128 | libro | 8.302 | 8.3019 | 0.00 % | ✅ |
| estrapolazione di Richardson p = 2, Full, mesh 8-32 | libro | 7.978 | 7.9779 | 0.00 % | ✅ |
| estrapolazione di Richardson p = 2, Full, mesh 32-128 | libro | 8.695 | 8.6945 | 0.01 % | ✅ |
| freccia, Reduced (1 punto), N = 8 (spigolo inferiore) | libro | 11.44 | 11.44 | 0.00 % | ✅ |
| freccia, Reduced (1 punto), N = 32 | libro | 9.323 | 9.3233 | 0.00 % | ✅ |
| freccia, Reduced (1 punto), N = 128 | libro | 8.922 | 8.922 | 0.00 % | ✅ |
| estrapolazione di Richardson p = 2, Reduced, mesh 8-32 | libro | 8.617 | 8.6177 | 0.01 % | ✅ |
| estrapolazione di Richardson p = 2, Reduced, mesh 32-128 | libro | 8.788 | 8.7882 | 0.00 % | ✅ |
| freccia, H-G (1 punto + hourglass), N = 8 | libro | 8.572 | 8.5718 | 0.00 % | ✅ |
| freccia, H-G (1 punto + hourglass), N = 32 | libro | 8.719 | 8.7192 | 0.00 % | ✅ |
| freccia, H-G (1 punto + hourglass), N = 128 | libro | 8.771 | 8.7707 | 0.00 % | ✅ |
| estrapolazione di Richardson p = 2, H-G, mesh 8-32 | libro | 8.768 | 8.7684 | 0.00 % | ✅ |
| estrapolazione di Richardson p = 2, H-G, mesh 32-128 | libro | 8.788 | 8.7879 | 0.00 % | ✅ |
| teoria della trave con taglio (t = 1) | libro | 8.82 | 8.82 | 0.00 % | ✅ |
| H-G su mesh 32x16 contro la teoria della trave | esatta | 8.82 | 8.7878 | 0.37 % | ✅ |

**Note**: Spessore: la Fig. 18.7-4 quota 0,10, ma tutti i numeri della tabella (e la teoria della trave 8,820) corrispondono a t = 1,0: con t = 0,10 le frecce sarebbero 10 volte maggiori. Si usa t = 1,0. La freccia e' letta al nodo di mezzeria dell'estremo libero, con P applicato nello spigolo superiore: cosi' coincidono tutte le celle tranne Reduced N = 8, dove il libro stampa 11,440, che e' la freccia dello spigolo inferiore (a mezza altezza feagent da' 11,483); con 11,440 il libro calcola anche l'estrapolato 8,617, che si ritrova solo usando quel valore. Si confronta quindi quella cella con lo spigolo inferiore.

*Codice*: `validation/books/cook/ch18.py`

<a id="cook-18-7-beam-with-hole-irregular"></a>

### Sez. 18.7, Fig. 18.7-6 - Mensola con grande foro centrale: tensione massima al raffinamento della mesh

🟡 · pag. PDF 582 · plane_stress

Trave L = 2,50, altezza 0,50, spessore 0,10, foro centrale r = 0,20, E = 1e4, nu = 0,25, carico P = 0,10 all'estremo. Il libro trova sigma_max = 51,37, 61,15, 67,18 ai centri degli elementi con tre mesh irregolari, estrapola 93,3 e cita il dato fotoelastico 98,4 in flessione pura sul bordo superiore del foro.

**Modello**: Meta' superiore della trave con mesh mappate proprie (blocco radiale attorno al foro e due blocchi rettangolari) di Q8 (160 e 640 elementi); incastro in x = 0, solo spostamento verticale sull'asse y = 0; taglio uniforme all'estremo destro oppure flessione pura.

![cook-18.7-beam-with-hole-irregular](images/books/cook-18.7-beam-with-hole-irregular.png)

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| convergenza: sigma_max nodale Q8 640 contro 160 elementi | altro | 120.8 | 121.19 | 0.33 % | ✅ |
| |sigma| sul bordo superiore del foro in flessione pura (fotoelasticita') | libro | 98.4 | 95.183 | 3.27 % | ✅ |

**Note**: Le mesh del libro non sono ricostruibili nodo per nodo. Il carico P = 0,10 e' inteso sulla trave intera (meta' superiore caricata con P/2): con questa lettura i valori ai centri degli elementi delle mesh grossolane hanno l'ordine di quelli del libro (62 con 40 elementi) e la flessione pura con lo stesso momento al foro (M = 0,125) da' 95,2 sul bordo superiore del foro, contro il 98,4 fotoelastico (3 %). Sotto taglio la tensione massima converge a circa 121 (Q8, valore nodale), ben oltre l'estrapolazione lineare 93,3 del libro: il taglio fa lavorare a flessione i due legamenti sopra e sotto il foro (effetto Vierendeel), che le mesh grossolane del libro non colgono.

*Codice*: `validation/books/cook/ch18.py`

<a id="cook-18-8-q4-eigenvalue-test"></a>

### Sez. 18.8, Fig. 18.8-1, Eq. 18.8-1, 18.8-2 - Autovalori della matrice di rigidezza di un Q4 quadrato in deformazione piana

✅ · pag. PDF 584 · plane_strain

Quadrato di lato 1, E = 1, nu = 0,3, spessore 1: il libro elenca i tre autovalori nulli dei moti rigidi e i cinque non nulli 1,92308 (estensione uniforme), 0,76923 (due volte) e 0,57692 (due volte, modi flessionali).

**Modello**: Un PlaneQ4 di feagent (2x2 Gauss), quadrato di lato 1, deformazione piana, non vincolato.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| autovalore 1 (moto rigido) | libro | 0 | -1.1164e-16 | 0.00 % | ✅ |
| autovalore 2 (moto rigido) | libro | 0 | -9.2611e-17 | 0.00 % | ✅ |
| autovalore 3 (moto rigido) | libro | 0 | 1.0871e-16 | 0.00 % | ✅ |
| autovalore 4 | libro | 0.57692 | 0.57692 | 0.00 % | ✅ |
| autovalore 5 | libro | 0.57692 | 0.57692 | 0.00 % | ✅ |
| autovalore 6 | libro | 0.76923 | 0.76923 | 0.00 % | ✅ |
| autovalore 7 | libro | 0.76923 | 0.76923 | 0.00 % | ✅ |
| autovalore 8 | libro | 1.9231 | 1.9231 | 0.00 % | ✅ |

**Note**: Gli autovalori coincidono a tutte le cifre stampate.

*Codice*: `validation/books/cook/ch18.py`

<a id="cook-18-8-single-element-cantilever-test"></a>

### Sez. 18.8, Fig. 18.8-2 - Test del singolo elemento: mensola in flessione pura al variare di L/H

✅ · pag. PDF 585 · plane_stress

Il libro propone di provare una formulazione con un solo elemento a mensola al crescere dell'allungamento L/H. Si misura la rotazione dell'estremo in flessione pura rispetto alla teoria della trave.

**Modello**: Un solo elemento piano feagent (Q4 2x2 e QM6 a modi incompatibili), tensione piana, coppia di forze nodali all'estremo libero, L/H = 1, 2, 5, 10.

| Grandezza | Rif. | Riferimento | feagent | Scarto | |
|---|---|---|---|---|---|
| Q4: rotazione / teoria della trave, L/H = 1 | esatta | 0.67407 | 0.67407 | 0.00 % | ✅ |
| QM6: rotazione / teoria della trave, L/H = 1 | esatta | 1 | 1 | 0.00 % | ✅ |
| Q4: rotazione / teoria della trave, L/H = 2 | esatta | 0.37917 | 0.37917 | 0.00 % | ✅ |
| QM6: rotazione / teoria della trave, L/H = 2 | esatta | 1 | 1 | 0.00 % | ✅ |
| Q4: rotazione / teoria della trave, L/H = 5 | esatta | 0.093333 | 0.093333 | 0.00 % | ✅ |
| QM6: rotazione / teoria della trave, L/H = 5 | esatta | 1 | 1 | 0.00 % | ✅ |
| Q4: rotazione / teoria della trave, L/H = 10 | esatta | 0.025278 | 0.025278 | 0.00 % | ✅ |
| QM6: rotazione / teoria della trave, L/H = 10 | esatta | 1 | 1 | 0.00 % | ✅ |

**Note**: Il libro non stampa numeri: il riferimento e' la forma chiusa del Q4 rettangolare in flessione pura, (1 - nu^2)/(1 + (1 - nu)/2 (L/H)^2), che descrive l'irrigidimento da taglio parassita; il QM6 e' esatto per ogni L/H. Dati liberi: E = 1, nu = 0,3, H = 1.

*Codice*: `validation/books/cook/ch18.py`
