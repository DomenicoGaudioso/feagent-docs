---
layout: default
title: "39 - Benchmark report"
parent: English
nav_order: 39
---

# 39 - Benchmark report

Full report of the published benchmarks solved with feagent (run of 2026-09-08): **275 of 275 comparisons within the tolerance of the original test**. Each row gives the feagent value, the reference, the error and the tolerance; the last column carries the source and any note. Groups follow [15 - Testing and Validation](en-15-testing-validation.html); the nonlinear-solver groups are described in [40 - Nonlinear time history](en-40-nonlinear-time-history.html). Regenerate with `python validation/simis_benchmarks.py --docs`.

## Summary

| Group | Comparisons | Within tolerance | Max error [%] |
|---|---|---|---|
| Bell (1987) - cantilever HE300B | 20 | 20 | 0.5008 |
| Bell (1987) fig. 6.6 - fixed end and two supports | 8 | 8 | 0.2026 |
| Irgens (1985) - examples 1, 3, 5 | 7 | 7 | 0.5265 |
| MacNeal & Harder (1985) - straight beam | 5 | 5 | 6.2312 |
| MacNeal & Harder (1985) - curved beam | 4 | 4 | 1.3545 |
| MacNeal & Harder (1985) - twisted beam | 6 | 6 | 0.2443 |
| simis.io - static pull, one-element model | 28 | 28 | 0.0000 |
| simis.io - maximum stress | 2 | 2 | 0.0000 |
| simis.io - element weight | 4 | 4 | 0.0000 |
| simis.io - spring test | 12 | 12 | 0.0000 |
| simis.io - eigenfrequency of a circular cylinder | 2 | 2 | 0.0934 |
| simis.io - decay test tower (damping models) | 22 | 22 | 0.0543 |
| NAFEMS FV2 - pin-ended cross | 16 | 16 | 0.5525 |
| NAFEMS FV4 - cantilever with off-centre point masses | 12 | 12 | 0.3599 |
| NAFEMS FV5 - deep simply-supported beam | 18 | 18 | 4.0609 |
| simis.io - earthquake tower vs OpenSees | 33 | 33 | 0.1631 |
| simis.io - static pull tower vs OpenSees | 48 | 48 | 0.0004 |
| Nonlinear solver - elastic limit | 2 | 2 | 0.0000 |
| Nonlinear solver - bilinear isolator vs OpenSees | 5 | 5 | 0.0024 |
| Nonlinear solver - friction pendulum vs OpenSees | 5 | 5 | 0.0279 |
| Nonlinear solver - bidirectional isolator vs OpenSees | 9 | 9 | 0.0022 |
| Nonlinear solver - viscous damper vs OpenSees | 3 | 3 | 0.0028 |
| Nonlinear solver - isolated deck, energy balance | 4 | 4 | 0.0000 |

## Detailed results

### Bell (1987) - cantilever HE300B

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| l = 0.6 m (l/h = 2) EB | w_tip [m] | 0.000136217 | 0.000136217 | 0.0000 | 0.01 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; formula esatta P l^3/(3EI) |
| l = 0.6 m (l/h = 2) EB | w_tip tab. [m] | 0.000136217 | 0.000136 | 0.1593 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; valore tabulato a 3 cifre (Ashes: 0.5 % sul valore esatto) |
| l = 0.6 m (l/h = 2) Timoshenko | w_tip [m] | 0.000392374 | 0.000392374 | 0.0000 | 0.01 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; formula esatta w_E + P l/(G A_s) |
| l = 0.6 m (l/h = 2) Timoshenko | w_tip tab. [m] | 0.000392374 | 0.000392 | 0.0955 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; tabulato come sigma arrotondato x w_E (Ashes stesso devia 0.44 % a l = 3 m) |
| l = 0.6 m (l/h = 2) | sigma = w_T/w_E | 2.88052 | 2.88 | 0.0180 | 0.5 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; rapporto tabulato da Bell |
| l = 1.5 m (l/h = 5) EB | w_tip [m] | 0.00212838 | 0.00212838 | 0.0000 | 0.01 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; formula esatta P l^3/(3EI) |
| l = 1.5 m (l/h = 5) EB | w_tip tab. [m] | 0.00212838 | 0.00213 | 0.0759 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; valore tabulato a 3 cifre (Ashes: 0.5 % sul valore esatto) |
| l = 1.5 m (l/h = 5) Timoshenko | w_tip [m] | 0.00276878 | 0.00276878 | 0.0000 | 0.01 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; formula esatta w_E + P l/(G A_s) |
| l = 1.5 m (l/h = 5) Timoshenko | w_tip tab. [m] | 0.00276878 | 0.00277 | 0.0441 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; tabulato come sigma arrotondato x w_E (Ashes stesso devia 0.44 % a l = 3 m) |
| l = 1.5 m (l/h = 5) | sigma = w_T/w_E | 1.30088 | 1.3 | 0.0679 | 0.5 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; rapporto tabulato da Bell |
| l = 3 m (l/h = 10) EB | w_tip [m] | 0.0170271 | 0.0170271 | 0.0000 | 0.01 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; formula esatta P l^3/(3EI) |
| l = 3 m (l/h = 10) EB | w_tip tab. [m] | 0.0170271 | 0.017 | 0.1593 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; valore tabulato a 3 cifre (Ashes: 0.5 % sul valore esatto) |
| l = 3 m (l/h = 10) Timoshenko | w_tip [m] | 0.0183079 | 0.0183079 | 0.0000 | 0.01 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; formula esatta w_E + P l/(G A_s) |
| l = 3 m (l/h = 10) Timoshenko | w_tip tab. [m] | 0.0183079 | 0.0184 | 0.5008 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; tabulato come sigma arrotondato x w_E (Ashes stesso devia 0.44 % a l = 3 m) |
| l = 3 m (l/h = 10) | sigma = w_T/w_E | 1.07522 | 1.08 | 0.4425 | 0.5 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; rapporto tabulato da Bell |
| l = 6 m (l/h = 20) EB | w_tip [m] | 0.136217 | 0.136217 | 0.0000 | 0.01 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; formula esatta P l^3/(3EI) |
| l = 6 m (l/h = 20) EB | w_tip tab. [m] | 0.136217 | 0.136 | 0.1593 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; valore tabulato a 3 cifre (Ashes: 0.5 % sul valore esatto) |
| l = 6 m (l/h = 20) Timoshenko | w_tip [m] | 0.138778 | 0.138778 | 0.0000 | 0.01 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; formula esatta w_E + P l/(G A_s) |
| l = 6 m (l/h = 20) Timoshenko | w_tip tab. [m] | 0.138778 | 0.139 | 0.1596 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; tabulato come sigma arrotondato x w_E (Ashes stesso devia 0.44 % a l = 3 m) |
| l = 6 m (l/h = 20) | sigma = w_T/w_E | 1.01881 | 1.02 | 0.1171 | 0.5 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell Cantilever'; rapporto tabulato da Bell |

### Bell (1987) fig. 6.6 - fixed end and two supports

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| Eulero-Bernoulli | delta sotto P [m] | 4.63834e-05 | 4.64e-05 | 0.0358 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell' |
| Eulero-Bernoulli | M incastro [kNm] | 23.6842 | 23.7 | 0.0666 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell'; hogging |
| Eulero-Bernoulli | M sotto P [kNm] | 21.2171 | 21.2 | 0.0807 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell'; sagging |
| Eulero-Bernoulli | M appoggio [kNm] | 8.88158 | 8.88 | 0.0178 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell'; hogging |
| Timoshenko | delta sotto P [m] | 0.000213827 | 0.000214 | 0.0807 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell' |
| Timoshenko | M incastro [kNm] | 20.1407 | 20.1 | 0.2026 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell'; hogging |
| Timoshenko | M sotto P [kNm] | 22.5976 | 22.6 | 0.0107 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell'; sagging |
| Timoshenko | M appoggio [kNm] | 9.66412 | 9.66 | 0.0426 | 1 | OK | Bell (1987) fig. 6.6 / simis.io 'Benchmark Bell'; hogging |

### Irgens (1985) - examples 1, 3, 5

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| es. 1 cap. 19 (mensola) | u_max [m] | 0.0427736 | 0.043 | 0.5265 | 1 | OK | Irgens (1985) / simis.io 'Benchmark Irgens'; esatto F L^3/(3EI) = 0.04277 |
| es. 1 cap. 19 (mensola) | phi_max [deg] | 0.91903 | 0.92 | 0.1054 | 1 | OK | Irgens (1985) / simis.io 'Benchmark Irgens'; esatto F L^2/(2EI) = 0.9190 deg |
| es. 3 cap. 19 (appoggiata, q) | u_mezzeria [m] | 0.0167391 | 0.0168 | 0.3622 | 1 | OK | Irgens (1985) / simis.io 'Benchmark Irgens'; esatto 5 q L^4/(384 EI) = 0.01674 |
| es. 5 cap. 24 (angolare) | |d_x| [m] | 0.011427 | 0.0114 | 0.2366 | 1 | OK | Irgens (1985) / simis.io 'Benchmark Irgens'; chiusa flessione deviata 0.01143; F L^3 = 2000 N m^3 |
| es. 5 cap. 24 (angolare) | |d_z| [m] | 0.00856954 | 0.0086 | 0.3541 | 1 | OK | Irgens (1985) / simis.io 'Benchmark Irgens'; chiusa flessione deviata 0.00857 |
| es. 5 cap. 24 (angolare) | d_x chiusa [m] | -0.011427 | -0.011427 | 0.0000 | 0.01 | OK | Irgens (1985) / simis.io 'Benchmark Irgens'; assi principali via 'axes' (segno incluso) |
| es. 5 cap. 24 (angolare) | d_z chiusa [m] | -0.00856954 | -0.00856954 | 0.0000 | 0.01 | OK | Irgens (1985) / simis.io 'Benchmark Irgens' |

### MacNeal & Harder (1985) - straight beam

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| extension (Fx = 1) | u_x [m] | 3e-05 | 3e-05 | 0.0000 | 1 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Straight Beam' |
| in-plane shear (F lungo h = 0.2) | u [m] | 0.108094 | 0.1081 | 0.0059 | 1 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Straight Beam' |
| out-of-plane shear (F lungo b = 0.1) | u [m] | 0.432094 | 0.4321 | 0.0015 | 1 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Straight Beam' |
| twist (Mx = 1) | theta [rad] | 0.034079 | 0.03208 | 6.2312 | 7 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Straight Beam'; J Roark = 4.5776e-05 m^4; Ashes 1.878 deg = 0.03278 rad |
| twist (Mx = 1) | theta [deg] | 1.95258 | 1.878 | 3.9713 | 7 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Straight Beam'; riferimento come riportato da simis.io |

### MacNeal & Harder (1985) - curved beam

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| in-plane shear, 6 elementi | d_tip [m] | 0.0873465 | 0.08734 | 0.0074 | 2 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Curved Beam'; corde rettilinee sull'arco |
| out-of-plane shear, 6 elementi | d_tip [m] | 0.496746 | 0.5022 | 1.0860 | 2 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Curved Beam'; corde rettilinee sull'arco |
| in-plane shear, 24 elementi | d_tip [m] | 0.088523 | 0.08734 | 1.3545 | 2 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Curved Beam'; corde rettilinee sull'arco |
| out-of-plane shear, 24 elementi | d_tip [m] | 0.500202 | 0.5022 | 0.3979 | 2 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Curved Beam'; corde rettilinee sull'arco |

### MacNeal & Harder (1985) - twisted beam

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| in-plane (F lungo spessore), 12 elementi | d_tip [m] | -0.00542404 | -0.005424 | 0.0007 | 1 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Twisted Beam'; roll costante a tratti |
| out-of-plane (F lungo larghezza), 12 elementi | d_tip [m] | 0.00175491 | 0.001754 | 0.0518 | 1 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Twisted Beam'; roll costante a tratti |
| in-plane (F lungo spessore), 48 elementi | d_tip [m] | -0.00542899 | -0.005424 | 0.0921 | 1 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Twisted Beam'; roll costante a tratti |
| out-of-plane (F lungo larghezza), 48 elementi | d_tip [m] | 0.00174995 | 0.001754 | 0.2308 | 1 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Twisted Beam'; roll costante a tratti |
| in-plane (F lungo spessore), 91 elementi | d_tip [m] | -0.00542923 | -0.005424 | 0.0964 | 1 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Twisted Beam'; roll costante a tratti |
| out-of-plane (F lungo larghezza), 91 elementi | d_tip [m] | 0.00174971 | 0.001754 | 0.2443 | 1 | OK | MacNeal & Harder (1985) / simis.io 'Benchmark MacNeal Twisted Beam'; roll costante a tratti |

### simis.io - static pull, one-element model

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| 1 tubo, Fx | u [m] | 0.0260333 | 0.0260333 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.0260 |
| 1 tubo, Fx | sigma_max [MPa] | 164.01 | 164.01 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 164 MPa |
| 2 tubo, Fy | u [m] | 0.0260333 | 0.0260333 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.0260 |
| 2 tubo, Fy | sigma_max [MPa] | 164.01 | 164.01 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 164 MPa |
| 3 tubo, F a 45 deg | u_x [m] | 0.0184083 | 0.0184083 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.0184 |
| 3 tubo, F a 45 deg | u_y [m] | 0.0184083 | 0.0184083 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.0184 |
| 3 tubo, F a 45 deg | sigma_max [MPa] | 164.01 | 164.01 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); come caso 1; 16 punti di recupero sulla circonferenza (i 4 di default stanno a 0 e 90 deg) |
| 4 tubo, Fz = 100 MN | u_z [m] | 0.0382768 | 0.0382768 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 3.828e-2 |
| 4 tubo, Fz = 100 MN | sigma [MPa] | 803.813 | 803.813 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 803.9 MPa |
| 5 tubo, Mz = 1 MNm | twist [deg] | 0.0581724 | 0.0581724 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 5.819e-2 deg |
| 6 tubo 20 m, Fx | u [m] | 0.208267 | 0.208267 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.2082 |
| 6 tubo 20 m, Fx | sigma_max [MPa] | 328.02 | 328.02 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 328 MPa |
| 7 tubo 20 m, Fz | u_z [m] | 0.0765536 | 0.0765536 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 76.80 mm |
| 8 tubo 20 m, Mz | twist [deg] | 0.116345 | 0.116345 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.1163 deg |
| 9 tubo Timoshenko, Fx | u [m] | 0.0280237 | 0.0280237 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.02802 |
| 10 scatolare, Fx (lato 3 m) | u_x [m] | 0.00905719 | 0.00905719 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 9.057e-3 |
| 10 scatolare, Fx (lato 3 m) | sigma_max [MPa] | 85.5905 | 85.5905 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 85.55 MPa |
| 11 scatolare, Fy (lato 1 m) | u_y [m] | 0.04997 | 0.04997 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.0500 |
| 11 scatolare, Fy (lato 1 m) | sigma_max [MPa] | 157.405 | 157.405 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 157.5 MPa |
| 12 scatolare ruotato 90 deg, Fx | u_x [m] | 0.04997 | 0.04997 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); = caso 11 |
| 12 scatolare ruotato 90 deg, Fx | sigma_max [MPa] | 157.405 | 157.405 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 157.5 MPa |
| 13 scatolare ruotato 30 deg, Fx | u_x [m] | 0.0192854 | 0.0192854 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.0193 |
| 13 scatolare ruotato 30 deg, Fx | |u_y| [m] | 0.0177157 | 0.0177157 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.0177 |
| 13 scatolare ruotato 30 deg, Fx | sigma_max [MPa] | 152.826 | 152.826 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 153 MPa |
| 14 rigidezze, Fx | u_x [m] | 0.0333333 | 0.0333333 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico) |
| 15 rigidezze, Fy | u_y [m] | 0.0333333 | 0.0333333 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico) |
| 16 rigidezze, Fz | u_z [m] | 0.04 | 0.04 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico) |
| 17 rigidezze, Mz | twist [deg] | 0.0572958 | 0.0572958 | 0.0000 | 0.1 | OK | simis.io 'Static pull: one-element model' (analitico); 0.0573 deg |

### simis.io - maximum stress

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| torre 100 m | sigma_max [MPa] | 138.686 | 138.686 | 0.0000 | 0.1 | OK | simis.io 'Maximum stress' (analitico); 138 MPa |
| torre 100 m | freccia [m] | 1.13766 | 1.13766 | 0.0000 | 0.1 | OK | simis.io 'Maximum stress' (analitico); 1.13 m |

### simis.io - element weight

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| tubo r = 2.5 m, t = 0.2 m | R_z [kN] | 2513.97 | 2513.97 | 0.0000 | 0.01 | OK | simis.io 'Element weight' (analitico); W = 2513 kN |
| scatolare 5 x 1 x 0.2 m | R_z [kN] | 1867.19 | 1867.19 | 0.0000 | 0.01 | OK | simis.io 'Element weight' (analitico); W = 1868 kN |
| cerchio pieno r = 2.5 m | R_z [kN] | 16367 | 16367 | 0.0000 | 0.01 | OK | simis.io 'Element weight' (analitico); W = 16366 kN |
| tubo r = 2.5 m su Marte (g = 3.72076) | R_z [kN] | 953.832 | 953.832 | 0.0000 | 0.01 | OK | simis.io 'Element weight' (analitico); W = 954 kN |

### simis.io - spring test

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| Fx = 100 kN | ux [m] | 0.02 | 0.02 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico) |
| Fx = -200 kN | ux [m] | -0.04 | -0.04 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico) |
| Fy = 100 kN | uy [m] | 0.0166667 | 0.0166667 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico) |
| Fy = -200 kN | uy [m] | -0.0333333 | -0.0333333 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico) |
| Fz = 10 kN | uz [m] | 0.01 | 0.01 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico) |
| Fz = -25 kN | uz [m] | -0.025 | -0.025 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico) |
| Fx, Fy, Fz = 200, 100, -25 kN (u) | ux [m] | 0.04 | 0.04 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico) |
| Fx, Fy, Fz = 200, 100, -25 kN (v) | uy [m] | 0.02 | 0.02 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico) |
| Fx, Fy, Fz = 200, 100, -25 kN (w) | uz [m] | -0.025 | -0.025 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico) |
| Mx = 10 MNm | rx [deg] | 0.0114592 | 0.0114592 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico); 0.01146 deg |
| My = 10 MNm | ry [deg] | 0.0114592 | 0.0114592 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico); 0.01146 deg |
| Mz = 10 MNm | rz [deg] | 0.0114592 | 0.0114592 | 0.0000 | 0.01 | OK | simis.io 'Spring test' (analitico); 0.01146 deg |

### simis.io - eigenfrequency of a circular cylinder

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| 22 elementi, massa lumped | f1 [Hz] | 6.74769 | 6.754 | 0.0934 | 0.1 | OK | simis.io 'Eigenfrequency circular cylinder' (analitico); esatto 6.7541 Hz |
| 22 elementi, massa consistent | f1 [Hz] | 6.75409 | 6.754 | 0.0013 | 0.1 | OK | simis.io 'Eigenfrequency circular cylinder' (analitico); esatto 6.7541 Hz |

### simis.io - decay test tower (damping models)

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| modale | T1 (Y) [s] | 3.11059 | 3.109 | 0.0511 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); cantilever: beta_1 l = 1.8751 |
| modale | T2 (X) [s] | 1.53583 | 1.535 | 0.0543 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato) |
| 1 nessuno smorzamento | u_max [m] | 0.495065 | 0.495065 | 0.0000 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); xi = 0.00 % |
| 1 nessuno smorzamento | periodo [s] | 3.11061 | 3.11059 | 0.0009 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); analitico 2 pi / w_d = 3.1106 |
| 2 massa (xi1 = 1 %) | u_max [m] | 0.487393 | 0.487393 | 0.0000 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); xi = 1.00 % |
| 2 massa (xi1 = 1 %) | periodo [s] | 3.11077 | 3.11074 | 0.0008 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); analitico 2 pi / w_d = 3.1107 |
| 3 rigidezza (xi1 = 1 %) | u_max [m] | 0.487393 | 0.487393 | 0.0000 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); xi = 1.00 % |
| 3 rigidezza (xi1 = 1 %) | periodo [s] | 3.11077 | 3.11074 | 0.0009 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); analitico 2 pi / w_d = 3.1107 |
| 4 massa, 2o modo (xi2 = 0.49 %) | u_max [m] | 0.242543 | 0.242543 | 0.0001 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); xi = 0.49 % |
| 4 massa, 2o modo (xi2 = 0.49 %) | periodo [s] | 1.53591 | 1.53585 | 0.0035 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); analitico 2 pi / w_d = 1.5359 |
| 5 rigidezza, 2o modo (xi2 = 2.0 %) | u_max [m] | 0.236876 | 0.236875 | 0.0002 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); xi = 2.03 % |
| 5 rigidezza, 2o modo (xi2 = 2.0 %) | periodo [s] | 1.5362 | 1.53615 | 0.0035 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); analitico 2 pi / w_d = 1.5361 |
| 6 Rayleigh 1o modo (xi1 = 1.02 %) | u_max [m] | 0.487219 | 0.487219 | 0.0000 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); xi = 1.02 % |
| 6 Rayleigh 1o modo (xi1 = 1.02 %) | periodo [s] | 3.11078 | 3.11075 | 0.0008 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); analitico 2 pi / w_d = 3.1108 |
| 7 Rayleigh 2o modo (xi2 = 0.79 %) | u_max [m] | 0.241433 | 0.241433 | 0.0000 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); xi = 0.79 % |
| 7 Rayleigh 2o modo (xi2 = 0.79 %) | periodo [s] | 1.53594 | 1.53588 | 0.0035 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); analitico 2 pi / w_d = 1.5359 |
| 8 massa mu = 0.05 (xi1 = 1.23 %) | u_max [m] | 0.485605 | 0.485605 | 0.0000 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); xi = 1.24 % |
| 8 massa mu = 0.05 (xi1 = 1.23 %) | periodo [s] | 3.11085 | 3.11083 | 0.0008 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); analitico 2 pi / w_d = 3.1108 |
| 9 rigidezza lambda = 0.05 (xi1 = 5.05 %) | u_max [m] | 0.458435 | 0.458434 | 0.0001 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); xi = 5.05 % |
| 9 rigidezza lambda = 0.05 (xi1 = 5.05 %) | periodo [s] | 3.11459 | 3.11456 | 0.0008 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); analitico 2 pi / w_d = 3.1146 |
| 10 Rayleigh mu = lambda = 0.05 (xi1 = 6.29 %) | u_max [m] | 0.450198 | 0.450197 | 0.0002 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); xi = 6.29 % |
| 10 Rayleigh mu = lambda = 0.05 (xi1 = 6.29 %) | periodo [s] | 3.11678 | 3.11676 | 0.0008 | 1 | OK | simis.io 'Decay test tower' (Biggs 1964, oscillatore smorzato); analitico 2 pi / w_d = 3.1168 |

### NAFEMS FV2 - pin-ended cross

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| massa lumped, 16 el./braccio | f1 [Hz] | 11.3362 | 11.336 | 0.0021 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa lumped, 16 el./braccio | f2 [Hz] | 17.6807 | 17.709 | 0.1597 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa lumped, 16 el./braccio | f3 [Hz] | 17.6807 | 17.709 | 0.1597 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa lumped, 16 el./braccio | f4 [Hz] | 17.7093 | 17.709 | 0.0019 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa lumped, 16 el./braccio | f5 [Hz] | 45.3442 | 45.345 | 0.0017 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa lumped, 16 el./braccio | f6 [Hz] | 57.0729 | 57.39 | 0.5525 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa lumped, 16 el./braccio | f7 [Hz] | 57.0729 | 57.39 | 0.5525 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa lumped, 16 el./braccio | f8 [Hz] | 57.3881 | 57.39 | 0.0034 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa consistent, 16 el./braccio | f1 [Hz] | 11.3363 | 11.336 | 0.0023 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa consistent, 16 el./braccio | f2 [Hz] | 17.6808 | 17.709 | 0.1592 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa consistent, 16 el./braccio | f3 [Hz] | 17.6808 | 17.709 | 0.1592 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa consistent, 16 el./braccio | f4 [Hz] | 17.7094 | 17.709 | 0.0024 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa consistent, 16 el./braccio | f5 [Hz] | 45.3457 | 45.345 | 0.0016 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa consistent, 16 el./braccio | f6 [Hz] | 57.076 | 57.39 | 0.5470 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa consistent, 16 el./braccio | f7 [Hz] | 57.076 | 57.39 | 0.5470 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |
| massa consistent, 16 el./braccio | f8 [Hz] | 57.3912 | 57.39 | 0.0022 | 1 | OK | NAFEMS FV2 (Abbassian, Dawswell, Knowles 1987) 'Pin-ended cross' |

### NAFEMS FV4 - cantilever with off-centre point masses

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| massa lumped, 10 elementi | f1 [Hz] | 1.72112 | 1.723 | 0.1089 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa lumped, 10 elementi | f2 [Hz] | 1.72487 | 1.727 | 0.1231 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa lumped, 10 elementi | f3 [Hz] | 7.43335 | 7.413 | 0.2746 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa lumped, 10 elementi | f4 [Hz] | 9.97465 | 9.972 | 0.0266 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa lumped, 10 elementi | f5 [Hz] | 18.0897 | 18.155 | 0.3599 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa lumped, 10 elementi | f6 [Hz] | 27.0055 | 26.957 | 0.1801 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa consistent, 10 elementi | f1 [Hz] | 1.72328 | 1.723 | 0.0162 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa consistent, 10 elementi | f2 [Hz] | 1.72684 | 1.727 | 0.0091 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa consistent, 10 elementi | f3 [Hz] | 7.4134 | 7.413 | 0.0053 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa consistent, 10 elementi | f4 [Hz] | 9.97479 | 9.972 | 0.0280 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa consistent, 10 elementi | f5 [Hz] | 18.1819 | 18.155 | 0.1482 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |
| massa consistent, 10 elementi | f6 [Hz] | 26.9864 | 26.957 | 0.1092 | 1 | OK | NAFEMS FV4 (TNSB rev. 3, 1990) 'Cantilever with off-centre point masses'; masse su bracci rigidi (add_rigid_link) |

### NAFEMS FV5 - deep simply-supported beam

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| Timoshenko + massa consistente con inerzia rotazionale, 20 elementi | f1 (flessione) [Hz] | 42.6111 | 42.65 | 0.0913 | 2 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A |
| Timoshenko + massa consistente con inerzia rotazionale, 20 elementi | f2 (flessione) [Hz] | 42.6111 | 42.65 | 0.0913 | 2 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A |
| Timoshenko + massa consistente con inerzia rotazionale, 20 elementi | f3 (torsione) [Hz] | 71.2793 | 71.2 | 0.1113 | 2 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A |
| Timoshenko + massa consistente con inerzia rotazionale, 20 elementi | f4 (assiale) [Hz] | 125.032 | 125 | 0.0257 | 2 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A |
| Timoshenko + massa consistente con inerzia rotazionale, 20 elementi | f5 (flessione) [Hz] | 147.88 | 148.15 | 0.1820 | 2 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A |
| Timoshenko + massa consistente con inerzia rotazionale, 20 elementi | f6 (flessione) [Hz] | 147.88 | 148.15 | 0.1820 | 2 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A |
| Timoshenko + massa consistente con inerzia rotazionale, 20 elementi | f7 (torsione) [Hz] | 214.278 | 213.61 | 0.3126 | 2 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A |
| Timoshenko + massa consistente con inerzia rotazionale, 20 elementi | f8 (flessione) [Hz] | 283.003 | 283.47 | 0.1648 | 2 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A |
| Timoshenko + massa consistente con inerzia rotazionale, 20 elementi | f9 (flessione) [Hz] | 283.003 | 283.47 | 0.1648 | 2 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A |
| Timoshenko + massa consistente senza inerzia rotazionale, 20 elementi | f1 (flessione) [Hz] | 43.1854 | 42.65 | 1.2552 | 5 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A; senza inerzia rotazionale le flessioni salgono (atteso) |
| Timoshenko + massa consistente senza inerzia rotazionale, 20 elementi | f2 (flessione) [Hz] | 43.1854 | 42.65 | 1.2552 | 5 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A; senza inerzia rotazionale le flessioni salgono (atteso) |
| Timoshenko + massa consistente senza inerzia rotazionale, 20 elementi | f3 (torsione) [Hz] | 71.2793 | 71.2 | 0.1113 | 5 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A; senza inerzia rotazionale le flessioni salgono (atteso) |
| Timoshenko + massa consistente senza inerzia rotazionale, 20 elementi | f4 (assiale) [Hz] | 125.032 | 125 | 0.0257 | 5 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A; senza inerzia rotazionale le flessioni salgono (atteso) |
| Timoshenko + massa consistente senza inerzia rotazionale, 20 elementi | f5 (flessione) [Hz] | 152.825 | 148.15 | 3.1558 | 5 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A; senza inerzia rotazionale le flessioni salgono (atteso) |
| Timoshenko + massa consistente senza inerzia rotazionale, 20 elementi | f6 (flessione) [Hz] | 152.825 | 148.15 | 3.1558 | 5 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A; senza inerzia rotazionale le flessioni salgono (atteso) |
| Timoshenko + massa consistente senza inerzia rotazionale, 20 elementi | f7 (torsione) [Hz] | 214.278 | 213.61 | 0.3126 | 5 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A; senza inerzia rotazionale le flessioni salgono (atteso) |
| Timoshenko + massa consistente senza inerzia rotazionale, 20 elementi | f8 (flessione) [Hz] | 294.981 | 283.47 | 4.0609 | 5 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A; senza inerzia rotazionale le flessioni salgono (atteso) |
| Timoshenko + massa consistente senza inerzia rotazionale, 20 elementi | f9 (flessione) [Hz] | 294.981 | 283.47 | 4.0609 | 5 | OK | NAFEMS FV5 (R0016, 1993) 'Deep simply-supported beam'; A_s = 5/6 A; senza inerzia rotazionale le flessioni salgono (atteso) |

### simis.io - earthquake tower vs OpenSees

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| modale | T1 [s] | 4.38758 | 4.39 | 0.0551 | 1 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); periodo dichiarato da simis.io |
| 1 base costante 1 m/s^2 X | u_top rel. [m] max | 1.07829 | 1.07828 | 0.0012 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); gradino a t = 0: OpenSees parte con a0 = 0, feagent ricava a0 dal carico |
| 1 base costante 1 m/s^2 X | u_top rel. [m] RMS/max [%] | 0.0863694 | 0 | 0.0864 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 1 base costante 1 m/s^2 X | a_top rel. [m/s^2] max | 1.20824 | 1.20814 | 0.0086 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 1 base costante 1 m/s^2 X | a_top rel. [m/s^2] RMS/max [%] | 0.16312 | 0 | 0.1631 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 1 base costante 1 m/s^2 X | V_base [N] max | 729072 | 729068 | 0.0006 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 1 base costante 1 m/s^2 X | V_base [N] RMS/max [%] | 0.0759637 | 0 | 0.0760 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 1 base costante 1 m/s^2 X | M_base [Nm] max | 5.53021e+07 | 5.53018e+07 | 0.0006 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 1 base costante 1 m/s^2 X | M_base [Nm] RMS/max [%] | 0.0829499 | 0 | 0.0829 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 2 base costante 0.5 m/s^2 Y | u_top rel. [m] max | 0.539147 | 0.539142 | 0.0008 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); gradino a t = 0: OpenSees parte con a0 = 0, feagent ricava a0 dal carico |
| 2 base costante 0.5 m/s^2 Y | u_top rel. [m] RMS/max [%] | 0.0863685 | 0 | 0.0864 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 2 base costante 0.5 m/s^2 Y | a_top rel. [m/s^2] max | 0.604122 | 0.604071 | 0.0084 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 2 base costante 0.5 m/s^2 Y | a_top rel. [m/s^2] RMS/max [%] | 0.16312 | 0 | 0.1631 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 2 base costante 0.5 m/s^2 Y | V_base [N] max | 364536 | 364534 | 0.0006 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 2 base costante 0.5 m/s^2 Y | V_base [N] RMS/max [%] | 0.0759649 | 0 | 0.0760 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 2 base costante 0.5 m/s^2 Y | M_base [Nm] max | 2.76511e+07 | 2.76509e+07 | 0.0006 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 2 base costante 0.5 m/s^2 Y | M_base [Nm] RMS/max [%] | 0.0829501 | 0 | 0.0830 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 3 base sinusoidale 10 m/s^2, T = 3 s, X | u_top rel. [m] max | 10.0672 | 10.0672 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 3 base sinusoidale 10 m/s^2, T = 3 s, X | u_top rel. [m] RMS/max [%] | 2.83557e-05 | 0 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 3 base sinusoidale 10 m/s^2, T = 3 s, X | a_top rel. [m/s^2] max | 32.5484 | 32.5484 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 3 base sinusoidale 10 m/s^2, T = 3 s, X | a_top rel. [m/s^2] RMS/max [%] | 7.38796e-05 | 0 | 0.0001 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 3 base sinusoidale 10 m/s^2, T = 3 s, X | V_base [N] max | 4.64782e+06 | 4.64782e+06 | 0.0001 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 3 base sinusoidale 10 m/s^2, T = 3 s, X | V_base [N] RMS/max [%] | 4.69235e-05 | 0 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 3 base sinusoidale 10 m/s^2, T = 3 s, X | M_base [Nm] max | 4.7394e+08 | 4.7394e+08 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 3 base sinusoidale 10 m/s^2, T = 3 s, X | M_base [Nm] RMS/max [%] | 5.12659e-05 | 0 | 0.0001 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 4 base sinusoidale 5 m/s^2, T = 1 s, Y | u_top rel. [m] max | 0.764639 | 0.764639 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 4 base sinusoidale 5 m/s^2, T = 1 s, Y | u_top rel. [m] RMS/max [%] | 2.98759e-05 | 0 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 4 base sinusoidale 5 m/s^2, T = 1 s, Y | a_top rel. [m/s^2] max | 7.93783 | 7.93783 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 4 base sinusoidale 5 m/s^2, T = 1 s, Y | a_top rel. [m/s^2] RMS/max [%] | 3.48359e-05 | 0 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 4 base sinusoidale 5 m/s^2, T = 1 s, Y | V_base [N] max | 947041 | 947041 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 4 base sinusoidale 5 m/s^2, T = 1 s, Y | V_base [N] RMS/max [%] | 2.85974e-05 | 0 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 4 base sinusoidale 5 m/s^2, T = 1 s, Y | M_base [Nm] max | 3.60542e+07 | 3.60542e+07 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente) |
| 4 base sinusoidale 5 m/s^2, T = 1 s, Y | M_base [Nm] RMS/max [%] | 3.48926e-05 | 0 | 0.0000 | 0.5 | OK | simis.io 'Earthquake Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |

### simis.io - static pull tower vs OpenSees

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| 1 senza massa in testa, nessuno | u_top [m] max | 2.21329 | 2.21329 | 0.0001 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 1 senza massa in testa, nessuno | u_top [m] RMS/max [%] | 0.000114946 | 0 | 0.0001 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 1 senza massa in testa, nessuno | a_top [m/s^2] max | 52.1594 | 52.1594 | 0.0000 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 1 senza massa in testa, nessuno | a_top [m/s^2] RMS/max [%] | 2.89633e-05 | 0 | 0.0000 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 1 senza massa in testa, nessuno | M_base [Nm] max | 1.39122e+08 | 1.39122e+08 | 0.0003 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 1 senza massa in testa, nessuno | M_base [Nm] RMS/max [%] | 0.000116871 | 0 | 0.0001 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 2 senza massa in testa, rigidezza xi1 = 5 % | u_top [m] max | 2.21792 | 2.21792 | 0.0002 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 2 senza massa in testa, rigidezza xi1 = 5 % | u_top [m] RMS/max [%] | 8.95241e-05 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 2 senza massa in testa, rigidezza xi1 = 5 % | a_top [m/s^2] max | 37.4629 | 37.4629 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 2 senza massa in testa, rigidezza xi1 = 5 % | a_top [m/s^2] RMS/max [%] | 7.62476e-06 | 0 | 0.0000 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 2 senza massa in testa, rigidezza xi1 = 5 % | M_base [Nm] max | 1.08339e+08 | 1.08339e+08 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 2 senza massa in testa, rigidezza xi1 = 5 % | M_base [Nm] RMS/max [%] | 0.000114788 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 3 senza massa in testa, massa xi1 = 5 % | u_top [m] max | 2.21808 | 2.21808 | 0.0000 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 3 senza massa in testa, massa xi1 = 5 % | u_top [m] RMS/max [%] | 8.88977e-05 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 3 senza massa in testa, massa xi1 = 5 % | a_top [m/s^2] max | 52.1813 | 52.1813 | 0.0000 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 3 senza massa in testa, massa xi1 = 5 % | a_top [m/s^2] RMS/max [%] | 1.28236e-05 | 0 | 0.0000 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 3 senza massa in testa, massa xi1 = 5 % | M_base [Nm] max | 1.21568e+08 | 1.21568e+08 | 0.0004 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 3 senza massa in testa, massa xi1 = 5 % | M_base [Nm] RMS/max [%] | 0.000101664 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 4 senza massa in testa, Rayleigh xi = 5 % (modi 1, 2) | u_top [m] max | 2.21798 | 2.21798 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 4 senza massa in testa, Rayleigh xi = 5 % (modi 1, 2) | u_top [m] RMS/max [%] | 8.9689e-05 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 4 senza massa in testa, Rayleigh xi = 5 % (modi 1, 2) | a_top [m/s^2] max | 41.5726 | 41.5726 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 4 senza massa in testa, Rayleigh xi = 5 % (modi 1, 2) | a_top [m/s^2] RMS/max [%] | 7.96735e-06 | 0 | 0.0000 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 4 senza massa in testa, Rayleigh xi = 5 % (modi 1, 2) | M_base [Nm] max | 1.12747e+08 | 1.12747e+08 | 0.0003 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 4 senza massa in testa, Rayleigh xi = 5 % (modi 1, 2) | M_base [Nm] RMS/max [%] | 0.000109795 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 5 massa in testa 150 t, nessuno | u_top [m] max | 2.3837 | 2.3837 | 0.0002 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 5 massa in testa 150 t, nessuno | u_top [m] RMS/max [%] | 0.000105764 | 0 | 0.0001 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 5 massa in testa 150 t, nessuno | a_top [m/s^2] max | 6.04838 | 6.04838 | 0.0001 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 5 massa in testa 150 t, nessuno | a_top [m/s^2] RMS/max [%] | 3.11462e-05 | 0 | 0.0000 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 5 massa in testa 150 t, nessuno | M_base [Nm] max | 1.16592e+08 | 1.16592e+08 | 0.0001 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 5 massa in testa 150 t, nessuno | M_base [Nm] RMS/max [%] | 0.000136925 | 0 | 0.0001 | 20 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 6 massa in testa 150 t, rigidezza xi1 = 5 % | u_top [m] max | 2.31328 | 2.31328 | 0.0002 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 6 massa in testa 150 t, rigidezza xi1 = 5 % | u_top [m] RMS/max [%] | 8.85287e-05 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 6 massa in testa 150 t, rigidezza xi1 = 5 % | a_top [m/s^2] max | 5.58439 | 5.58439 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 6 massa in testa 150 t, rigidezza xi1 = 5 % | a_top [m/s^2] RMS/max [%] | 2.43659e-05 | 0 | 0.0000 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 6 massa in testa 150 t, rigidezza xi1 = 5 % | M_base [Nm] max | 1.07849e+08 | 1.07849e+08 | 0.0000 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 6 massa in testa 150 t, rigidezza xi1 = 5 % | M_base [Nm] RMS/max [%] | 0.00011677 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 7 massa in testa 150 t, massa xi1 = 5 % | u_top [m] max | 2.31322 | 2.31322 | 0.0002 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 7 massa in testa 150 t, massa xi1 = 5 % | u_top [m] RMS/max [%] | 8.83775e-05 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 7 massa in testa 150 t, massa xi1 = 5 % | a_top [m/s^2] max | 5.91355 | 5.91355 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 7 massa in testa 150 t, massa xi1 = 5 % | a_top [m/s^2] RMS/max [%] | 2.29716e-05 | 0 | 0.0000 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 7 massa in testa 150 t, massa xi1 = 5 % | M_base [Nm] max | 1.07904e+08 | 1.07904e+08 | 0.0002 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 7 massa in testa 150 t, massa xi1 = 5 % | M_base [Nm] RMS/max [%] | 0.000117836 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 8 massa in testa 150 t, Rayleigh xi = 5 % (modi 1, 2) | u_top [m] max | 2.31328 | 2.31328 | 0.0002 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 8 massa in testa 150 t, Rayleigh xi = 5 % (modi 1, 2) | u_top [m] RMS/max [%] | 8.86977e-05 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 8 massa in testa 150 t, Rayleigh xi = 5 % (modi 1, 2) | a_top [m/s^2] max | 5.68675 | 5.68675 | 0.0000 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 8 massa in testa 150 t, Rayleigh xi = 5 % (modi 1, 2) | a_top [m/s^2] RMS/max [%] | 2.40074e-05 | 0 | 0.0000 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |
| 8 massa in testa 150 t, Rayleigh xi = 5 % (modi 1, 2) | M_base [Nm] max | 1.0785e+08 | 1.0785e+08 | 0.0003 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente) |
| 8 massa in testa 150 t, Rayleigh xi = 5 % (modi 1, 2) | M_base [Nm] RMS/max [%] | 0.000116882 | 0 | 0.0001 | 5 | OK | simis.io 'Static pull Opensees' (OpenSees eseguito localmente); errore RMS normalizzato al picco |

### Nonlinear solver - elastic limit

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| Fy = 1e15 N | max |u_nl - u_lin| / u_max [%] | 2.97829e-10 | 0 | 0.0000 | 1e-08 | OK | feagent lineare (solve_time_history) con appoggio elastico k1 |
| Fy = 1e15 N | iterazioni max | 2 | 2 | 0.0000 | 0 | OK | feagent lineare (solve_time_history) con appoggio elastico k1; Newton converge al 2o passaggio (residuo nullo) |

### Nonlinear solver - bilinear isolator vs OpenSees

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| LRB bilineare, EC8 suolo C | u [m] max | 0.0898048 | 0.0898039 | 0.0010 | 0.5 | OK | OpenSees zeroLength + Steel01 (incrudimento cinematico) |
| LRB bilineare, EC8 suolo C | u [m] RMS/max [%] | 0.000814065 | 0 | 0.0008 | 0.5 | OK | OpenSees zeroLength + Steel01 (incrudimento cinematico); errore RMS normalizzato al picco |
| LRB bilineare, EC8 suolo C | F [N] max | 89902.4 | 89902 | 0.0004 | 0.5 | OK | OpenSees zeroLength + Steel01 (incrudimento cinematico) |
| LRB bilineare, EC8 suolo C | F [N] RMS/max [%] | 0.0023684 | 0 | 0.0024 | 0.5 | OK | OpenSees zeroLength + Steel01 (incrudimento cinematico); errore RMS normalizzato al picco |
| LRB bilineare, EC8 suolo C | iterazioni max | 3 | 3 | 0.0000 | 0 | OK | OpenSees zeroLength + Steel01 (incrudimento cinematico); convergenza OK; u_max = 0.0898 m |

### Nonlinear solver - friction pendulum vs OpenSees

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| FPS R = 3 m, mu = 5 % | u [m] max | 0.091937 | 0.0919368 | 0.0002 | 0.5 | OK | OpenSees zeroLength + Steel01 (FPS come bilineare rigido-plastico) |
| FPS R = 3 m, mu = 5 % | u [m] RMS/max [%] | 0.000308074 | 0 | 0.0003 | 0.5 | OK | OpenSees zeroLength + Steel01 (FPS come bilineare rigido-plastico); errore RMS normalizzato al picco |
| FPS R = 3 m, mu = 5 % | F [N] max | 78949.9 | 78949.8 | 0.0001 | 0.5 | OK | OpenSees zeroLength + Steel01 (FPS come bilineare rigido-plastico) |
| FPS R = 3 m, mu = 5 % | F [N] RMS/max [%] | 0.0278756 | 0 | 0.0279 | 0.5 | OK | OpenSees zeroLength + Steel01 (FPS come bilineare rigido-plastico); errore RMS normalizzato al picco |
| FPS R = 3 m, mu = 5 % | periodo pendolo [s] | 3.47461 | 3.47461 | 0.0000 | 0.01 | OK | OpenSees zeroLength + Steel01 (FPS come bilineare rigido-plastico); u_max = 0.0919 m, E_diss = 34234 J |

### Nonlinear solver - bidirectional isolator vs OpenSees

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| isolatore accoppiato, 2 componenti | u_x [m] max | 0.0964465 | 0.0964457 | 0.0009 | 0.5 | OK | OpenSees elastomericBearingPlasticity (Park-Wen-Ang bidirezionale) |
| isolatore accoppiato, 2 componenti | u_x [m] RMS/max [%] | 0.000527572 | 0 | 0.0005 | 0.5 | OK | OpenSees elastomericBearingPlasticity (Park-Wen-Ang bidirezionale); errore RMS normalizzato al picco |
| isolatore accoppiato, 2 componenti | u_y [m] max | 0.0697774 | 0.0697774 | 0.0000 | 0.5 | OK | OpenSees elastomericBearingPlasticity (Park-Wen-Ang bidirezionale) |
| isolatore accoppiato, 2 componenti | u_y [m] RMS/max [%] | 0.00123837 | 0 | 0.0012 | 0.5 | OK | OpenSees elastomericBearingPlasticity (Park-Wen-Ang bidirezionale); errore RMS normalizzato al picco |
| isolatore accoppiato, 2 componenti | F_x [N] max | 85646.2 | 85645.8 | 0.0004 | 0.5 | OK | OpenSees elastomericBearingPlasticity (Park-Wen-Ang bidirezionale) |
| isolatore accoppiato, 2 componenti | F_x [N] RMS/max [%] | 0.00151261 | 0 | 0.0015 | 0.5 | OK | OpenSees elastomericBearingPlasticity (Park-Wen-Ang bidirezionale); errore RMS normalizzato al picco |
| isolatore accoppiato, 2 componenti | F_y [N] max | 78143.4 | 78143.4 | 0.0001 | 0.5 | OK | OpenSees elastomericBearingPlasticity (Park-Wen-Ang bidirezionale) |
| isolatore accoppiato, 2 componenti | F_y [N] RMS/max [%] | 0.00223023 | 0 | 0.0022 | 0.5 | OK | OpenSees elastomericBearingPlasticity (Park-Wen-Ang bidirezionale); errore RMS normalizzato al picco |
| isolatore accoppiato, 2 componenti | iterazioni max | 3 | 3 | 0.0000 | 0 | OK | OpenSees elastomericBearingPlasticity (Park-Wen-Ang bidirezionale); convergenza OK; d_max = 0.1113 m |

### Nonlinear solver - viscous damper vs OpenSees

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| molla + dissipatore alpha = 0.5 | u [m] max | 0.0352362 | 0.0352362 | 0.0000 | 1 | OK | OpenSees zeroLength + Viscous (F = c |v|^alpha); OpenSees regolarizza |v|^alpha a modo suo vicino a v = 0 |
| molla + dissipatore alpha = 0.5 | u [m] RMS/max [%] | 0.00281641 | 0 | 0.0028 | 1 | OK | OpenSees zeroLength + Viscous (F = c |v|^alpha); errore RMS normalizzato al picco |
| molla + dissipatore alpha = 0.5 | iterazioni max | 5 | 5 | 0.0000 | 0 | OK | OpenSees zeroLength + Viscous (F = c |v|^alpha); convergenza OK |

### Nonlinear solver - isolated deck, energy balance

| Case | Quantity | feagent | Reference | Error [%] | Tol. [%] | Result | Source / note |
|---|---|---|---|---|---|---|---|
| 2 pendoli, 2 componenti EC8 | bilancio energetico [%] | 2.87222e-12 | 0 | 0.0000 | 1 | OK | bilancio energetico (Uang & Bertero 1990) e equilibrio dinamico; E_in = 336411 J: E_k 57, E_s 16, E_d 809, E_h 335529 |
| 2 pendoli, 2 componenti EC8 | iterazioni max | 4 | 4 | 0.0000 | 0 | OK | bilancio energetico (Uang & Bertero 1990) e equilibrio dinamico; convergenza OK; d_max pendoli = 0.1305 m |
| 2 pendoli, 2 componenti EC8 | F_x pendoli vs -M a_abs [N] max | 446966 | 446966 | 0.0000 | 2 | OK | bilancio energetico (Uang & Bertero 1990) e equilibrio dinamico; impalcato rigido in direzione assiale |
| 2 pendoli, 2 componenti EC8 | F_x pendoli vs -M a_abs [N] RMS/max [%] | 6.50086e-11 | 0 | 0.0000 | 2 | OK | bilancio energetico (Uang & Bertero 1990) e equilibrio dinamico; errore RMS normalizzato al picco |

