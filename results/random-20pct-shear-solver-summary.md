# Random 20% GnP — Full-RVE XY Shear Solver Summary

Source: ANSYS Mechanical Enterprise Academic Research, MAPDL 2026 R1; original run dated **9 November 2026**. This is a transcription of relevant results from the supplied solver output, excluding computer-specific paths and license boilerplate.

## Model and solver

| Item | Recorded value |
|---|---|
| Workbench analysis name | Random_20pct_GnP_RVE_Full_Shear |
| Type | 3-D static structural, periodic XY shear |
| Solver | Distributed PCG; 4 parallel processes |
| X bounds | 0 to 2.08e-6 m |
| Y bounds | 0 to 8.32e-6 m |
| Z bounds | -14.4e-6 to 0 m |
| Full bounding cell | 2.08 × 8.32 × 14.40 µm |
| Number of nodes | 1,048,906 |
| Solid elements | 249,879 SOLID186 |
| Body count | 11 |
| Applied engineering shear GAM | 0.01 |
| BADPAIR | 0, **face-interior pairs only** |
| PCG iterations | 219; converged |
| Errors | 0 |

## Results extracted by POST1

| Item | Value | Unit |
|---|---:|---|
| VTOT | 2.49200639e-16 | m³ |
| UTOT | 8.45025999e-11 | J |
| GXYVSUM | 2.49203461e-18 | m³ |
| GAMAVG | +0.01000011324 | dimensionless |
| G12ENG | 6781739110 | Pa |
| G12ENGGPA | 6.781739110 | GPa |

Recalculation using the displayed rounded fields:

\[
G_{12}=\frac{2(8.45025999\times10^{-11})}{(2.49200639\times10^{-16})(0.01000011324)^2}
\approx6.78173912\ \mathrm{GPa}.
\]

The cell-volume check gives \(2.08\times10^{-6}\cdot8.32\times10^{-6}\cdot14.40\times10^{-6}=2.49200640\times10^{-16}\ \mathrm{m^3}\), consistent with VTOT. The relative difference between the computed average shear strain and applied 0.01 is about **0.0011324%**.

## Warnings and verification status

- The zero BADPAIR value verifies only the script's unmatched **face-interior** counter; edge and corner pairs are not explicitly checked.
- ANSYS warned that element shape checking was inactive.
- ANSYS warned that reduced-integration brick elements were used with PCG; multiple elements through the thickness or the sparse solver should be checked.
- The pre-solve CEDELE command found no existing equations to delete; this warning did not prevent solving.
- The solution output reports convergence and zero errors. The final performance section additionally notes memory/I/O limitations.
- The analysis title identifies the random-20% case but does not independently verify every material assignment.
- Mesh independence and periodic displacement-jump residuals have not been demonstrated.

This summary is based on the **original** APDL periodic constraints, not the previously sign-reversed review copy. The comment-cleaned reviewed file now retains the original solver commands.

Reference benchmark: [random-20pct-validation.csv](random-20pct-validation.csv).
