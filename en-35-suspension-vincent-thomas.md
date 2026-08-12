---
layout: default
title: "35 - Vincent Thomas Suspension Bridge"
parent: "14 - Usage Examples"
grand_parent: English
nav_order: 10
---

# 35 - Vincent Thomas Suspension Bridge (San Pedro)

**Faithful** 3D reconstruction and self-weight analysis of the **Vincent Thomas
Bridge** (San Pedro, CA), a much-studied seismic benchmark. Geometry, sections
and loads are the **real published values** from Abdel-Ghaffar & Housner's Caltech
Earthquake Engineering Research Laboratory reports; the computed frequencies are
compared with those **measured** on the bridge.

The model uses the [cable elements](en-33-cables-cable-stayed-suspension.html): an
**elastic-catenary** main cable, tension-only **hangers** and a non-linear
solution with *load-stepping*.

## 1. Real data

From the Vincent Thomas numerical example in A.M. Abdel-Ghaffar, *Dynamic
analyses of suspension bridge structures*, EERL 76-01, Caltech 1976 (pp.
199-200):

| Quantity | Real value |
|----------|-----------:|
| Main span | 1500 ft = 457.2 m |
| Side spans | 506.5 ft = 154.4 m |
| Cable sag | 150 ft = 45.72 m |
| Truss/cable spacing *b* | 59.17 ft = 18.03 m |
| Stiffening-truss depth *d* | 15 ft = 4.572 m |
| Cable area | 121 in² = 0.0781 m² |
| Chord area | 53.78 in² = 0.0347 m² |
| Diagonal area | 16.9 in² = 0.0109 m² |
| Deck dead load | 6.15 kips/ft (≈ 45 kN/m per truss) |
| Design cable horizontal force | 6,750 kips = 30,027 kN/cable |

## 2. FEM model

The deck consists of the two real **stiffening trusses** (chords, verticals and
diagonals, Pratt type) connected by floor beams and bottom lateral bracing,
forming the torsionally stiff **box system**. The main cable is modelled with
**elastic-catenary** elements (one per panel, exact self-weight), the **hangers**
are tension-only cables, and the **towers are portal frames** (two legs X-braced
at several levels).

![Vincent Thomas 3D FEM model](images/vt_real_model.png)

## 3. Loads

Load = deck self-weight (distributed on the top chords) + cable self-weight
(added automatically).

![Loads on the FEM model](images/vt_real_loads.png)

## 4. Static analysis

Non-linear solution (Newton-Raphson with *load-stepping*). The cable takes its
self-weight equilibrium shape and the deck follows it through the hangers.

![Deformed shape under self-weight](images/vt_real_deformed.png)

The chord **axial force** alternates tension/compression along the span; the
**cable tension** grows from midspan toward the towers.

![Internal forces: axial force and cable tension](images/vt_real_forces.png)

**Static validation:** the computed horizontal cable force is **28,585 kN/cable**
versus the published design value of **30,027 kN/cable** (−5%), with no live
load — an excellent agreement using only real data.

## 5. Modal analysis

With cable self-weight included and the cables linearized about equilibrium, the
3D modal analysis yields vertical, lateral and torsional modes. The vertical
frequencies measured by Abdel-Ghaffar & Housner (ambient vibration tests, EERL
77-01, Fig. 18) are **0.234 / 0.385 / 0.835 Hz** (symmetric modes).

| Mode | beamfeapy 3D | measured (1977) | error |
|------|-------------:|----------------:|------:|
| 1st lateral | 0.141 Hz | — | — |
| 1st torsional | 0.182 Hz | — | — |
| 1st symmetric vertical | **0.254 Hz** | 0.234 Hz | +9% |
| 2nd vertical | 0.395 Hz | 0.385 Hz | +3% |

Using **the report's real sections** (no calibration), the 1st vertical mode is
within **+9%** of the measured value and the 2nd within **+3%**: the
reconstruction is faithful in geometry, stiffness and mass.

![Mode shapes: lateral, vertical, torsional](images/vt_real_modes.png)

## 6. References

- A.M. Abdel-Ghaffar, *Dynamic analyses of suspension bridge structures*, EERL
  76-01, Caltech, 1976 (structural data, Vincent Thomas example).
- A.M. Abdel-Ghaffar, G.W. Housner, *An analysis of the dynamic characteristics
  of a suspension bridge by ambient vibration measurements*, EERL 77-01, Caltech,
  1977 (measured frequencies).
- H.M. Irvine, *Cable Structures*, MIT Press, 1981.

## 7. How to regenerate

```bash
python scripts/vincent_thomas_real.py        # build model + analysis
python scripts/gen_vincent_thomas_figures.py # all figures on this page
```

See also the [Bill Emerson cable-stayed bridge](en-34-cable-stayed-emerson.html),
the [Strait of Messina Bridge](en-36-strait-of-messina-bridge.html) (3,300 m
global model) and the [cable-element theory](en-33-cables-cable-stayed-suspension.html).
