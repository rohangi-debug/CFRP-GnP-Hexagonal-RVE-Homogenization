# Periodic XY Shear Loading — Full Hexagonal RVE

## 1. Purpose and Model

The longitudinal shear modulus \(G_{12}\) was obtained using the **full hexagonal RVE**, with global **X along the carbon fibre** (material direction 1) and global **Y along direction 2**. The material regions and properties are the same as those in [01-normal-loading.md](01-normal-loading.md), for the **20 vol% randomly oriented GnP interphase**.

The full-RVE model uses a bounding cell of **2.08 × 8.32 × 14.40 µm** (X × Y × Z), with **1,048,906 nodes** and **249,879 SOLID186 elements**. The recorded simulation was solved in **ANSYS Mechanical / MAPDL 2026 R1**.

## 2. Periodic Boundary Conditions

The full RVE was subjected to **engineering XY shear strain**

\[
\gamma_{12}=0.01,\qquad
\varepsilon_{12}=\varepsilon_{21}=\gamma_{12}/2.
\]

Periodic displacement constraints were generated using ANSYS APDL constraint equations (CE). The pre-solve script obtains the coordinate bounds and automatically calculates the shear displacement offsets:

\[
\Delta u_y^{(X)}=\frac{\gamma_{12}L_x}{2},
\qquad
\Delta u_x^{(Y)}=\frac{\gamma_{12}L_y}{2}.
\]

For the recorded full RVE these offsets have magnitudes **1.04 × 10⁻⁸ m** and **4.16 × 10⁻⁸ m**, respectively. The script pairs periodic nodes on opposite **face interiors**, and applies separate equations for **edges and corner vertices** to avoid redundant constraints. The three translations of one reference corner are fixed to remove rigid-body motion.

The original executable commands, with comment lines removed, are retained in [xy-shear-periodic-original.inp](../apdl-scripts/xy-shear-periodic-original.inp). The script should be executed on a **matching periodic mesh**; their node searches and boundary-coordinate tolerance depend on that mesh. The underlying periodic-shear formulation follows Barbero, Chapter 6, Eqs. (6.29)–(6.39).

## 3. Energy-Based Post-Processing

The ANSYS /POST1 commands sum the **elastic strain energy** and **volume** over all selected solid elements. The volume-averaged **engineering XY elastic shear strain** is calculated from the element table:

\[
V=\sum_e V_e,\qquad U=\sum_e U_e,\qquad
\overline\gamma_{12}=\frac{\sum_e\gamma_{12,e}V_e}{V}.
\]

For the imposed pure-shear macroscopic strain state, the effective shear modulus is

\[
\boxed{G_{12}=\frac{2U}{V\overline\gamma_{12}^{\,2}}}.
\]

The post-processing commands are supplied in [xy-shear-energy-post1.inp](../apdl-scripts/xy-shear-energy-post1.inp). Volumes are in m³, strain energy in joules, and \(G_{12}\) in pascals (converted to GPa in the script).

## 4. Recorded ANSYS Result

The original ANSYS run on **9 November 2026** produced:

| Parameter | Value |
|---|---:|
| Applied engineering shear, GAM | 0.01000000 |
| Average engineering shear, GAMAVG | 0.01000011324 |
| Total volume, VTOT | 2.49200639 × 10⁻¹⁶ m³ |
| Total strain energy, UTOT | 8.45025999 × 10⁻¹¹ J |
| G12ENGGPA | **6.781739110 GPa** |

Independent substitution of the logged values yields

\[
G_{12}=\frac{2(8.45025999\times10^{-11})}
{(2.49200639\times10^{-16})(0.01000011324)^2}
\approx6.78173912\ \mathrm{GPa}.
\]

The calculated modulus differs from **6.78 GPa** reported by Pawlik et al. (2019) by approximately **0.026%**. The solver converged after **219 PCG iterations** with zero errors. The script printed **BADPAIR = 0** for its unmatched **face-interior** pairs.

The detailed values are recorded in [random-20pct-shear-solver-summary.md](../results/random-20pct-shear-solver-summary.md), with the literature comparison in [random-20pct-validation.csv](../results/random-20pct-validation.csv).

## 5. Reproducibility Notes

The original APDL commands and the strain-energy outputs reproduce the reported result. However, the following checks are not yet documented: the periodic displacement-jump residuals (including edges and vertices), independent confirmation of the material assignments in the solved model, and mesh convergence. The solver also warned that element shape checking was inactive and advised checking reduced-integration brick elements with PCG. These limitations should be addressed before describing the model as fully numerically verified.

## References

1. Pawlik, M., Le, H., and Lu, Y. (2019). *Effects of the graphene nanoplatelets reinforced interphase on mechanical properties of carbon fibre reinforced polymer – A multiscale modelling study*. **Composites Part B: Engineering, 177**, 107097.
2. Barbero, E. J. (2014). *Finite Element Analysis of Composite Materials Using ANSYS*, 2nd ed., Chapter 6, Eqs. (6.29)–(6.40).
