---
layout: default
title: "57 - Simulazione di crollo AEM (Applied Element Method)"
parent: Italiano
nav_order: 57
---

# 57 - Simulazione di crollo AEM (Applied Element Method)

`feagent.aem` e' il solver **Applied Element Method** (Meguro & Tagel-Din,
2000): la struttura e' discretizzata in corpi rigidi collegati da molle che
si fessurano, si snervano e si rompono; i corpi staccati diventano detriti
che urtano fra loro e si depositano sul suolo. Serve per il **crollo
progressivo** (rimozione di un pilastro secondo l'UFC 4-023-03), per la
frattura del calcestruzzo, gli impatti e il blast.

Convenzione degli assi: **Z verticale**, gravita' di default (0, 0, -9.81).

## Modelli

* **Telai** (sole `BeamElement3D`): ogni trave e' divisa in
  `segments_per_beam` corpi rigidi (default 6). Ai giunti uno *spring set*
  (N, V1, V2, T, M1, M2) negli assi della trave, con rigidezza in serie delle
  due semi-lunghezze affacciate (`EA/(l/2)`, `GA_s/(l/2)`, `EI/(l/2)`,
  `GJ/(l/2)`); nei nodi con piu' membrature la stella di semi-molle e'
  sostituita esattamente dalla maglia di molle fra le coppie
  (`C_ij = K_i S^-1 K_j`) e viene ricalcolata dopo una rimozione. I
  vincoli collegano il corpo a terra con la sua semi-molla, sui soli GdL
  bloccati (assi globali). Leggi: elastica-fragile o elasto-plastica
  (`plastic=True`, rottura a `ductility` volte lo snervamento); capacita'
  `fy A`, `fy A_s/sqrt(3)`, `fy W_t/sqrt(3)`, `fy W_pl`. Un giunto che
  perde lo sforzo normale, o i cui punti si separano oltre un quarto del
  segmento, si stacca del tutto (rottura `SEP`) e subentra il contatto.
* **Solidi** (tutti i tipi di feagent: Hex8 anche incompatibile, Tet4,
  Tet10, Wedge6, Pyramid5, anche misti nella stessa mesh): un corpo rigido
  per elemento con **inerzia tensoriale esatta** del poliedro (facce dai
  nodi d'angolo: lati rettilinei); su ogni faccia condivisa una **griglia
  di n x n molle** (`springs_per_side`, default 5) normale + 2 tangenti,
  con rigidezza in serie dei due tratti baricentro-faccia. Nessun fattore di
  taratura: la mensola Hex8 coincide con la trave di Timoshenko entro il
  5 % con la griglia 5 x 5 (1-2 % con 10 x 10).

Le molle lavorano in una terna che coruota con la **rotazione media** dei
due corpi; le rotazioni sono finite e aggiornate in modo moltiplicativo
(`R <- R expm(dt w)`), e il ramo del logaritmo della rotazione relativa e'
seguito con continuita' (una cerniera puo' ruotare oltre 180 gradi).

## Calcestruzzo (`ConcreteProps`)

* trazione: elastica fino a `f_t A_t`, poi softening lineare fino
  all'apertura `w_f = 2 G_f / f_t`: l'energia dissipata vale `G_f A_t`
  qualunque sia la mesh; scarico e ricarico secanti (danno con memoria);
* compressione: rigidezza piena anche a fessura aperta, con `fc`
  elasto-plastica;
* taglio: criterio della tensione principale (`sigma_1 >= f_t`, Tagel-Din
  & Meguro) e poi attrito `mu` (default 0.5); una molla senza coesione che
  scorre oltre la propria cella si separa.

Il softening deve essere risolto nel tempo: se l'apertura relativa per passo
supera `w_f` la molla passa dall'elasticita' alla rottura in un passo, non
dissipa la sua energia di frattura e il solver lo segnala con un avviso.

## Carichi, masse, scenari

Peso proprio, carichi nodali (anche per caso, `cases=`), carichi
**distribuiti** (anche trapezoidali e parziali, assi locali o globali) e
**concentrati** sulle travi, applicati ai segmenti nel baricentro del
carico, forze di volume e pressioni su facce esposte (`face_pressure`), **blast** a storia temporale
(`BlastLoad`, forma di Friedlander; sulle teste dei telai serve l'area
esposta), **impatti** (`initial_velocity` con 3 o 6 componenti). I carichi
nodali concordi con la gravita' portano la loro **massa** `F/g`
(`load_mass=True`, default): senza, il carico accelererebbe la sola massa
strutturale e i detriti cadrebbero a piu' g. Il peso proprio scritto come
carico del modello (`Model.add_self_weight`) sostituisce quello interno e
non aggiunge massa (c'e' gia' quella da rho). `ColumnRemoval(element, t)`
toglie l'elemento all'istante `t`; il carico dei nodi che portava resta sugli
altri corpi del nodo.

## Integrazione, smorzamento, contatti

Differenze centrali con passo stabile automatico (`0.9 * 2 / omega_max`,
autovalore massimo della rigidezza sparsa; un `dt` oltre `2/omega_max` e'
rifiutato). Lo **smorzamento** proporzionale alla massa (`damping`, xi su
`omega_1`) agisce sul moto **relativo** al moto rigido di ogni gruppo di
corpi collegati da molle integre: smorza le vibrazioni ma non frena la
caduta dei detriti ne' la rotazione attorno a una cerniera fessurata.

Contatti a penalita' con smorzatore normale (rapporto 0.7) e attrito di
Coulomb: **suolo** `Ground(z=..., friction=...)` sui vertici, con molla
tangenziale di aderenza; **fra corpi** (`contact=True`) con normale di
coppia dall'asse separatore (SAT) e punti di contatto su vertici e griglia
3 x 3 di ogni faccia (blocchi impilati allineati e travi incrociate si
appoggiano correttamente), travi come capsule. La rigidezza di contatto
`contact_stiffness * E * h` e' limitata perche' non riduca il passo stabile:
la compenetrazione sotto il peso resta di micrometri.

## Bilancio energetico

```
W_ext + E_0 = E_cin + E_el + E_diss + E_fract + E_removed
              + E_contact + W_friction + W_contact   (+ residuo)
```

`E_fract` e' la dissipazione delle molle (frattura, plasticita', attrito
delle fessure) misurata come lavoro locale di ogni molla meno l'energia
immagazzinata; il residuo confronta questa misura con il lavoro sui corpi e
non e' nullo per costruzione. Nei casi di prova resta fra 1e-5 e 1e-2, anche
nei crolli con urti al suolo.

```python
from feagent import Material, Model, Section
from feagent.aem import AEMModel, ColumnRemoval, Ground, solve_collapse

m = Model()                                  # telaio nel piano XZ, basi fisse
# ... nodi, travi, m.fix(...), m.add_nodal_load(n, Fz=-15e3) ...
am = AEMModel.from_model(m, segments_per_beam=6, fy=275e6)
res = solve_collapse(am, scenario=ColumnRemoval(element=3, t=0.8),
                     t_end=3.0, damping=0.05, ramp=0.5,
                     ground=Ground(z=0.0, friction=0.5))
res.energy_balance()                 # voci del bilancio e residuo
res.failed_springs()                 # timeline delle rotture
res.end_force(am.source_map[1][-1], 1)   # forza in testa al pilastro 1
res.to_vtk("crollo/telaio", stride=2)    # serie ParaView (.vtu + .pvd)
```

## Nella GUI di feagent

*Analisi > Crollo progressivo (AEM)...* su un modello di sole travi o di
soli solidi (foglio **Solid**: tipo, nodi, materiale; carichi nei fogli
SolidBodyForce, SolidFaceLoad, SolidThermal). La finestra chiede l'elemento
da rimuovere e l'istante, durata, smorzamento, casi, suolo e attrito,
contatto fra detriti e i parametri del materiale (fy, fattore sulle
capacita', segmenti per i telai; ft, Gf, fc, attrito delle fessure, molle
per lato per i solidi). Il risultato si anima con il pulsante play e il
cursore del tempo: posizioni reali (scala 1), corpi con molle rotte in
rosso, corpi rimossi nascosti, suolo; con la **vista estrusa** le travi
sono disegnate con la loro sezione orientata come il corpo rigido.
*Risultati* offre lo stato finale, le energie nel tempo e la timeline delle
rotture. I solidi sono gestiti anche dalle analisi ordinarie: disegno delle
facce di bordo, deformata, tensioni di von Mises, principali e componenti
mediate ai nodi.

## Accuratezza e validazione

Telai: le molle concentrate ai giunti integrano la curvatura con la regola
dei trapezi, errore sulle frecce che decresce come 1/n^2:

| schema | n = 2 | n = 4 | n = 8 |
|---|---|---|---|
| mensola, carico in punta | +12 % | +3,1 % | +0,8 % |
| trave appoggiata, mezzeria | +13 % | +3,3 % | +0,9 % |
| trave incastrata, mezzeria | +50 % | +13 % | +3,7 % |
| portale, carico sulla trave | +43 % | +11 % | +3,4 % |

Gli sforzi governati dall'equilibrio non dipendono da n.

Solidi: le molle fra i baricentri affacciati sono esatte per celle regolari;
con facce inclinate la rigidezza si scosta. Mensola 2 x 0,25 x 0,25 m
contro la trave di Timoshenko: esaedri 8 x 1 x 1 con griglia 5 x 5 +5 %,
esaedri divisi in 6 piramidi +4 %, esaedri divisi in 2 cunei lungo la
diagonale -21 %.

`validation/aem/validation_aem.py`: rimozione del pilastro centrale di un
telaio 2 x 2 (UFC 4-023-03) confrontata con i solutori FEM di feagent
(statica del telaio intatto e danneggiato, time-history di Newmark del
telaio danneggiato con la stessa massa):

| grandezza | FEM | AEM | scarto |
|---|---|---|---|
| N pilastro laterale prima [kN] | 30,00 | 30,00 | 0,0 % |
| N pilastro laterale dopo [kN] | 45,00 | 44,36 | 1,4 % |
| picco di N [kN] | 63,58 | 63,71 | 0,2 % |
| spostamento finale del nodo [mm] | 12,29 | 12,22 | 0,5 % |
| picco dello spostamento [mm] | 24,50 | 25,44 | 3,8 % |

Il fattore di amplificazione dinamica dello spostamento vale 2,00 (FEM) e
2,08 (AEM), contro il valore teorico 2 della rimozione istantanea non
smorzata.

## Limiti noti

* modelli misti travi + solidi non supportati;
* mesh di solidi irregolari (cunei sulla diagonale, tetraedri) meno precise
  degli esaedri regolari; elementi piccoli riducono il passo stabile (il
  crollo di 16 solidi misti con contatto richiede circa 2 minuti);
* il contatto fra solidi rileva vertici e punti di faccia, non gli spigoli
  esatti; fra travi le capsule hanno il raggio della sezione equivalente;
* lo sforzo assiale di un estremo libero caricato ha un errore 1/(2n) (meta'
  segmento rigido oltre l'ultima molla);
* nelle fasi molto violente (aperture per passo dell'ordine di `w_f`) la
  dissipazione per frattura e' sottostimata, con avviso.

Esempi: `usage_examples/41_aem_progressive_collapse.py` (telai),
`42_aem_solid_fracture.py` (mensola in c.a.), `43_aem_debris_impact.py`
(impilamento, travi incrociate, impatto, blast), `44_aem_vtk_export.py`.
