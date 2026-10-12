---
layout: default
title: "58 - Membrane, pannelli a taglio ed elementi piani"
parent: Italiano
nav_order: 58
---

# 58 - Membrane, pannelli a taglio ed elementi piani

Oltre al guscio completo (membrana piu' flessione di Mindlin), feagent ha i
tipi di piastra dei codici commerciali che lavorano **solo nel proprio piano**:

| Tipo | Come si crea | Rigidezza | Uso tipico |
|---|---|---|---|
| Membrana 3D | `add_shell(..., formulation="membrane")`, `add_shell_tri(..., formulation="membrane")` | tensione piana completa, in un piano qualsiasi dello spazio | teli, lamiere tese, anime e piattabande schematizzate senza flessione |
| Pannello a taglio | `add_shell(..., formulation="shear_panel")` (solo 4 nodi) | solo flusso di taglio $N_{xy} = G\,t\,\gamma_{xy}$ | anime e diaframmi sottili irrigiditi da correnti e montanti a trave |

Nel foglio Excel `Shell` si usa la colonna `Formulation` (`membrane`,
`shear_panel`; per i triangoli restano anche `cst` e `thin`).

## Membrana 3D

Stessa geometria, stessa terna locale e stessa membrana del guscio Q4 (Gauss
2x2) e T3 (deformazione costante), senza flessione e senza rigidezza di
drilling:

$$
\mathbf{N} = \mathbf{C}\,(\boldsymbol{\varepsilon} - \boldsymbol{\varepsilon}_0),
\qquad
\mathbf{C} = \frac{E\,t}{1-\nu^2}
\begin{bmatrix} 1 & \nu & 0 \\ \nu & 1 & 0 \\ 0 & 0 & \tfrac{1-\nu}{2} \end{bmatrix}
$$

Il carico termico agisce con la sola variazione uniforme $\Delta T$ (il
gradiente nello spessore non ha effetto). I risultati (`Result.shell_forces`)
hanno $N_x$, $N_y$, $N_{xy}$; momenti e tagli sono nulli.

## Pannello a taglio

Il pannello porta il solo flusso di taglio, con deformazione a taglio costante
(un punto di integrazione): e' il modello classico dei pannelli sottili
irrigiditi (Kuhn, *Stresses in aircraft and shell structures*, 1956;
Przemieniecki, *Theory of matrix structural analysis*, 1968, cap. 5):

$$
N_{xy} = G\,t\,\gamma_{xy}, \qquad N_x = N_y = 0
$$

Il taglio e' riferito agli assi dei bordi (x lungo il lato 1-2): per questo il
pannello esiste solo a 4 nodi. In un triangolo l'asse locale cadrebbe sulla
diagonale e il taglio non avrebbe significato fisico. Il pannello non ha
rigidezza per le deformazioni normali e il carico termico e' nullo: va sempre
irrigidito da travi o bielle sui bordi, altrimenti il modello e' labile.

Verifica analitica: pannello quadrato con bordi a biella rigidi, fisso alla
base e caricato in sommita' da $V$: lo spostamento e' $\delta = V/(G\,t)$ e
$N_{xy} = V/a$ (test `tests/test_membrane_shear_panel.py`).

## Gradi di liberta'

Membrane e pannelli usano le sole traslazioni dei nodi. Le rotazioni dei nodi
collegati soltanto a questi elementi (o a solidi) non hanno rigidezza e il
solutore le blocca in automatico; un momento nodale applicato a uno di questi
nodi da' errore, perche' andrebbe perso. Fuori dal proprio piano una membrana
piana non ha rigidezza: va vincolata, irrigidita da travi o inserita in una
mesh chiusa.

```python
from feagent import Material, Model
from feagent.shell import ShellSection

m = Model()
...
m.add_shell(1, [1, 2, 3, 4], Material(210e9, 0.3, 1.2e-5), ShellSection(t=0.012),
            formulation="shear_panel")       # anima del traverso fra correnti a trave
```

## Elementi piani 2D

`add_plane(id, nodi, materiale, formulation, t, kind, incompatible)` crea un
elemento nel piano globale **X-Y** (tutti i nodi alla stessa quota Z). Nel
foglio Excel `Plane`: `Plane`, `Nodes`, `Material`, `Type`, `Formulation`, `t`,
`Incompatible`.

| Tipo | Nodi | Integrazione | Note |
|---|---|---|---|
| T3 | 3 | 1 punto | deformazione costante |
| T6 | 6 (angoli, poi spigoli 1-2, 2-3, 3-1) | 3 punti | quadratico |
| Q4 | 4 | Gauss 2x2 | con `incompatible=True` modi incompatibili di Wilson-Taylor (QM6): niente irrigidimento a taglio in flessione |
| Q8 | 8 (angoli, poi spigoli 1-2, 2-3, 3-4, 4-1) | Gauss 3x3 | serendipity, lati anche curvi |

| `formulation` | Ipotesi | Uso | $\sigma_{zz}$ |
|---|---|---|---|
| `stress` | tensione piana, spessore `t` | lastre sottili caricate nel piano, anime, pareti | 0 |
| `strain` | deformazione piana ($\varepsilon_{zz} = 0$), spessore `t` (di solito 1) | sezioni di opere lunghe: muri, gallerie, dighe, rilevati | $\nu(\sigma_{xx} + \sigma_{yy}) - E\alpha\Delta T$ |
| `axisym` | assialsimmetria: X = raggio $r \ge 0$, Y = asse | serbatoi, silos, pali, piastre e fondazioni circolari | tensione circonferenziale $\sigma_\theta$ |

In assialsimmetria la deformazione circonferenziale e' $\varepsilon_\theta = u_r/r$
e volumi, masse e carichi si riferiscono all'**anello completo** ($2\pi r$): una
forza nodale e' la risultante sull'intera circonferenza.

Gli elementi piani stanno con i solidi (id condivisi): forze di volume
(`add_solid_body_force`, la componente Z e' ignorata), carichi sui lati
(`add_solid_pressure` e `add_solid_surface_load` con `face` = lato, il lato k
va dal nodo d'angolo k+1 al successivo), termici (`add_solid_thermal`), masse e
tensioni (`Result.solid_stresses`, `Result.solid_nodal_stresses`, vettore
$[\sigma_{xx}, \sigma_{yy}, \sigma_{zz}, \tau_{xy}, 0, 0]$ con von Mises e principali
del tensore completo) sono gli stessi. `uz` e le rotazioni dei nodi collegati
solo a elementi piani sono bloccati in automatico; una forza Fz su questi nodi
da' errore.

Nella GUI i comandi stanno in **Modello > Piastre**. Lo strumento **Q**
(Disegna elementi piani) funziona come quello dei gusci: si cliccano 3 o 4
angoli nel piano X-Y. Con l'ordine quadratico i nodi a meta' dei lati si creano
da soli e si condividono con gli elementi vicini, cosi' nascono T6 e Q8. Ci sono
anche il generatore di mesh piana, l'elemento dai nodi selezionati e la
pressione sui lati dei nodi selezionati. Un modello di soli elementi piani si
apre con Y verticale e non viene proposta la conversione a Z verticale, che
porterebbe gli elementi fuori piano.

Verifiche analitiche (`tests/test_plane.py`):

* patch test di MacNeal-Harder su mesh distorta, per tutti i tipi e per
  tensione e deformazione piana: tensione costante esatta;
* mensola in tensione piana (L/H = 10): freccia di Timoshenko entro il 2 % con
  Q8 e Q4 incompatibile, mentre il Q4 standard resta troppo rigido;
* cilindro spesso in pressione interna (Lame'), in deformazione piana (quarto
  di anello in Q8) e in assialsimmetria: spostamento radiale entro lo 0,2 %,
  tensione circonferenziale al raggio interno entro il 2 %;
* dilatazione termica libera (tensioni nulle) e impedita fuori piano
  ($\sigma_{zz} = -E\alpha\Delta T$);
* masse $\rho\,t\,A$ e $2\pi\,r_G\,A\,\rho$; primo modo di una mensola entro il 3 %
  dalla trave di Eulero.

```python
from feagent import Material, Model

m = Model()
...
m.add_plane(1, [1, 2, 3, 4, 5, 6, 7, 8], Material(30e9, 0.2, 1e-5), formulation="axisym")
m.add_solid_pressure(1, 3, 2.0e5)          # pressione sul lato 4-1
res = m.solve()
s = res.solid_stresses(1)                   # s["szz"] = tensione circonferenziale
```
