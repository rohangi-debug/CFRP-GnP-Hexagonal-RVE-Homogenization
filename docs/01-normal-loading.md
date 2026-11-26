# Normal Loading — One-Eighth Hexagonal RVE

> **Documentation status:** Reconstructed working draft, 25 November 2026. Sections 1–5 preserve the recorded project setup; Sections 6–9 document the reaction-force-based recovery and validation. Items marked **verification required** must be checked against the solved ANSYS project before this guide is described as fully reproducible.
>
> **Scope:** Pawlik et al. (2019) baseline with **randomly oriented GnPs at 20% volume fraction within the interphase**. This guide does not report the later Aluko-property extension or an aligned-GnP calculation.

## 1. Objective

Determine the effective elastic properties of a carbon-fibre-reinforced polymer (CFRP) composite containing a graphene nanoplatelet (GnP)-reinforced interphase using finite element homogenization.

## 2. Geometry

The one-eighth hexagonal representative volume element (RVE) was modeled in SolidWorks using the geometric parameters reported by Pawlik et al. (2019), Section 2.2.2.

The RVE dimensions are **7.20 × 4.16 × 1.04 µm** (height × width × thickness). The carbon fibre diameter is **7.10 µm**, with a **0.20 µm**-thick GnP-reinforced interphase surrounding the fibre.

The model consists of three material regions: carbon fibre (**66%** by volume), epoxy matrix (**26.3%**), and GnP-reinforced interphase (**7.7%**).

The fibre direction is aligned with the global **X-axis** (material direction 1). The geometry was exported from SolidWorks and imported into ANSYS Mechanical for numerical homogenization.

**Coordinate mapping used below:** the previously documented X-direction RVE length is 1.04 µm. The 7.20 µm and 4.16 µm cross-sectional dimensions must be mapped to the global Y- and Z-axes using the archived CAD model before reporting transverse face areas.

## 3. Material Properties

Three material regions were assigned in ANSYS Mechanical: AS4 carbon fibre, EPON 828 epoxy resin, and the homogenized GnP-reinforced interphase.

### 3.1 Carbon Fibre (AS4)

The carbon fibre was modeled as a transversely isotropic linear elastic material, with the fibre direction aligned along global X.

| Property | Value |
|---|---:|
| Longitudinal Young's modulus, \(E_1\) | 224.0 GPa |
| Transverse Young's modulus, \(E_2\) | 17.2 GPa |
| Longitudinal shear modulus, \(G_{12}\) | 27.6 GPa |
| Transverse shear modulus, \(G_{23}\) | 5.73 GPa |
| Longitudinal Poisson's ratio, \(\nu_{12}\) | 0.20 |
| Transverse Poisson's ratio, \(\nu_{23}\) | 0.50 |

### 3.2 Epoxy Matrix (EPON 828)

The epoxy matrix was modeled as an isotropic linear elastic material.

| Property | Value |
|---|---:|
| Young's modulus, \(E_m\) | 3.45 GPa |
| Poisson's ratio, \(\nu_m\) | 0.34 |

### 3.3 GnP-Reinforced Interphase

The interphase was modeled as an **equivalent homogeneous isotropic material** instead of explicitly meshing the individual randomly oriented GnPs.

For the 20% randomly oriented GnP case, the recorded effective interphase properties were obtained using the analytical homogenization approach discussed by Ji et al. (2010) and Pawlik et al. (2019).

| Property | Recorded value |
|---|---:|
| Young's modulus, \(E_I\) | 94.080348 GPa |
| Poisson's ratio, \(\nu_I\) | 0.2143153 |
| Shear modulus, \(G_I\) | 38.738 GPa |
| Bulk modulus, \(K_I\) | 54.886 GPa |

The **20% GnP volume fraction refers to the interphase**, not to the whole RVE. All three regions were assigned linear elastic behavior.

**Reproducibility note:** The archived README records these interphase values, but the complete spreadsheet/calculation sequence used to obtain them has not yet been deposited in the repository. The underlying Mori–Tanaka calculation should be checked against the original Ji equations before it is treated as independently verified.

## 4. Meshing

### 4.1 Mesh Generation Strategy

The one-eighth hexagonal RVE was meshed in ANSYS Mechanical using the **Sweep Method**. The meshing strategy focused on resolving the thin GnP-reinforced interphase between the carbon fibre and epoxy matrix.

Since the interphase thickness was 200 nm, a nominal element size of 100 nm (\(1\times10^{-7}\) m) was selected to provide approximately two elements across the interphase. Visual inspection of the generated mesh confirmed two elements across this region. The carbon fibre and surrounding epoxy were meshed to accommodate the interphase-focused discretization.

### 4.2 Sweep Method

The Sweep Method was selected because the RVE has a uniform cross-section along the fibre direction (global X-axis). It was used to generate a structured mesh along that direction while maintaining the mesh around curved fibre–interphase–matrix boundaries.

### 4.3 Mesh Settings

| Parameter | Setting |
|---|---|
| Meshing method | Sweep Method |
| Nominal element size | \(1\times10^{-7}\) m (100 nm) |
| Mesh sizing | Adaptive |
| Element order | Program Controlled |
| Element transition | Fast |
| Smoothing | Medium |
| Mesh quality checking | Enabled |
| Automatic inflation | None |

### 4.4 Mesh Statistics

| Parameter | Value |
|---|---:|
| Total nodes | 159,561 |
| Total elements | 36,454 |

The mesh was generated without reported mesh-generation errors.

### 4.5 Mesh Selection and Limitations

The nominal element size was selected from the geometric resolution required for a 200 nm-thick interphase. **A formal mesh-convergence study was not performed**, so mesh independence of the effective elastic properties has not been established.

## 5. Boundary Conditions

Displacement-controlled normal loading was used to generate three independent macroscopic strain states. The following is the **previously documented setup**; the Y-loading strain state has been clarified by the researcher: a prescribed 1% Y-normal strain with zero macroscopic X- and Z-normal strains. Exact face selections and reaction-force outputs still require confirmation against the solved project.

### 5.1 X-Direction Loading

A nominal axial strain of 1% was applied along the fibre direction (global X-axis). With \(L_x=1.04\) µm,

\[
U_x=\varepsilon_x L_x=0.01(1.04\times10^{-6})=1.04\times10^{-8}\ \mathrm{m}.
\]

The previously recorded constraints were:

| Face | Prescribed component |
|---|---|
| \(X=0\) | \(U_x=0\) |
| \(X=1.04\) µm | \(U_x=1.04\times10^{-8}\) m |
| \(Y=0\) | \(U_y=0\) |
| \(Z=0\) | \(U_z=0\) |

Other components were described as free unless constrained by symmetry.

### 5.2 Y- and Z-Direction Loading

**Researcher-confirmed Y-loading procedure:** A macroscopic normal strain of 0.01 was applied in the global Y-direction, and the macroscopic X- and Z-direction normal strains were held at zero:

\[
[\overline\varepsilon_{11},\overline\varepsilon_{22},\overline\varepsilon_{33}]^T=[0,0.01,0]^T.
\]

The reaction-force components were extracted for all three directions to obtain **\(C_{12}\), \(C_{22}\), and \(C_{32}\)**. See Section 6.4 for the direct formula. This establishes the prescribed **macroscopic strain state**, although the precise face-scoping details and original force values have yet to be re-examined.

The previously documented Z-direction procedure likewise prescribed a nominal 1% axial strain with the corresponding symmetry constraints. The original displacement objects and face dimensions must be confirmed from the ANSYS model.

### 5.3 Important Boundary-Condition Verification

**The Y-loading macroscopic transverse strains are now researcher-confirmed as zero; the X- and Z-loading constraints still require checking against the archived ANSYS settings.** For direct reaction-force recovery of a column of the stiffness matrix, all three average normal strains must be controlled. In the corresponding one-eighth-RVE procedure given in Barbero, Chapter 6, Eqs. (6.21), (6.24), and (6.27), the transverse displacement component is also fixed on the **opposite outer face**.

For an X-normal-strain stiffness-column experiment, the required macroscopic strain state is

\[
[\overline\varepsilon_{11},\overline\varepsilon_{22},\overline\varepsilon_{33}]^T
=[0.01,0,0]^T.
\]

For the corresponding Y and Z experiments, the intended strain states are \([0,0.01,0]^T\) and \([0,0,0.01]^T\).

**Verification required:** Inspect the ANSYS *Displacement* objects, scoped faces, components and magnitudes to reconstruct the exact boundary implementation. For Y loading, the researcher confirmed the pure-strain state, so the second stiffness column follows directly from Y-loading reaction forces. For X and Z loading, if a transverse component was not actually constrained, the measured average strain vectors must instead be recovered and the general matrix method in Section 6.4 used.

## 6. Solution and Reaction-Force Post-Processing

### 6.1 Static Solution

The linear elastic RVE was solved in ANSYS Mechanical for each of the three normal-loading configurations. The recorded procedure extracted **reaction forces**, rather than directly averaging element stresses, to calculate the macroscopic normal stress responses.

### 6.2 Archived Evidence of Force-Reaction Extraction

The repository screenshot [one-eighth-rve-ansys.png](../images/one-eighth-rve-ansys.png) shows an ANSYS *Force Reaction* result scoped to **Displacement 2**. The displayed force components are approximately:

| Global force component | Screenshot value |
|---|---:|
| \(F_X\) | \(1.7215\times10^{-12}\) N |
| \(F_Y\) | \(3.845\times10^{-7}\) N |
| \(F_Z\) | \(5.7583\times10^{-4}\) N |

The displayed total reaction magnitude is approximately \(5.7583\times10^{-4}\) N.

**Evidence limit:** This screenshot does **not** identify all the reaction forces required for the three stiffness columns, nor does it verify the corresponding face selections or loading-case labels. These values should not be inserted into the stiffness calculations until the solved case and selected boundary condition are confirmed.

### 6.3 Average Stress from Reaction Forces

When the prescribed displacement and support conditions establish the intended homogeneous macroscopic normal strain state, the reaction-force result for the **complete positive outer face** can be converted to average normal stress as

\[
\overline{\sigma}_{ii}^{(j)}
=\frac{F_i^{(j)}}{A_i},
\]

where \(F_i^{(j)}\) [N] is the **signed** resultant reaction force in direction \(i\) from the face associated with the positive \(i\)-boundary during loading case \(j\), and \(A_i\) [m²] is that face's gross cross-sectional area. The sign is chosen consistently with positive macroscopic normal stress.

Use the actual external face areas of the one-eighth model. Do not use only fibre area, only interphase area, or the area of a partially scoped face. Reaction forces from two opposing faces must not be added as though they acted in the same direction.

**Verification required:** Recover the complete ANSYS reaction-force table and confirm that each query is scoped to the relevant entire boundary. If a required resultant is distributed among several constraints, identify all contributing reactions without double counting.

### 6.4 Effective Normal Stiffness Matrix

With global X identified as material direction 1, the normal constitutive relation is

\[
\begin{bmatrix}
\overline\sigma_{11}\\
\overline\sigma_{22}\\
\overline\sigma_{33}
\end{bmatrix}
=
\underbrace{\begin{bmatrix}
C_{11}&C_{12}&C_{13}\\
C_{21}&C_{22}&C_{23}\\
C_{31}&C_{32}&C_{33}
\end{bmatrix}}_{\mathbf C_N^{\mathrm{eff}}}
\begin{bmatrix}
\overline\varepsilon_{11}\\
\overline\varepsilon_{22}\\
\overline\varepsilon_{33}
\end{bmatrix}.
\]

**Case A — three verified pure normal macroscopic strain states.** If each imposed strain state is exactly 1% in the loaded direction and zero in the other two normal directions, then

\[
\boxed{C_{ij}=\frac{F_i^{(j)}}{A_i(0.01)}}.
\]

That is, the X, Y and Z load cases provide, respectively, columns 1, 2 and 3 of \(\mathbf C_N^{\mathrm{eff}}\), **when the stated pure-strain conditions are met**. The Y-loading conditions have been confirmed by the researcher, giving the second-column equations:

\[
\boxed{C_{12}=\frac{F_x^{(Y)}}{0.01A_x},\qquad C_{22}=\frac{F_y^{(Y)}}{0.01A_y},\qquad C_{32}=\frac{F_z^{(Y)}}{0.01A_z}}.
\]

For X and Z loading, corresponding direct extraction remains contingent on the exact archived boundary implementation. The individual reaction forces must be retained so the calculation can be audited.

**Case B — transverse strains were not all constrained.** If the average normal strain vector differs from the idealized pure-strain state, assemble the three measured average stress and strain vectors as columns:

\[
\boldsymbol\Sigma=
[\overline{\boldsymbol\sigma}^{(X)}\
\overline{\boldsymbol\sigma}^{(Y)}\
\overline{\boldsymbol\sigma}^{(Z)}],
\qquad
\boldsymbol{\mathcal E}=
[\overline{\boldsymbol\varepsilon}^{(X)}\
\overline{\boldsymbol\varepsilon}^{(Y)}\
\overline{\boldsymbol\varepsilon}^{(Z)}].
\]

For three linearly independent strain vectors and a valid homogenized response,

\[
\boxed{\mathbf C_N^{\mathrm{eff}}=\boldsymbol\Sigma\boldsymbol{\mathcal E}^{-1}}.
\]

Only the measured strain vectors, not the imposed axial displacements alone, justify use of this general relation. For either case, linear elasticity requires \(C_{ij}=C_{ji}\) within numerical accuracy.

### 6.5 Recovering Engineering Elastic Constants

The corresponding normal compliance matrix is

\[
\mathbf S_N^{\mathrm{eff}}
=(\mathbf C_N^{\mathrm{eff}})^{-1}.
\]

For the standard engineering convention (first subscript = loading direction),

\[
E_1=1/S_{11},\qquad E_2=1/S_{22},\qquad E_3=1/S_{33},
\]

\[
\nu_{12}=-S_{21}/S_{11},\qquad
\nu_{23}=-S_{32}/S_{22}.
\]

Because the fibre is along direction 1, a transversely isotropic homogenized lamina has the 2–3 plane as its isotropy plane. The transverse shear modulus can then be computed from

\[
G_{23}=\frac{E_2}{2(1+\nu_{23})}.
\]

The **longitudinal shear modulus \(G_{12}\)** was obtained from a separate **full-RVE periodic XY shear** simulation, with periodic displacement constraint equations written in APDL and a strain-energy-based extraction procedure. The shear calculation must be documented separately.

## 7. Recorded Results and Numerical Consistency Checks

### 7.1 Baseline Comparison: Random 20% GnP Interphase

The results recorded in the repository are:

| Property | Recorded ANSYS result | Pawlik et al. (2019) | Absolute relative difference |
|---|---:|---:|---:|
| \(E_1\) (GPa) | 156.169300 | 156.23000 | 0.04% |
| \(E_2\) (GPa) | 11.156360 | 11.56000 | 3.49% |
| \(\nu_{12}\) | 0.233086 | 0.23000 | 1.34% |
| \(\nu_{23}\) | 0.472923 | 0.47000 | 0.62% |
| \(G_{12}\) (GPa) | 6.781739 | 6.78000 | 0.03% |

See [random-20pct-validation.csv](../results/random-20pct-validation.csv). The relative difference is \(100|P_{\mathrm{FE}}-P_{\mathrm{ref}}|/|P_{\mathrm{ref}}|\).

These are **recorded project outputs**, not newly recomputed force-based results.

### 7.2 Independent Consistency Reconstruction (Not a Recovered ANSYS Output)

As a check on the archived engineering constants, enforce transverse isotropy with \(E_3=E_2\) and \(\nu_{13}=\nu_{12}\) and construct the normal compliance matrix

\[
\mathbf S_N=
\begin{bmatrix}
1/E_1&-\nu_{12}/E_1&-\nu_{12}/E_1\\
-\nu_{12}/E_1&1/E_2&-\nu_{23}/E_2\\
-\nu_{12}/E_1&-\nu_{23}/E_2&1/E_2
\end{bmatrix}.
\]

Its inversion gives the **implied**, rather than directly measured, normal stiffness matrix (GPa):

\[
\mathbf C_{N,\mathrm{implied}}\approx
\begin{bmatrix}
158.5036&5.0074&5.0074\\
5.0074&14.5286&6.9543\\
5.0074&6.9543&14.5286
\end{bmatrix}.
\]

The corresponding transverse shear modulus is \(G_{23}\approx3.78715\) GPa. The implied matrix is symmetric and its eigenvalues are positive. These values can be used as a **future cross-check** against the archived reaction-force-derived stiffness coefficients, but cannot substitute for those coefficients.

## 8. Verification Register Before Final Reproducibility Release

| Item | Evidence currently available | Outstanding verification |
|---|---|---|
| Geometry and constituent fractions | Recorded in the project notes and Pawlik baseline | Confirm axis-to-dimension mapping and face areas in CAD |
| Constituent property assignment | Recorded baseline input tables | Verify material assignment to each body; screenshots show both aligned and random material names in the tree |
| Mesh | Recorded 100 nm target, two interphase elements, 159,561 nodes and 36,454 elements | Independent mesh-convergence study |
| Normal strain amplitude | Recorded 1% X/Y/Z loading | Confirm applied displacement components and scope for all three analyses |
| Transverse constraints | Researcher confirms Y loading used \(\overline\varepsilon_{11}=\overline\varepsilon_{33}=0\), alongside \(\overline\varepsilon_{22}=0.01\) | Confirm face-scoping details; check X/Z loading pure-strain constraints before direct extraction |
| Reaction forces | One force-reaction screenshot for Displacement 2 | Recover all signed X/Y/Z force resultants, face areas, and load-case mapping |
| Stiffness matrix | Engineering constants archived, implied matrix reconstructed in Section 7.2 | Recompute the full \(3\times3\) normal stiffness matrix from ANSYS results; check symmetry and positive definiteness |
| Shear stiffness | Recorded \(G_{12}=6.781739\) GPa | Archive full-RVE periodic-constraint APDL, strain energy, imposed shear strain and calculation |
| Interphase homogenization | Four interphase effective properties recorded | Archive constituent inputs, Mori–Tanaka calculations and equation-version check |
| Scientific comparison | Baseline CSV in repository | Confirm recorded values against solved models; do not label literature agreement as mesh convergence |

## 9. References

1. Pawlik, M., Le, H., and Lu, Y. (2019). *Effects of the graphene nanoplatelets reinforced interphase on mechanical properties of carbon fibre reinforced polymer – A multiscale modelling study*. **Composites Part B: Engineering, 177**, 107097. https://doi.org/10.1016/j.compositesb.2019.107097
2. Ji, X.-Y., Cao, Y.-P., and Feng, X.-Q. (2010). *Micromechanics prediction of the effective elastic moduli of graphene sheet-reinforced polymer nanocomposites*. **Modelling and Simulation in Materials Science and Engineering, 18**, 045005. https://doi.org/10.1088/0965-0393/18/4/045005
3. Barbero, E. J. (2014). *Finite Element Analysis of Composite Materials Using ANSYS*, 2nd ed., Chapter 6, *Computational Micromechanics*, especially Eqs. (6.19)–(6.27).

*This repository is an independent implementation and is not an official implementation from the published authors.*
