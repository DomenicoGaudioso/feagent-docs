---
layout: default
title: "59 - Modelli surrogati e modelli ridotti"
parent: Italiano
nav_order: 59
---

# 59 - Modelli surrogati e modelli ridotti

feagent costruisce due tipi di modello "leggero" a partire da un modello
completo, dichiarando **a quali analisi e a quali risultati devono fare fede**:

| | Modello surrogato (`feagent.surrogate`) | Modello ridotto (`feagent.reduction`) |
|---|---|---|
| Che cosa riproduce | alcuni risultati scalari al variare di alcuni parametri | rigidezza e massa del modello su pochi GdL |
| Come | campioni di analisi feagent e regressione | algebra delle matrici (Guyan, Craig-Bampton) |
| Uso tipico | studi parametrici, ottimizzazione, incertezza, sensitivita' | sottostrutture (pile, fondazioni, impalcati), dinamica rapida |
| Fedelta' | tolleranza per obiettivo, verificata in validazione incrociata | esatta in statica (Guyan); frequenze entro tolleranza (Craig-Bampton) |

## Modello surrogato

### Parametri

Un parametro e' una grandezza del modello che varia in un intervallo
(`Parameter(name, low, high, path=..., mode="value"|"scale", log=False)`):

| `path` | Grandezza |
|---|---|
| `material:NOME.E` (anche `nu`, `alpha`, `rho`, `gamma`, `G`) | materiale, per nome; con E varia anche G (nu costante) |
| `section:NOME.Iy` (anche `A`, `Iz`, `J`, `Asy`, `Asz`, `mass_per_length`) | sezione delle travi |
| `shell_section:NOME.t` | spessore dei gusci |
| `plane:ID.t` | spessore di un elemento piano |
| `spring:ID.kx` (anche `k`, `ky` ... `crz`) | molla o molla-smorzatore |
| `elastic_support:NODO.uz` | vincolo elastico a terra |
| `nodal_mass:NODO.mx` (anche `my`, `mz`, `m`, `Ix` ...) | massa nodale |
| `node:ID.z` | coordinata di un nodo |
| `load_case:CASO` | coefficiente del caso nelle analisi |

`mode="scale"` interpreta il campione come fattore del valore del modello di
partenza; `log=True` campiona in scala logaritmica (rigidezze che variano di
ordini di grandezza). In Python `apply=funzione(model, valore)` permette
qualunque modifica.

### Obiettivi

Un obiettivo e' un'analisi piu' un risultato, con la sua tolleranza
(`Target(name, analysis, quantity, ..., tol=0.02)`):

| `analysis` | `quantity` | Indici |
|---|---|---|
| `{"type": "static", "cases": "G=1.35 Q=1.5"}` | `displacement`, `reaction` | `node`, `dof` |
| | `element_force` | `elem`, `comp` (N, Vy, Vz, T, My, Mz), `x` (0..1 oppure `maxabs`) |
| | `spring_force`, `spring_local` | `elem` (id della molla), `comp` (Fx ... Mz) |
| | `shell_force`, `solid_stress` | `elem`, `comp` |
| | `max_displacement` | `dof` |
| `{"type": "modal", "n_modes": 6, "mass_source": "G=1"}` | `frequency`, `period` | `mode` (1 = primo) |
| `{"type": "buckling", "n_modes": 4, "cases": ...}` | `load_factor` | `mode` |

Ogni analisi distinta si esegue una sola volta per campione, anche se serve a
piu' obiettivi.

### Algoritmo

1. Piano iniziale di campioni (ipercubo latino, Sobol o Halton,
   `scipy.stats.qmc`), di default $\max(8, (d+1)(d+2))$ con $d$ parametri.
2. Per ogni campione: copia del modello, parametri applicati, analisi, lettura
   dei risultati (anche in parallelo con `n_jobs`).
3. Per ogni obiettivo si prova una serie di regressori e si sceglie quello
   con l'errore minore in **validazione incrociata** (leave-one-out fino a 40
   campioni, poi k-fold):
   * superficie di risposta polinomiale di grado 2 o 3;
   * funzioni a base radiale (`scipy.interpolate.RBFInterpolator`);
   * kriging con nucleo gaussiano anisotropo e iperparametri a massima
     verosimiglianza (scritto con numpy e scipy, senza dipendenze nuove);
   * **legge di potenza** $y = c\,x_1^{a_1} x_2^{a_2}\cdots$ (logaritmo di ingressi e
     uscita), esatta per frecce, frequenze e carichi critici in funzione di
     moduli, inerzie, spessori e carichi;
   * le stesse forme sul logaritmo dell'uscita quando ha segno costante.
4. Errore dell'obiettivo: $\max_i |y_i^{cv} - y_i| / (\max y - \min y)$. Se
   supera la tolleranza si aggiungono campioni lontani da quelli gia' calcolati
   (maximin), pesati con l'incertezza del kriging sull'obiettivo peggiore.
5. Ci si ferma quando tutti gli obiettivi fanno fede o al tetto `max_samples`;
   gli obiettivi che non convergono sono dichiarati (avviso
   `SURROGATO_NON_CONVERGE`).

`Surrogate.validate(model, n)` confronta il surrogato con feagent su campioni
nuovi; `Surrogate.sensitivity()` calcola gli indici di Sobol del primo ordine e
totali (stimatori di Saltelli e Jansen) direttamente sul surrogato; una
previsione fuori dagli intervalli emette `SURROGATO_FUORI_DOMINIO`.

```python
from feagent.surrogate import Parameter, Target, build_surrogate

s = build_surrogate(
    model,
    parameters=[Parameter("E", 25e9, 40e9, path="material:CLS.E"),
                Parameter("k_app", 1e7, 1e9, path="spring:10.kx", log=True),
                Parameter("fQ", 0.5, 1.5, path="load_case:Q")],
    targets=[Target("freccia", {"type": "static", "cases": "G=1 Q=1"}, "displacement",
                    node=12, dof="uz", tol=0.01),
             Target("M_pila", {"type": "static", "cases": "G=1 Q=1"}, "element_force",
                    elem=40, comp="My", x=0.0, tol=0.01),
             Target("f1", {"type": "modal", "n_modes": 3, "mass_source": "G=1"}, "frequency",
                    mode=1, tol=0.005)])
print(s.summary())
s.predict({"E": 33e9, "k_app": 2e8, "fQ": 1.2})
s.save("surrogato.json")          # Surrogate.load("surrogato.json")
s.to_excel("surrogato.xlsx")      # fedelta', parametri, obiettivi, campioni, verifica
```

Una risposta con uno spigolo non si approssima con una funzione liscia, ed e'
giusto che il surrogato lo dica. Un esempio e' il primo modo che passa da una
direzione all'altra al variare delle inerzie. In questi casi si separano gli
obiettivi (frequenza per direzione) o si restringono gli intervalli.

### Da Excel, da riga di comando e dalla GUI

Nello stesso file del modello si aggiungono i fogli `SurrogateParameter`
(`Name, Path, Low, High, Mode, Log`), `SurrogateTarget` (`Name, Analysis,
Quantity, Cases, MassCases, Modes, Node, Dof, Elem, Comp, X, Mode, Tol`) e,
facoltativo, `SurrogateSettings` (`Key, Value`: `method`, `n_initial`,
`max_samples`, `batch`, `sampling`, `seed`, `jobs`, `validate`).

```bash
feagent surrogate build modello.xlsx -o surrogato.json --report surrogato.xlsx --jobs 4
```

```bash
feagent surrogate predict surrogato.json E=33e9 k_app=2e8 fQ=1.2
```

In alternativa ai fogli, `--spec spec.json` con `parameters`, `targets` e
`settings`. Nella GUI: **Analisi > Modello surrogato** apre le tre tabelle,
costruisce il surrogato (tabelle *Fedelta' del surrogato*, *Verifica* sui
campioni nuovi e *Sensitivita'* con gli indici di Sobol totali), fa previsioni
istantanee e salva il file `.json`.

## Modello ridotto

### Guyan

`guyan(model, masters, cases=None, mass_source=None)` condensa il modello sui
GdL master (nodi interi o coppie `(nodo, gdl)`):

$$
\mathbf{u} = \mathbf{T}\,\mathbf{u}_m, \quad
\mathbf{T} = \begin{bmatrix} \mathbf{I} \\ -\mathbf{K}_{ss}^{-1}\mathbf{K}_{sm} \end{bmatrix}, \quad
\mathbf{K}_r = \mathbf{T}^{\mathsf T}\mathbf{K}\,\mathbf{T}, \quad
\mathbf{M}_r = \mathbf{T}^{\mathsf T}\mathbf{M}\,\mathbf{T}
$$

In statica e' **esatta**: i carichi interni si riducono ai master e il loro
effetto locale si recupera con `ReducedModel.expand`. In dinamica e'
approssimata e migliora con il numero di master (Guyan 1965).

### Craig-Bampton

`craig_bampton(model, boundary, n_modes=10, mass_source=None)` aggiunge alle
forme statiche i primi modi a interfaccia bloccata (Craig e Bampton 1968).
Con `n_modes="auto"`, `f_max` e `tol` i modi interni crescono finche' tutte le
frequenze fino a `f_max` sono entro `tol` da quelle del modello completo.

### Fedelta' e superelementi

`ReducedModel.check(cases, n_modes)` confronta statica (spostamenti dei
master) e frequenze con il modello completo. Il modello ridotto si reinserisce
in un altro modello come **superelemento** (sottostrutturazione):

```python
from feagent.reduction import craig_bampton, guyan

pila = ...                                      # modello della pila con fondazione
red = guyan(pila, masters=[100])                 # nodo di testa pila
red.check()                                      # {'static': {...}} errore 0 in statica
ponte.add_superelement(1, **red.superelement_data())
red.save("pila_ridotta.npz")                     # load_superelement("pila_ridotta.npz")
```

I modi interni di Craig-Bampton diventano nodi ausiliari del modello ospite. La
massa di un superelemento e' una matrice piena: modale e dinamica passano in
automatico alla massa consistente (avviso `SUPERELEMENTO_MASSA`).

```bash
feagent reduce modello.xlsx --masters 5,9 --method craig-bampton --modes auto --fmax 20 --mass-cases G=1
```

Limiti attuali: la riduzione e' lineare (niente cavi ne' molle unilatere) e
non ammette vincoli cinematici ne' cedimenti non nulli nel modello da ridurre;
i superelementi non si salvano nel file Excel del modello ospite. La riduzione
si usa da Python e da riga di comando, non dalla GUI.

Verifiche (`tests/test_surrogate.py`, `tests/test_reduction.py`):

* mensola $w = P L^3/(3EI)$ con E, I e coefficiente del carico come parametri,
  riprodotta dalla legge di potenza con errore $10^{-7}$ e verificata su
  campioni nuovi;
* oscillatore $f = \sqrt{k/m}/(2\pi)$ entro lo 0,5 %;
* indici di Sobol: il coefficiente di Poisson non influenza la freccia di
  Eulero;
* Guyan esatto in statica con carichi interni, sottostruttura reinserita come
  superelemento uguale al modello intero, Craig-Bampton reinserito con le
  frequenze entro lo 0,2 %.
