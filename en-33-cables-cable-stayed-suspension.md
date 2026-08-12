---
layout: default
title: "33 - Cables: Cable-Stayed & Suspension Bridges"
parent: "14 - Usage Examples"
grand_parent: English
nav_order: 8
---

# 33 - Cables: cable-stayed and suspension bridges

beamfeapy provides **tension-only, large-displacement cable elements**, suitable
for **stay cables** (cable-stayed bridges) and **main cables** (suspension
bridges). Cables make the problem **nonlinear** (stiffness depends on the
tension and on the configuration) and are solved with `solve_nonlinear`
(Newton-Raphson).

## 1. Two cable elements

| Element | Class | Typical use |
|---------|-------|-------------|
| Corotational bar | `CableElement` (`add_cable`) | stays, hangers; cable discretised into several segments |
| Exact elastic catenary | `CatenaryCableElement` (`add_catenary_cable`) | suspension main cable, **exact self-weight** with one element per panel |

- **`add_cable`** — tension-only bar with material stiffness $EA/L_0$ and
  geometric "chord" stiffness $N/L$ (transverse stiffness proportional to the
  tension). If the cable goes into compression the tension is set to zero
  (tension-only). Optionally uses the **Ernst modulus** for inclined stays.
- **`add_catenary_cable`** — Irvine elastic catenary: carries its **own
  self-weight sag** ($w>0$) without discretisation; one element per panel
  represents the hanging cable exactly.

> Pretension is set with `N0` (initial tension, from which the unstressed length
> $L_0$ is derived) or directly with `L0`.

### Tangent stiffness matrix (symbolic form)

The cable acts only on the **3 translations** per node: the matrix is $6\times6$
over the DOF $[u_x^i, u_y^i, u_z^i,\ u_x^j, u_y^j, u_z^j]$. It is **nonlinear** →
the *tangent* stiffness is written in the current configuration. With $L_0$ the
unstressed length, $L$ the current length, $\mathbf{d}$ the current unit vector
from $i$ to $j$, $\varepsilon = (L-L_0)/L_0$ the strain and
$N = E_{\text{eff}} A\,\varepsilon$ the tension ($>0$ in tension):

$$
\mathbf{g} = N \begin{bmatrix} -\mathbf{d} \\ \mathbf{d} \end{bmatrix},
\qquad
\mathbf{K}_t = \begin{bmatrix} \mathbf{k} & -\mathbf{k} \\ -\mathbf{k} & \mathbf{k} \end{bmatrix},
\qquad
\mathbf{k} = \underbrace{\frac{E_{\text{eff}} A}{L_0}\,\mathbf{d}\,\mathbf{d}^{\mathsf T}}_{\text{material (axial)}}
+ \underbrace{\frac{N}{L}\big(\mathbf{I}_3 - \mathbf{d}\,\mathbf{d}^{\mathsf T}\big)}_{\text{geometric (chord)}}
$$

Writing out $\mathbf{d} = [d_x, d_y, d_z]^{\mathsf T}$, the $3\times3$ block
$\mathbf{k}$ is:

$$
\mathbf{k} =
\frac{E_{\text{eff}} A}{L_0}
\begin{bmatrix}
d_x^2 & d_x d_y & d_x d_z \\
d_x d_y & d_y^2 & d_y d_z \\
d_x d_z & d_y d_z & d_z^2
\end{bmatrix}
+ \frac{N}{L}
\begin{bmatrix}
1-d_x^2 & -d_x d_y & -d_x d_z \\
-d_x d_y & 1-d_y^2 & -d_y d_z \\
-d_x d_z & -d_y d_z & 1-d_z^2
\end{bmatrix}
$$

- the **material term** $\frac{E_{\text{eff}}A}{L_0}\mathbf{d}\mathbf{d}^{\mathsf T}$ gives stiffness only along the cable axis $\mathbf{d}$;
- the **geometric term** $\frac{N}{L}(\mathbf{I}_3 - \mathbf{d}\mathbf{d}^{\mathsf T})$ projects onto the plane **transverse** to $\mathbf{d}$ and grows with the tension $N$ (taut-string effect): an unloaded cable ($N=0$) has no lateral stiffness and is unstable.

For **tension-only** behaviour, if $\varepsilon < 0$ (slack cable) $N=0$ is set
with a minimal residual material stiffness to avoid singularity.

> The **catenary cable** (`CatenaryCableElement`) has the same $6\times6$
> structure but its internal forces come from the elastic-catenary compatibility
> equations (Irvine, 1981) and the tangent is computed by **finite differences**
> (then symmetrized), not in closed form.

## 2. Ernst equivalent modulus

An inclined stay modelled with **a single element** has an apparent axial
stiffness reduced by its self-weight sag. beamfeapy uses the Ernst modulus
(`ernst=True`):

$$
E_{\text{eff}} = \dfrac{E}{1 + \dfrac{w^2\,A\,L_h^2\,E}{12\,N^3}}
$$

with $w$ weight per unit length, $L_h$ horizontal projection, $N$ tension. For a
long stay ($L_h = 250$ m, $T = 3$ MN, $A = 0.01\ \text{m}^2$):
$E_{\text{eff}} \approx 158$ GPa, i.e. a **~19 % reduction** from $E = 195$ GPa.

## 3. Example: cable-stayed bridge

Central pylon, continuous deck and fan stays from the pylon top to the deck
nodes; the stays use the Ernst modulus.

![Cable-stayed bridge: layout and deformed shape](images/ex33_ponte_strallato.png)

**Validations** (script
[`scripts/generate_cable_bridges_figures.py`](https://github.com/DomenicoGaudioso/beamfeapy/blob/main/scripts/generate_cable_bridges_figures.py),
and example [`usage_examples/37_ponte_strallato.py`](https://github.com/DomenicoGaudioso/beamfeapy/blob/main/usage_examples/37_ponte_strallato.py)):

- **single stay**: node equilibrium gives $T = P/\sin\theta$; the FEM reproduces
  it **exactly** (0.00 % error);
- **Ernst modulus**: the stiffness reduction matches the closed-form formula;
- **global equilibrium**: the sum of vertical reactions equals the total load
  (deck weight + stay self-weight).

```python
import math
from beamfeapy import Material, Model, Section

m = Model()
m.add_node(1, 0.0, 55.0, 0.0)     # pylon top
m.add_node(2, 100.0, 0.0, 0.0)    # deck anchorage
theta = math.atan2(55.0, 100.0)
# stay with Ernst modulus and form-finding pretension
m.add_cable(1, 1, 2, E=195e9, A=0.010, N0=1.5e5 / math.sin(theta),
            w=78.5e3 * 0.010, ernst=True)
# ... deck/pylon beams, supports, loads ...
r = m.solve_nonlinear(max_iter=200)
T = r.cable_force(1)              # stay tension [N]
```

## 4. Example: suspension bridge

Main cable as an **elastic catenary** (one element per panel, exact self-weight)
+ vertical **hangers** (tension-only cables) + deck (stiffening girder).

![Suspension bridge: main cable, hangers and deck](images/ex33_ponte_sospeso.png)

**Validation** (classical parabolic-cable theory): for a uniform vertical load
the horizontal tension component is $H = w\,L^2/(8f)$. With $L = 800$ m and sag
$f \approx 81$ m ($f/L \approx 1/9.8$):

| Quantity | FEM | Theory $wL^2/8f$ | Error |
|----------|----:|-----------------:|------:|
| $H$ (horizontal) | 52.8 MN | 52.6 MN | 0.3 % |

The main-cable tension ranges from ~52.8 MN (midspan) to ~56.7 MN (tower tops),
where $T_{\max} = \sqrt{H^2 + V^2}$.

```python
from beamfeapy import Material, Model, Section

m = Model()
# main cable: one elastic CATENARY element per panel (exact self-weight)
m.add_catenary_cable(1, n_a, n_b, E=200e9, A=0.30, w=78.5e3 * 0.30)
# hangers: vertical tension-only cables from the cable to the deck
m.add_cable(10, n_cable, n_deck, E=200e9, A=0.012, N0=1.5e6)
# ... deck (beams), supports, loads ...
r = m.solve_nonlinear(max_iter=400)
H    = r.cable_horizontal(1)     # horizontal tension component [N]
Tmax = r.cable_force(1)          # panel maximum tension [N]
```

## 5. Quick API

| Method | Description |
|--------|-------------|
| `Model.add_cable(id, ni, nj, E, A, N0=…, L0=…, w=…, ernst=…)` | tension-only bar cable (stays, hangers) |
| `Model.add_catenary_cable(id, ni, nj, E, A, w, N0=…, L0=…)` | elastic catenary cable (exact self-weight) |
| `Model.solve_nonlinear(cases=…, max_iter=…)` | nonlinear solution (Newton-Raphson) |
| `Result.cable_force(id)` | cable axial tension [N] (>0 tension) |
| `Result.cable_horizontal(id)` | horizontal tension component $H$ (catenary) [N] |

> References: H.J. Ernst, *Der Bauingenieur* 40 (1965); H.M. Irvine, *Cable
> Structures*, MIT Press, 1981; N.J. Gimsing & C.T. Georgakis, *Cable Supported
> Bridges*, Wiley, 2012.
