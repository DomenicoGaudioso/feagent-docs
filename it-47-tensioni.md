---
layout: default
title: "47 - Tensioni in travi e gusci, sezioni composte per fasi"
parent: Italiano
nav_order: 47
---

# 47 - Tensioni in travi e gusci, sezioni composte per fasi

feagent calcola le tensioni normali nelle travi fibra per fibra e le tensioni dei
gusci, e le mostra nell'interfaccia lungo le travi, sulla vista estrusa e nella
sezione. Nelle sezioni composte acciaio-calcestruzzo ogni fase ha il proprio
coefficiente di omogeneizzazione `n`: la stessa fibra riceve da ogni fase una
tensione diversa, e le tensioni si sommano fase per fase (EN 1994-2 5.4.2.2).

## Sezioni composte: parti e fasi

Una sezione si descrive con le sue **parti** (foglio `SectionPart`):

| Colonna | Significato |
|---|---|
| Section | sezione composta (stesso nome del foglio Section) |
| Part | nome della parte: Soletta, Piattabanda inf, Anima... |
| Kind | `steel` acciaio, `concrete` calcestruzzo (diviso per n), `rebar` barre |
| Y, Z, h, b | rettangolo: centro (y, z) e dimensioni (h lungo y, b lungo z) |
| Points | poligono al posto del rettangolo: `y:z; y:z; ...` |
| Area | barra puntiforme nel punto (Y, Z) |
| Er | E / E dell'acciaio per acciaio e barre (vuoto = 1) |

La y locale e' l'altezza della sezione. Con Z verticale (travi nel piano X-Y,
carichi lungo Z) va impostato il vettore di riferimento `RefX, RefY, RefZ = 0, 0, 1`.
Nel foglio Section, per una sezione composta, A, Iy e Iz restano vuoti: si
calcolano dalle parti. J e le aree di taglio si prendono dal foglio (J vuoto:
stima delle lamiere d'acciaio).

Le **fasi** stanno nel foglio `StressPhase`:

| Colonna | Significato |
|---|---|
| Phase | nome della fase |
| Group | gruppo di sezioni creato per la fase (vuoto = sezione di default) |
| n | coefficiente Ea/Ec,eff; vuoto = il calcestruzzo non collabora (getto fresco) |
| n0, phi, psiL | in alternativa a n: `n = n0 (1 + psiL phi)` (psiL 1,1 permanenti, 0,55 ritiro, 1,5 deformazioni impresse) |
| Cracked | sezione fessurata: calcestruzzo escluso, barre attive |
| Cases | casi di carico della fase, es. `G2,R` |

Sei tu a dire a quale fase appartiene ogni caso (colonna Cases): `G1` nella fase
del getto, `G2` e il ritiro a lungo termine, il traffico a breve termine. Ogni
caso si risolve con la sezione della propria fase; le combinazioni si calcolano
**per sovrapposizione delle fasi** (ogni caso con la sua rigidezza, poi la somma
pesata). La riga con Group vuoto da' la sezione di default; se manca si usa la
fase con l'n piu' piccolo (breve termine).

L'esempio `composite_girder` del catalogo (`feagent examples composite_girder`)
e' una trave di 30 m con le tre fasi; i suoi controlli confrontano le tensioni con
il calcolo a mano sezione per sezione (Steiner), con scarto sotto 1e-12.

## Dalla libreria

```python
from feagent.composite import CompositeSection, Part, Phase, rect, n_long_term

parti = [Part("Piattabanda inf", "steel", rect(0.015, 0, 0.03, 0.6)),
         Part("Anima", "steel", rect(0.63, 0, 1.2, 0.012)),
         Part("Piattabanda sup", "steel", rect(1.24, 0, 0.02, 0.4)),
         Part("Soletta", "concrete", rect(1.375, 0, 0.25, 3.0)),
         Part("Barre", "rebar", area=0.0075, y=1.45, z=0.0)]
cs = CompositeSection(parti, name="TRAVE")
n0 = 210 / 34
f1, f2, f3 = Phase(None, name="F1"), Phase(n_long_term(n0, 2.0), name="F2"), Phase(n0, name="F3")

m.add_section_group("F1", cs.section(f1)); m.link_section_to_cases("F1", "G1")
m.add_section_group("F2", cs.section(f2)); m.link_section_to_cases("F2", "G2")
m.add_section_group("F3", cs.section(f3)); m.link_section_to_cases("F3", "Q")

res = m.solve_staged({"SLE rara": {"G1": 1, "G2": 1, "Q": 1}})["SLE rara"]
d = res.beam_stresses(6, n=21)
d["sigma"]      # (ascisse, fibre), somma delle fasi
d["phases"]     # contributo di ogni fase, stessa forma
d["labels"]     # "Piattabanda inf inf sx", "Soletta sup sx", ...
```

`CompositeSection.properties(fase)` restituisce area, baricentro e inerzie
omogeneizzate all'acciaio; `stresses(N, My, Mz, fase)` la tensione nelle fibre
con la formula di Navier generalizzata (anche con `Iyz` diverso da zero).
Le sezioni di forma (`Shape` rect, I, box, circle, tube) hanno i punti di recupero
automatici; per i gusci `Result.shell_element_stresses(id)` da' sigma x, sigma y,
tau xy, principali e von Mises sulle facce superiore, media e inferiore.

## Nell'interfaccia

- **Risultati > σ max travi / σ min travi**: diagramma lungo le travi della fibra
  piu' tesa o piu' compressa, con il nome della fibra in legenda;
  **Tensione in una fibra** per una fibra scelta (Soletta sup, Piattabanda inf...).
- Con la **vista estrusa** (tasto E) la trave si disegna con la sezione vera,
  colorata punto per punto con la tensione, sulle facce e sulle testate.
- **Tensioni nella sezione…** (menu Risultati o menu contestuale di una trave):
  sezione in scala con i baricentri di ogni fase, diagramma sigma(y) con il salto
  all'interfaccia acciaio-calcestruzzo, contributi delle fasi, cursore lungo la
  trave e tabella fibra per fibra.
- **Gusci**: mappe di σ von Mises, σ1, σ2, σx, σy, τxy sulle facce superiore e
  inferiore.
- Le mappe di colore hanno una **legenda a bande uguali** con i valori a ogni confine.
- L'unita' delle tensioni si sceglie a parte nella barra di stato (MPa di default).

## Limiti attuali

- Gli effetti primari del ritiro e della temperatura nella sezione composta
  (tensioni autoequilibrate) non sono ancora inclusi: si sommano solo le tensioni
  dovute alle sollecitazioni del modello.
- Le travi a sezione variabile usano le proprieta' interpolate e i punti della
  sezione, senza fasi.
- Il dettaglio per sezione e' disponibile per le analisi statiche lineari.
