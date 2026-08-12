---
layout: default
title: "21 - Stiffness Matrix Assembly"
parent: English
nav_order: 21
---

# 21 - Stiffness Matrix Assembly

This page explains how beamfeapy builds the stiffness matrix, from the single
element up to the global system, and how it applies the constraints to solve.

The complete path is:

```
k_local (12×12)  ──T──►  k_global (12×12)  ──scatter──►  K (ndof×ndof)  ──vincoli──►  K_ff u_f = F_f
```

References in the code: `BeamElement3D.stiffness_local/_global`,
`Model.assemble_stiffness`, `Model.assemble_stiffness_sparse`, `Model.solve`.

---

## 1. Local element stiffness (12×12)

The local stiffness matrix derives from the integral $\mathbf{k}^e = \int_0^L
\mathbf{B}^T \mathbf{D}\, \mathbf{B}\, dx$ (see
[20 - Shape Functions](en-20-shape-functions.html)). For the prismatic element
it has a **closed form** and the four behaviors remain decoupled.

### Axial term ($EA$)

$$
\mathbf{k}_{\text{ax}} = \frac{EA}{L}
\begin{bmatrix} 1 & -1 \\ -1 & 1 \end{bmatrix}
\qquad \text{(DOF } u_x^i, u_x^j\text{)}
$$

```python
ea = E * A / L
k[0, 0] = k[6, 6] = ea
k[0, 6] = k[6, 0] = -ea
```

### Torsional term ($GJ$)

$$
\mathbf{k}_{\text{tor}} = \frac{GJ}{L}
\begin{bmatrix} 1 & -1 \\ -1 & 1 \end{bmatrix}
\qquad \text{(DOF } \theta_x^i, \theta_x^j\text{)}
$$

```python
gj = G * J / L
k[3, 3] = k[9, 9] = gj
k[3, 9] = k[9, 3] = -gj
```

### Bending terms ($EI_z$ and $EI_y$)

For bending in the x-y plane (DOF $u_y^i, \theta_z^i, u_y^j, \theta_z^j$ →
$EI_z$), the classic beam block is:

$$
\mathbf{k}_{\text{flex},z} = \frac{EI_z}{L^3}
\begin{bmatrix}
12 & 6L & -12 & 6L \\
6L & 4L^2 & -6L & 2L^2 \\
-12 & -6L & 12 & -6L \\
6L & 2L^2 & -6L & 4L^2
\end{bmatrix}
$$

The library writes the coefficients in a **unified Euler / Timoshenko form**,
introducing the shear parameter $\Phi$ (zero for Euler-Bernoulli):

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

For Euler-Bernoulli $\Phi_y = 0$ and the classic block is recovered. For Timoshenko
$\Phi_y = \dfrac{12 EI_z}{G A_{sy} L^2}$ introduces shear deformability.

> The block for the x-z plane ($EI_y$, DOF $u_z, \theta_y$) is analogous, but with
> **inverted signs** on the mixed terms due to the convention
> $\theta_y = -du_z/dx$.

### Full 12×12 local matrix (symbolic form)

Assembling the four contributions in the DOF order
$[\,u_x^i, u_y^i, u_z^i, \theta_x^i, \theta_y^i, \theta_z^i,\ u_x^j, u_y^j, u_z^j, \theta_x^j, \theta_y^j, \theta_z^j\,]$
yields the explicit single-element matrix. For readability set
(Euler-Bernoulli, $\Phi = 0$):

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

For **Timoshenko** the bending coefficients are divided by $(1+\Phi)$ and the
rotational terms become $c = \frac{(4+\Phi)EI}{L(1+\Phi)}$,
$d = \frac{(2-\Phi)EI}{L(1+\Phi)}$, with
$\Phi_y = \frac{12 EI_z}{G A_{sy} L^2}$ (x-y plane) and
$\Phi_z = \frac{12 EI_y}{G A_{sz} L^2}$ (x-z plane).

> The matrix is **symmetric** and singular (rank 6): the 6 zero eigenvalues
> correspond to the rigid-body modes of the free element.

---

## 2. From local to global stiffness

The local stiffness is expressed in the element reference frame. To assemble, it must
be transformed into the global reference frame through the rotation matrix.

### Rotation matrix $\mathbf{R}$ (3×3)

The rows of $\mathbf{R}$ are the unit vectors of the local axes in global coordinates
(see [08 - Section Orientation](en-08-section-orientation.html)):

$$
\mathbf{R} = \begin{bmatrix}
\mathbf{e}_x^T \\ \mathbf{e}_y^T \\ \mathbf{e}_z^T
\end{bmatrix}
\qquad
\mathbf{v}_{\text{loc}} = \mathbf{R}\, \mathbf{v}_{\text{glob}}
$$

### Transformation matrix $\mathbf{T}$ (12×12)

$\mathbf{R}$ is replicated 4 times (3 translations + 3 rotations × 2 nodes) along
the diagonal:

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

### Congruence: rotation of the stiffness

The global element stiffness is obtained by **congruence**:

$$
\boxed{\ \mathbf{k}^e_{\text{glob}} = \mathbf{T}^T\, \mathbf{k}^e_{\text{loc}}\, \mathbf{T}\ }
$$

```python
def stiffness_global(self):
    T = self.transformation_matrix()
    return T.T @ self.stiffness_local_effective() @ T
```

> `stiffness_local_effective()` optionally applies the **static condensation**
> of the releases (internal hinges) before the rotation — see
> [06 - Timoshenko and Releases](en-06-timoshenko-releases.html).

---

## 3. Local → global degree of freedom map

Each node has 6 global DOF numbered progressively according to the node order:

$$
\text{node with index } p \;\Rightarrow\; \text{DOF } [6p,\ 6p+1,\ \dots,\ 6p+5]
$$

```python
@property
def dof_map(self):
    dof_map = {}
    for i, nid in enumerate(sorted(self.nodes)):
        dof_map[nid] = np.arange(6*i, 6*i + 6)
    return dof_map
```

The 12 global DOF of an element are the concatenation of the DOF of its two nodes:

```python
def global_dofs(self, dof_map):
    return np.concatenate([dof_map[self.node_i.id], dof_map[self.node_j.id]])
```

Example: element between node 1 (index 0) and node 3 (index 2):

```
global_dofs = [0,1,2,3,4,5,  12,13,14,15,16,17]
              └── node 1 ──┘  └─── node 3 ────┘
```

---

## 4. Assembly of the global matrix

### Dense version (didactic)

The assembly is a **sum of scatters**: each $\mathbf{k}^e_{\text{glob}}$ is
inserted into the rows/columns corresponding to its global DOF and the contributions of
elements sharing a node are **summed** automatically.

$$
\mathbf{K} = \sum_e \mathbf{L}_e^T\, \mathbf{k}^e_{\text{glob}}\, \mathbf{L}_e
$$

where $\mathbf{L}_e$ is the boolean localization matrix (here implemented
implicitly with NumPy indexing):

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

`np.ix_(ed, ed)` selects the 12×12 sub-block of $\mathbf{K}$ corresponding to the
element DOF; the `+=` operator sums the overlapping contributions.

### Sparse version (large models)

For large models, allocating a dense matrix $n_{dof} \times n_{dof}$ is avoided:
the **triplets** (row, column, value) of all the 12×12 matrices are generated and
passed to `scipy.sparse.coo_matrix`, which sums the duplicates during the conversion
to CSR (standard method, Cuvelier–Japhet–Scarella 2016):

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

The two methods produce the same matrix (verified in the
`test_solver_consistency` tests). See [12 - Sparse Solver](en-12-sparse-solver.html).

---

## 5. Application of constraints and solution

The global system is $\mathbf{K}\,\mathbf{U} = \mathbf{F}$, but $\mathbf{K}$ is
singular until the constraints are applied. The DOF are partitioned into **free**
($f$) and **constrained/prescribed** ($p$):

$$
\begin{bmatrix} \mathbf{K}_{ff} & \mathbf{K}_{fp} \\ \mathbf{K}_{pf} & \mathbf{K}_{pp} \end{bmatrix}
\begin{bmatrix} \mathbf{U}_f \\ \mathbf{U}_p \end{bmatrix}
=
\begin{bmatrix} \mathbf{F}_f \\ \mathbf{R}_p \end{bmatrix}
$$

The first equation solves for the free displacements (accounting for any
settlements $\mathbf{U}_p \neq 0$):

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

### Support reactions

Once all the displacements are known, the reactions are recovered from equilibrium
$\mathbf{R} = \mathbf{K}\mathbf{U} - \mathbf{F}$ (nonzero only on the constrained DOF):

```python
R = (K @ U) - F
R[f_idx] = 0.0   # the free DOFs are in equilibrium by construction
```

---

## 6. Flow summary

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
│   7.  partiziona DOF in liberi (f) e vincolati (p)                 │
│   8.  K_ff U_f = F_f − K_fp U_p           # risolvi                │
│   9.  R = K U − F                         # reazioni               │
└──────────────────────────────────────────────────────────────────┘
```

---

## 7. Analogous matrices: mass and geometric stiffness

The same assembly scheme (rotation + scatter) also applies to other
element matrices:

| Matrix | Symbol | Use | Method |
|---------|---------|-----|--------|
| Elastic stiffness | $\mathbf{K}$ | static, modal, buckling | `assemble_stiffness` |
| Mass | $\mathbf{M}$ | modal analysis | `assemble_mass` |
| Geometric stiffness | $\mathbf{K}_g$ | linear buckling | `assemble_geometric_stiffness` |

The geometric stiffness $\mathbf{K}_g$ depends on the axial force $N$ of each
element (extracted from the static analysis) and follows the same pattern:

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

In explicit form, the local geometric stiffness of the single element
(consistent Przemieniecki formulation, $N>0$ in tension), in the DOF order
$[\,u_x^i, u_y^i, u_z^i, \theta_x^i, \theta_y^i, \theta_z^i,\ u_x^j, u_y^j, u_z^j, \theta_x^j, \theta_y^j, \theta_z^j\,]$, is:

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

It depends only on the geometry ($L$) and the axial force $N$: the axial DOF
(1, 7) and torsional DOF (4, 10) do not contribute. The two non-zero blocks
govern flexural buckling in the $x\text{-}y$ and $x\text{-}z$ planes.

The **linear buckling** problem then becomes a generalized eigenvalue problem
$(\mathbf{K} + \lambda \mathbf{K}_g)\boldsymbol{\phi} = \mathbf{0}$
(see [19 - Modal Analysis](en-19-modal-analysis.html) and
[25 - 3D Building](en-25-palazzo-3d.html)).

---

## 8. Spring elements and elastic supports

### 2-node axial spring (`SpringElement`)

An axial spring of stiffness $k$ connects two nodes along their joining line.
With $\mathbf{n} = [n_x, n_y, n_z]^{\mathsf T}$ the unit vector from $i$ to $j$,
the elongation is $\Delta = \mathbf{n}\cdot(\mathbf{u}_j - \mathbf{u}_i)$ and the
force $N = k\,\Delta$ (positive in tension). The spring acts **only on the
translations** (no moments), so the matrix is $6\times6$ over the DOF
$[u_x^i, u_y^i, u_z^i,\ u_x^j, u_y^j, u_z^j]$:

$$
\mathbf{k}_{\text{spring}} =
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
n = self.direction()          # unit vector i -> j
P = np.outer(n, n)            # 3x3
K = np.zeros((6, 6))
K[0:3, 0:3] =  k * P
K[3:6, 3:6] =  k * P
K[0:3, 3:6] = -k * P
K[3:6, 0:3] = -k * P
```

The matrix has **rank 1** (a single deformation mode, along $\mathbf{n}$): the
transverse directions and the rotations stay free. **Unilateral** springs
(`behavior='tension'` or `'compression'`) use an effective stiffness
$k_{\text{eff}}$ equal to $k$ when active and $0$ when off; the state is decided
iteratively by the solver (active-set scheme).

### Elastic supports to ground (`add_elastic_support`)

An elastic support to ground on a single nodal DOF is not a 2-node element but a
**diagonal contribution** to the global stiffness: it adds $k$ on the
corresponding DOF,

$$
\mathbf{K}[d, d] \mathrel{+}= k, \qquad d = \text{constrained nodal DOF}
$$

Independent translational and rotational stiffnesses can thus be assigned to the
6 DOF of a node $[u_x, u_y, u_z, \theta_x, \theta_y, \theta_z]$.

---

## 9. Multi-node constraints: Equal DOF and rigid links

{: .warning }
**Not yet implemented.** This section describes the planned theoretical
formulation for `beamfeapy`. The API (`Model.add_equal_dof`,
`Model.add_rigid_link`) is a proposal and may change. Currently the available
constraints are rigid restraints (`fix`), prescribed displacements, and elastic
supports (`add_elastic_support`).

Multi-node constraints (*Multi-Point Constraints*, MPC) enforce **linear
relations between the DOF of different nodes**, expressible in general form as:

$$
\mathbf{C}\,\mathbf{U} = \mathbf{Q}
$$

where $\mathbf{C}$ is the constraint matrix (one row per equation) and
$\mathbf{Q}$ the right-hand side (zero for homogeneous constraints).

### Equal DOF

Ties one or more DOF of a *slave* node $s$ to the corresponding DOF of a
*master* node $m$, enforcing their equality:

$$
u^{s}_{d} = u^{m}_{d}, \qquad d \in \{u_x, u_y, u_z, \theta_x, \theta_y, \theta_z\}
$$

This is the constraint row $u^{s}_{d} - u^{m}_{d} = 0$. Useful for **shared-DOF
hinges**, symmetries, or to couple translations of distinct nodes (e.g. a deck
translating rigidly in plane).

### Rigid link

Forces the *slave* node $s$ to follow the **rigid-body** motion of the *master*
node $m$. With $\mathbf{r} = \mathbf{x}_s - \mathbf{x}_m$ the lever arm, the
kinematics is:

$$
\mathbf{u}_s = \mathbf{u}_m + \boldsymbol{\theta}_m \times \mathbf{r},
\qquad
\boldsymbol{\theta}_s = \boldsymbol{\theta}_m
$$

In matrix form, the 6 slave DOF derive from the 6 master DOF through the
kinematic matrix $\mathbf{T}_{rl}$ (with $\tilde{\mathbf{r}}$ the cross-product
matrix):

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

Equal DOF is the special case $\mathbf{r} = \mathbf{0}$ with the selected DOF
subset.

### Enforcement methods

Once the equations $\mathbf{C}\,\mathbf{U} = \mathbf{Q}$ are written, the
constraint can be enforced in three ways:

| Method | Idea | Pros / cons |
|--------|------|-------------|
| **Elimination (master-slave)** | $\mathbf{U} = \mathbf{T}_g\,\mathbf{U}_{red}$, then $\mathbf{K}_{red} = \mathbf{T}_g^{\mathsf T}\mathbf{K}\,\mathbf{T}_g$ | exact, reduces unknowns; needs master/slave partition |
| **Penalty** | adds $\alpha\,\mathbf{C}^{\mathsf T}\mathbf{C}$ to $\mathbf{K}$ | simple; approximate constraint, sensitive to $\alpha$ |
| **Lagrange multipliers** | augmented system $\begin{bmatrix}\mathbf{K} & \mathbf{C}^{\mathsf T}\\ \mathbf{C} & \mathbf{0}\end{bmatrix}\begin{bmatrix}\mathbf{U}\\ \boldsymbol{\lambda}\end{bmatrix} = \begin{bmatrix}\mathbf{F}\\ \mathbf{Q}\end{bmatrix}$ | exact; increases size and loses positive definiteness |

The approach planned for `beamfeapy` is **master-slave elimination**, consistent
with the static condensation already used for releases (§1).

{: .note }
**Proposed API (draft)**
```python
# Equal DOF: DOF uy, uz of node 5 follow node 2
m.add_equal_dof(master=2, slave=5, dofs=["uy", "uz"])

# Rigid link: node 7 is rigidly tied to node 3 (all 6 DOF)
m.add_rigid_link(master=3, slave=7)
```

---

## Verification: properties of the assembled matrix

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

*See also:*
[20 - Shape Functions](en-20-shape-functions.html) |
[12 - Sparse Solver](en-12-sparse-solver.html) |
[06 - Timoshenko and Releases](en-06-timoshenko-releases.html) |
[13 - Conventions](en-13-conventions.html)
