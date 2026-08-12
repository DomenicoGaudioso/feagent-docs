---
layout: default
title: "21 - Assemblaggio della Rigidezza"
parent: Italiano
nav_order: 21
---

# 21 - Assemblaggio della matrice di rigidezza

Questa pagina spiega come beamfeapy costruisce la matrice di rigidezza, dal singolo
elemento fino al sistema globale, e come applica i vincoli per risolvere.

Il percorso completo è:

```
k_local (12×12)  ──T──►  k_global (12×12)  ──scatter──►  K (ndof×ndof)  ──vincoli──►  K_ff u_f = F_f
```

Riferimenti nel codice: `BeamElement3D.stiffness_local/_global`,
`Model.assemble_stiffness`, `Model.assemble_stiffness_sparse`, `Model.solve`.

---

## 1. Rigidezza locale dell'elemento (12×12)

La matrice di rigidezza locale deriva dall'integrale $\mathbf{k}^e = \int_0^L
\mathbf{B}^T \mathbf{D}\, \mathbf{B}\, dx$ (vedi
[20 - Funzioni di forma](it-20-shape-functions.html)). Per l'elemento prismatico
ha **forma chiusa** e i quattro comportamenti restano disaccoppiati.

### Termine assiale ($EA$)

$$
\mathbf{k}_{\text{ax}} = \frac{EA}{L}
\begin{bmatrix} 1 & -1 \\ -1 & 1 \end{bmatrix}
\qquad \text{(GdL } u_x^i, u_x^j\text{)}
$$

```python
ea = E * A / L
k[0, 0] = k[6, 6] = ea
k[0, 6] = k[6, 0] = -ea
```

### Termine torsionale ($GJ$)

$$
\mathbf{k}_{\text{tor}} = \frac{GJ}{L}
\begin{bmatrix} 1 & -1 \\ -1 & 1 \end{bmatrix}
\qquad \text{(GdL } \theta_x^i, \theta_x^j\text{)}
$$

```python
gj = G * J / L
k[3, 3] = k[9, 9] = gj
k[3, 9] = k[9, 3] = -gj
```

### Termini flessionali ($EI_z$ e $EI_y$)

Per la flessione nel piano x-y (GdL $u_y^i, \theta_z^i, u_y^j, \theta_z^j$ →
$EI_z$), il blocco classico della trave è:

$$
\mathbf{k}_{\text{flex},z} = \frac{EI_z}{L^3}
\begin{bmatrix}
12 & 6L & -12 & 6L \\
6L & 4L^2 & -6L & 2L^2 \\
-12 & -6L & 12 & -6L \\
6L & 2L^2 & -6L & 4L^2
\end{bmatrix}
$$

La libreria scrive i coefficienti in forma **unificata Eulero / Timoshenko**,
introducendo il parametro di taglio $\Phi$ (nullo per Eulero-Bernoulli):

$$
a = \frac{12 EI_z}{L^3(1+\Phi_y)},\quad
b = \frac{6 EI_z}{L^2(1+\Phi_y)},\quad
c = \frac{(4+\Phi_y) EI_z}{L(1+\Phi_y)},\quad
d = \frac{(2-\Phi_y) EI_z}{L(1+\Phi_y)}
$$

```python
den = 1.0 + phiy
a = 12 * E * Iz / (L**3 * den)
b = 6  * E * Iz / (L**2 * den)
c = (4 + phiy) * E * Iz / (L * den)
d = (2 - phiy) * E * Iz / (L * den)
k[1, 1] = k[7, 7] = a
k[1, 7] = k[7, 1] = -a
k[1, 5] = k[5, 1] = b
k[1, 11] = k[11, 1] = b
k[5, 7] = k[7, 5] = -b
k[7, 11] = k[11, 7] = -b
k[5, 5] = k[11, 11] = c
k[5, 11] = k[11, 5] = d
```

Per Eulero-Bernoulli $\Phi_y = 0$ e si ritrova il blocco classico. Per Timoshenko
$\Phi_y = \dfrac{12 EI_z}{G A_{sy} L^2}$ introduce la deformabilità a taglio.

> Il blocco per il piano x-z ($EI_y$, GdL $u_z, \theta_y$) è analogo, ma con
> **segni invertiti** sui termini misti per via della convenzione
> $\theta_y = -du_z/dx$.

### Matrice locale completa 12×12 (forma simbolica)

Assemblando i quattro contributi nell'ordine dei GdL
$[\,u_x^i, u_y^i, u_z^i, \theta_x^i, \theta_y^i, \theta_z^i,\ u_x^j, u_y^j, u_z^j, \theta_x^j, \theta_y^j, \theta_z^j\,]$
si ottiene la matrice esplicita del singolo elemento. Per leggibilità si pongono
(Eulero-Bernoulli, $\Phi = 0$):

$$
a_z = \frac{12 EI_z}{L^3},\quad b_z = \frac{6 EI_z}{L^2},\quad
c_z = \frac{4 EI_z}{L},\quad d_z = \frac{2 EI_z}{L};
\qquad
a_y = \frac{12 EI_y}{L^3},\quad b_y = \frac{6 EI_y}{L^2},\quad
c_y = \frac{4 EI_y}{L},\quad d_y = \frac{2 EI_y}{L}
$$

$$
\mathbf{k}^e_{\text{loc}} =
\begin{bmatrix}
\frac{EA}{L} & 0 & 0 & 0 & 0 & 0 & -\frac{EA}{L} & 0 & 0 & 0 & 0 & 0\\[3pt]
0 & a_z & 0 & 0 & 0 & b_z & 0 & -a_z & 0 & 0 & 0 & b_z\\[3pt]
0 & 0 & a_y & 0 & -b_y & 0 & 0 & 0 & -a_y & 0 & -b_y & 0\\[3pt]
0 & 0 & 0 & \frac{GJ}{L} & 0 & 0 & 0 & 0 & 0 & -\frac{GJ}{L} & 0 & 0\\[3pt]
0 & 0 & -b_y & 0 & c_y & 0 & 0 & 0 & b_y & 0 & d_y & 0\\[3pt]
0 & b_z & 0 & 0 & 0 & c_z & 0 & -b_z & 0 & 0 & 0 & d_z\\[3pt]
-\frac{EA}{L} & 0 & 0 & 0 & 0 & 0 & \frac{EA}{L} & 0 & 0 & 0 & 0 & 0\\[3pt]
0 & -a_z & 0 & 0 & 0 & -b_z & 0 & a_z & 0 & 0 & 0 & -b_z\\[3pt]
0 & 0 & -a_y & 0 & b_y & 0 & 0 & 0 & a_y & 0 & b_y & 0\\[3pt]
0 & 0 & 0 & -\frac{GJ}{L} & 0 & 0 & 0 & 0 & 0 & \frac{GJ}{L} & 0 & 0\\[3pt]
0 & 0 & -b_y & 0 & d_y & 0 & 0 & 0 & b_y & 0 & c_y & 0\\[3pt]
0 & b_z & 0 & 0 & 0 & d_z & 0 & -b_z & 0 & 0 & 0 & c_z
\end{bmatrix}
$$

Per **Timoshenko** i coefficienti flessionali si dividono per $(1+\Phi)$ e i
termini rotazionali diventano $c = \frac{(4+\Phi)EI}{L(1+\Phi)}$,
$d = \frac{(2-\Phi)EI}{L(1+\Phi)}$, con
$\Phi_y = \frac{12 EI_z}{G A_{sy} L^2}$ (piano x-y) e
$\Phi_z = \frac{12 EI_y}{G A_{sz} L^2}$ (piano x-z).

> La matrice è **simmetrica** e singolare (rango 6): i 6 autovalori nulli
> corrispondono ai moti rigidi dell'elemento libero.

---

## 2. Dalla rigidezza locale a quella globale

La rigidezza locale è espressa nel riferimento dell'elemento. Per assemblare serve
trasformarla nel riferimento globale tramite la matrice di rotazione.

### Matrice di rotazione $\mathbf{R}$ (3×3)

Le righe di $\mathbf{R}$ sono i versori degli assi locali in coordinate globali
(vedi [08 - Orientazione sezione](it-08-section-orientation.html)):

$$
\mathbf{R} = \begin{bmatrix}
\mathbf{e}_x^T \\ \mathbf{e}_y^T \\ \mathbf{e}_z^T
\end{bmatrix}
\qquad
\mathbf{v}_{\text{loc}} = \mathbf{R}\, \mathbf{v}_{\text{glob}}
$$

### Matrice di trasformazione $\mathbf{T}$ (12×12)

$\mathbf{R}$ viene replicata 4 volte (3 traslazioni + 3 rotazioni × 2 nodi) lungo
la diagonale:

$$
\mathbf{T} = \begin{bmatrix}
\mathbf{R} & & & \\
& \mathbf{R} & & \\
& & \mathbf{R} & \\
& & & \mathbf{R}
\end{bmatrix}_{12\times 12}
$$

```python
def transformation_matrix(self):
    R = self.rotation_matrix()
    T = np.zeros((12, 12))
    for k in range(4):
        T[3*k:3*k+3, 3*k:3*k+3] = R
    return T
```

### Congruenza: rotazione della rigidezza

La rigidezza globale dell'elemento si ottiene per **congruenza**:

$$
\boxed{\ \mathbf{k}^e_{\text{glob}} = \mathbf{T}^T\, \mathbf{k}^e_{\text{loc}}\, \mathbf{T}\ }
$$

```python
def stiffness_global(self):
    T = self.transformation_matrix()
    return T.T @ self.stiffness_local_effective() @ T
```

> `stiffness_local_effective()` applica eventualmente la **condensazione statica**
> dei rilasci (cerniere interne) prima della rotazione — vedi
> [06 - Timoshenko e Rilasci](it-06-timoshenko-releases.html).

---

## 3. Mappa dei gradi di libertà locale → globale

Ogni nodo ha 6 GdL globali numerati progressivamente in base all'ordine dei nodi:

$$
\text{nodo con indice } p \;\Rightarrow\; \text{GdL } [6p,\ 6p+1,\ \dots,\ 6p+5]
$$

```python
@property
def dof_map(self):
    dof_map = {}
    for i, nid in enumerate(sorted(self.nodes)):
        dof_map[nid] = np.arange(6*i, 6*i + 6)
    return dof_map
```

I 12 GdL globali di un elemento sono la concatenazione dei GdL dei suoi due nodi:

```python
def global_dofs(self, dof_map):
    return np.concatenate([dof_map[self.node_i.id], dof_map[self.node_j.id]])
```

Esempio: elemento tra nodo 1 (indice 0) e nodo 3 (indice 2):

```
global_dofs = [0,1,2,3,4,5,  12,13,14,15,16,17]
              └── nodo 1 ──┘  └─── nodo 3 ────┘
```

---

## 4. Assemblaggio della matrice globale

### Versione densa (didattica)

L'assemblaggio è una **somma di scatter**: ogni $\mathbf{k}^e_{\text{glob}}$ viene
inserito nelle righe/colonne corrispondenti ai suoi GdL globali e i contributi di
elementi che condividono un nodo si **sommano** automaticamente.

$$
\mathbf{K} = \sum_e \mathbf{L}_e^T\, \mathbf{k}^e_{\text{glob}}\, \mathbf{L}_e
$$

dove $\mathbf{L}_e$ è la matrice booleana di localizzazione (qui implementata
implicitamente con l'indexing di NumPy):

```python
def assemble_stiffness(self):
    ndof = self.ndof
    K = np.zeros((ndof, ndof))
    for el in self.elements.values():
        kg = el.stiffness_global()              # 12×12 globale
        ed = el.global_dofs(self.dof_map)        # indici globali
        K[np.ix_(ed, ed)] += kg                  # scatter & somma
    return K
```

`np.ix_(ed, ed)` seleziona il sotto-blocco 12×12 di $\mathbf{K}$ corrispondente ai
GdL dell'elemento; l'operatore `+=` somma i contributi sovrapposti.

### Versione sparsa (modelli grandi)

Per modelli grandi si evita di allocare una matrice densa $n_{dof} \times n_{dof}$:
si generano le **terne** (riga, colonna, valore) di tutte le matrici 12×12 e si
passano a `scipy.sparse.coo_matrix`, che somma i duplicati durante la conversione
in CSR (metodo standard, Cuvelier–Japhet–Scarella 2016):

```python
def assemble_stiffness_sparse(self):
    from scipy import sparse
    ne = len(self.elements)
    rows = np.empty(ne * 144, dtype=np.int64)
    cols = np.empty(ne * 144, dtype=np.int64)
    vals = np.empty(ne * 144, dtype=float)
    for e, el in enumerate(self.elements.values()):
        kg = el.stiffness_global()
        ed = el.global_dofs(self.dof_map)
        sl = slice(e*144, (e+1)*144)
        rows[sl] = np.repeat(ed, 12)   # riga di ogni entry 12×12
        cols[sl] = np.tile(ed, 12)     # colonna di ogni entry
        vals[sl] = kg.ravel()
    return sparse.coo_matrix((vals, (rows, cols)),
                             shape=(self.ndof, self.ndof)).tocsr()
```

I due metodi producono la stessa matrice (verificato nei test
`test_solver_consistency`). Vedi [12 - Solver Sparso](it-12-sparse-solver.html).

---

## 5. Applicazione dei vincoli e soluzione

Il sistema globale è $\mathbf{K}\,\mathbf{U} = \mathbf{F}$, ma $\mathbf{K}$ è
singolare finché non si applicano i vincoli. Si partizionano i GdL in **liberi**
($f$) e **vincolati/prescritti** ($p$):

$$
\begin{bmatrix} \mathbf{K}_{ff} & \mathbf{K}_{fp} \\ \mathbf{K}_{pf} & \mathbf{K}_{pp} \end{bmatrix}
\begin{bmatrix} \mathbf{U}_f \\ \mathbf{U}_p \end{bmatrix}
=
\begin{bmatrix} \mathbf{F}_f \\ \mathbf{R}_p \end{bmatrix}
$$

La prima equazione risolve gli spostamenti liberi (tenendo conto di eventuali
cedimenti $\mathbf{U}_p \neq 0$):

$$
\boxed{\ \mathbf{K}_{ff}\, \mathbf{U}_f = \mathbf{F}_f - \mathbf{K}_{fp}\, \mathbf{U}_p\ }
$$

```python
free_mask = np.ones(ndof, dtype=bool)
free_mask[p_idx] = False
f_idx = np.flatnonzero(free_mask)

U = np.zeros(ndof)
U[p_idx] = [prescribed[i] for i in p_idx]   # cedimenti imposti

K   = self.assemble_stiffness()
Kff = K[np.ix_(f_idx, f_idx)]
Kfp = K[np.ix_(f_idx, p_idx)]
rhs = F[f_idx] - Kfp @ U[p_idx]
U[f_idx] = np.linalg.solve(Kff, rhs)
```

### Reazioni vincolari

Una volta noti tutti gli spostamenti, le reazioni si recuperano dall'equilibrio
$\mathbf{R} = \mathbf{K}\mathbf{U} - \mathbf{F}$ (non nulle solo sui GdL vincolati):

```python
R = (K @ U) - F
R[f_idx] = 0.0   # i GdL liberi sono in equilibrio per costruzione
```

---

## 6. Riepilogo del flusso

```
┌──────────────────────────────────────────────────────────────────┐
│ Per ogni elemento:                                                 │
│   1.  k_loc = stiffness_local()           # forma chiusa 12×12     │
│   2.  [condensazione rilasci, opzionale]  # cerniere               │
│   3.  T = transformation_matrix()         # da R (assi locali)     │
│   4.  k_glob = Tᵀ k_loc T                 # congruenza             │
│   5.  ed = global_dofs(dof_map)           # mappa loc→glob         │
│   6.  K[ed, ed] += k_glob                 # scatter & somma        │
├──────────────────────────────────────────────────────────────────┤
│ Sistema globale:                                                   │
│   7.  partiziona GdL in liberi (f) e vincolati (p)                 │
│   8.  K_ff U_f = F_f − K_fp U_p           # risolvi                │
│   9.  R = K U − F                         # reazioni               │
└──────────────────────────────────────────────────────────────────┘
```

---

## 7. Matrici analoghe: massa e rigidezza geometrica

Lo stesso schema di assemblaggio (rotazione + scatter) si applica anche ad altre
matrici dell'elemento:

| Matrice | Simbolo | Uso | Metodo |
|---------|---------|-----|--------|
| Rigidezza elastica | $\mathbf{K}$ | statica, modale, buckling | `assemble_stiffness` |
| Massa | $\mathbf{M}$ | analisi modale | `assemble_mass` |
| Rigidezza geometrica | $\mathbf{K}_g$ | buckling lineare | `assemble_geometric_stiffness` |

La rigidezza geometrica $\mathbf{K}_g$ dipende dallo sforzo normale $N$ di ciascun
elemento (estratto dalla statica) e segue lo stesso pattern:

```python
def assemble_geometric_stiffness(self, element_N):
    Kg = np.zeros((self.ndof, self.ndof))
    for el in self.elements.values():
        N = element_N.get(el.id, 0.0)
        kg_glob = el.geometric_stiffness_global(N)   # Tᵀ kg_loc T
        ed = el.global_dofs(self.dof_map)
        Kg[np.ix_(ed, ed)] += kg_glob
    return Kg
```

In forma esplicita, la rigidezza geometrica locale del singolo elemento
(formulazione consistente di Przemieniecki, $N>0$ a trazione), nell'ordine dei
12 GdL $[\,u_x^i, u_y^i, u_z^i, \theta_x^i, \theta_y^i, \theta_z^i,\ u_x^j, u_y^j, u_z^j, \theta_x^j, \theta_y^j, \theta_z^j\,]$, è:

$$
\mathbf{k}_g^e = N
\begin{bmatrix}
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0\\[3pt]
0 & \frac{6}{5L} & 0 & 0 & 0 & \frac{1}{10} & 0 & -\frac{6}{5L} & 0 & 0 & 0 & \frac{1}{10}\\[3pt]
0 & 0 & \frac{6}{5L} & 0 & -\frac{1}{10} & 0 & 0 & 0 & -\frac{6}{5L} & 0 & -\frac{1}{10} & 0\\[3pt]
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0\\[3pt]
0 & 0 & -\frac{1}{10} & 0 & \frac{2L}{15} & 0 & 0 & 0 & \frac{1}{10} & 0 & -\frac{L}{30} & 0\\[3pt]
0 & \frac{1}{10} & 0 & 0 & 0 & \frac{2L}{15} & 0 & -\frac{1}{10} & 0 & 0 & 0 & -\frac{L}{30}\\[3pt]
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0\\[3pt]
0 & -\frac{6}{5L} & 0 & 0 & 0 & -\frac{1}{10} & 0 & \frac{6}{5L} & 0 & 0 & 0 & -\frac{1}{10}\\[3pt]
0 & 0 & -\frac{6}{5L} & 0 & \frac{1}{10} & 0 & 0 & 0 & \frac{6}{5L} & 0 & \frac{1}{10} & 0\\[3pt]
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0\\[3pt]
0 & 0 & -\frac{1}{10} & 0 & -\frac{L}{30} & 0 & 0 & 0 & \frac{1}{10} & 0 & \frac{2L}{15} & 0\\[3pt]
0 & \frac{1}{10} & 0 & 0 & 0 & -\frac{L}{30} & 0 & -\frac{1}{10} & 0 & 0 & 0 & \frac{2L}{15}
\end{bmatrix}
$$

Dipende solo dalla geometria ($L$) e dallo sforzo normale $N$: i GdL assiali
(1, 7) e torsionali (4, 10) non vi contribuiscono. I due blocchi non nulli
governano l'instabilità flessionale nei piani $x\text{-}y$ e $x\text{-}z$.

Il problema di **buckling lineare** diventa allora un problema agli autovalori
generalizzato $(\mathbf{K} + \lambda \mathbf{K}_g)\boldsymbol{\phi} = \mathbf{0}$
(vedi [19 - Analisi Modale](it-19-modal-analysis.html) e
[25 - Palazzo 3D](it-25-palazzo-3d.html)).

---

## 8. Elementi molla e vincoli elastici

### Molla assiale a 2 nodi (`SpringElement`)

Una molla assiale di rigidezza $k$ collega due nodi lungo la loro congiungente.
Detto $\mathbf{n} = [n_x, n_y, n_z]^{\mathsf T}$ il versore da $i$ a $j$,
l'allungamento è $\Delta = \mathbf{n}\cdot(\mathbf{u}_j - \mathbf{u}_i)$ e lo
sforzo $N = k\,\Delta$ (positivo a trazione). La molla agisce **solo sulle
traslazioni** (nessun momento), quindi la matrice è $6\times6$ sui GdL
$[u_x^i, u_y^i, u_z^i,\ u_x^j, u_y^j, u_z^j]$:

$$
\mathbf{k}_{\text{molla}} =
\begin{bmatrix} k\,\mathbf{P} & -k\,\mathbf{P} \\ -k\,\mathbf{P} & k\,\mathbf{P} \end{bmatrix},
\qquad
\mathbf{P} = \mathbf{n}\,\mathbf{n}^{\mathsf T} =
\begin{bmatrix}
n_x^2 & n_x n_y & n_x n_z \\
n_x n_y & n_y^2 & n_y n_z \\
n_x n_z & n_y n_z & n_z^2
\end{bmatrix}
$$

```python
n = self.direction()          # versore i -> j
P = np.outer(n, n)            # 3x3
K = np.zeros((6, 6))
K[0:3, 0:3] =  k * P
K[3:6, 3:6] =  k * P
K[0:3, 3:6] = -k * P
K[3:6, 0:3] = -k * P
```

La matrice ha **rango 1** (un solo modo deformativo, lungo $\mathbf{n}$): le
direzioni trasversali e le rotazioni restano libere. Le molle **unilatere**
(`behavior='tension'` o `'compression'`) usano una rigidezza efficace
$k_{\text{eff}}$ pari a $k$ se attiva, $0$ se spenta; lo stato è deciso
iterativamente dal solutore (schema active-set).

### Vincoli elastici a terra (`add_elastic_support`)

Un vincolo elastico verso il suolo su un singolo GdL nodale non è un elemento
a 2 nodi, ma un **contributo diagonale** alla rigidezza globale: somma $k$ sul
GdL corrispondente,

$$
\mathbf{K}[d, d] \mathrel{+}= k, \qquad d = \text{GdL nodale vincolato}
$$

Per i 6 GdL di un nodo $[u_x, u_y, u_z, \theta_x, \theta_y, \theta_z]$ si possono
quindi assegnare rigidezze traslazionali e rotazionali indipendenti.

---

## 9. Vincoli multi-nodo: Equal DOF e link rigidi

{: .warning }
**Non ancora implementati.** Questa sezione descrive la formulazione teorica
prevista per `beamfeapy`. L'API (`Model.add_equal_dof`, `Model.add_rigid_link`)
è una proposta e potrebbe cambiare. Allo stato attuale i vincoli disponibili
sono i vincoli rigidi (`fix`), gli spostamenti imposti e i vincoli elastici
(`add_elastic_support`).

I vincoli multi-nodo (*Multi-Point Constraints*, MPC) impongono **relazioni
lineari tra i gradi di libertà di nodi diversi**, esprimibili in forma generale:

$$
\mathbf{C}\,\mathbf{U} = \mathbf{Q}
$$

dove $\mathbf{C}$ è la matrice dei vincoli (una riga per equazione) e
$\mathbf{Q}$ il termine noto (nullo per vincoli omogenei).

### Equal DOF (uguaglianza di gradi di libertà)

Lega uno o più GdL di un nodo *slave* $s$ ai corrispondenti GdL di un nodo
*master* $m$, imponendone l'uguaglianza:

$$
u^{s}_{d} = u^{m}_{d}, \qquad d \in \{u_x, u_y, u_z, \theta_x, \theta_y, \theta_z\}
$$

Equivale alla riga di vincolo $u^{s}_{d} - u^{m}_{d} = 0$. È utile per
**cerniere a GdL condivisi**, simmetrie, o per accoppiare traslazioni di nodi
distinti (es. impalcato che trasla rigidamente in piano).

### Link rigido (rigid link)

Impone che il nodo *slave* $s$ segua il moto di **corpo rigido** del nodo
*master* $m$. Detto $\mathbf{r} = \mathbf{x}_s - \mathbf{x}_m$ il braccio, la
cinematica è:

$$
\mathbf{u}_s = \mathbf{u}_m + \boldsymbol{\theta}_m \times \mathbf{r},
\qquad
\boldsymbol{\theta}_s = \boldsymbol{\theta}_m
$$

In forma matriciale, i 6 GdL dello slave si esprimono dai 6 del master tramite
la matrice cinematica $\mathbf{T}_{rl}$ (con $\tilde{\mathbf{r}}$ matrice di
prodotto vettoriale):

$$
\begin{bmatrix} \mathbf{u}_s \\ \boldsymbol{\theta}_s \end{bmatrix}
=
\underbrace{\begin{bmatrix} \mathbf{I}_3 & -\tilde{\mathbf{r}} \\ \mathbf{0} & \mathbf{I}_3 \end{bmatrix}}_{\mathbf{T}_{rl}}
\begin{bmatrix} \mathbf{u}_m \\ \boldsymbol{\theta}_m \end{bmatrix},
\qquad
\tilde{\mathbf{r}} =
\begin{bmatrix}
0 & -r_z & r_y \\
r_z & 0 & -r_x \\
-r_y & r_x & 0
\end{bmatrix}
$$

L'Equal DOF è il caso particolare $\mathbf{r} = \mathbf{0}$ con il sottoinsieme
di GdL selezionato.

### Metodi di imposizione

Una volta scritte le equazioni $\mathbf{C}\,\mathbf{U} = \mathbf{Q}$, il vincolo
può essere imposto in tre modi:

| Metodo | Idea | Pro / contro |
|--------|------|--------------|
| **Eliminazione (master-slave)** | $\mathbf{U} = \mathbf{T}_g\,\mathbf{U}_{rid}$, poi $\mathbf{K}_{rid} = \mathbf{T}_g^{\mathsf T}\mathbf{K}\,\mathbf{T}_g$ | esatto, riduce le incognite; richiede partizione master/slave |
| **Penalità** | aggiunge $\alpha\,\mathbf{C}^{\mathsf T}\mathbf{C}$ a $\mathbf{K}$ | semplice; vincolo approssimato, sensibile ad $\alpha$ |
| **Moltiplicatori di Lagrange** | sistema aumentato $\begin{bmatrix}\mathbf{K} & \mathbf{C}^{\mathsf T}\\ \mathbf{C} & \mathbf{0}\end{bmatrix}\begin{bmatrix}\mathbf{U}\\ \boldsymbol{\lambda}\end{bmatrix} = \begin{bmatrix}\mathbf{F}\\ \mathbf{Q}\end{bmatrix}$ | esatto; aumenta la dimensione e perde la definita positività |

L'approccio previsto per `beamfeapy` è l'**eliminazione master-slave**, coerente
con la condensazione statica già usata per i rilasci (§1).

{: .note }
**API proposta (bozza)**
```python
# Equal DOF: i GdL uy, uz del nodo 5 seguono il nodo 2
m.add_equal_dof(master=2, slave=5, dofs=["uy", "uz"])

# Link rigido: il nodo 7 è solidale al nodo 3 (tutti e 6 i GdL)
m.add_rigid_link(master=3, slave=7)
```

---

## Verifica: proprietà della matrice assemblata

```python
import numpy as np
from beamfeapy import Material, Model, Section

m = Model()
m.add_node(1, 0, 0, 0); m.add_node(2, 4, 0, 0); m.add_node(3, 8, 0, 0)
mat = Material(210e9, 0.3); sec = Section(A=1e-2, Iy=2e-5, Iz=3e-5, J=1e-5)
m.add_beam(1, 1, 2, mat, sec); m.add_beam(2, 2, 3, mat, sec)

K = m.assemble_stiffness()
# 1. simmetrica
assert np.allclose(K, K.T)
# 2. semidefinita positiva (modi rigidi -> autovalori ~0, mai negativi)
assert np.linalg.eigvalsh(K).min() > -1e-6
# 3. denso == sparso
assert np.allclose(K, m.assemble_stiffness_sparse().toarray())
print("Matrice di rigidezza verificata.")
```

---

*Vedi anche:*
[20 - Funzioni di forma](it-20-shape-functions.html) |
[12 - Solver Sparso](it-12-sparse-solver.html) |
[06 - Timoshenko e Rilasci](it-06-timoshenko-releases.html) |
[13 - Convenzioni](it-13-conventions.html)
