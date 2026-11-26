# Normal Loading — One-Eighth Hexagonal RVE

## 1. Purpose and Model

This guide describes the finite-element procedure used to obtain the effective **normal stiffness coefficients** of a CFRP composite with a graphene nanoplatelet (GnP)-reinforced interphase. The method follows the one-eighth RVE approach of Barbero (Chapter 6) and the geometry and material data of Pawlik et al. (2019). The worked example is the **randomly oriented 20 vol% GnP interphase** case.

The one-eighth hexagonal RVE was created in SolidWorks and imported into ANSYS Mechanical. Its dimensions are **7.20 × 4.16 × 1.04 µm**, with the fibre axis along **global X (material direction 1)**. The AS4 carbon fibre diameter is **7.10 µm** and the interphase thickness is **0.20 µm**. The RVE contains **66% fibre, 26.3% epoxy and 7.7% interphase by volume**. The 20% GnP loading refers to the interphase, not the entire RVE.

## 2. Material Properties

All three regions were treated as linearly elastic. The randomly oriented GnP interphase was represented by a homogeneous isotropic material rather than explicit platelets.

| Region | Property | Value |
|---|---|---:|
| AS4 carbon fibre (transversely isotropic) | E1 | 224.0 GPa |
| | E2 = E3 | 17.2 GPa |
| | G12 = G13 | 27.6 GPa |
| | G23 | 5.73 GPa |
| | ν12 = ν13 | 0.20 |
| | ν23 | 0.50 |
| EPON 828 epoxy (isotropic) | E | 3.45 GPa |
| | ν | 0.34 |
| 20% random GnP interphase (isotropic) | E | 94.080348 GPa |
| | ν | 0.2143153 |
| | G | 38.738 GPa |
| | K | 54.886 GPa |

The interphase properties were obtained by analytical homogenization following Ji et al. (2010) and Pawlik et al. (2019). The complete intermediate calculations are not currently included in the repository.

## 3. Meshing

A **Sweep Method** mesh was generated in ANSYS Mechanical, with a nominal element size of **100 nm** to provide approximately two elements through the 200 nm interphase. The recorded mesh contains **159,561 nodes** and **36,454 elements**. Element order was program controlled. A mesh-convergence study has not yet been documented.

The carbon fibre, GnP-reinforced interphase, and epoxy were connected using **Shared Topology** in ANSYS Mechanical's **Connections** branch. This was used to maintain displacement continuity between the material regions.

## 4. Boundary Conditions

Three separate static structural analyses were performed, applying **1% macroscopic normal strain** successively along X, Y and Z. In each case, the other two macroscopic normal strains were set to zero. The normal displacement on each boundary was prescribed; tangential components were left free except where required by the model's symmetry constraints.

| Loading case | Applied normal strain | Other normal strains |
|---|---|---|
| X (direction 1) | ε11 = 0.01 | ε22 = ε33 = 0 |
| Y (direction 2) | ε22 = 0.01 | ε11 = ε33 = 0 |
| Z (direction 3) | ε33 = 0.01 | ε11 = ε22 = 0 |

For the **X-loading worked example**, the RVE length is \(L_x=1.04\ \mu\mathrm m\), so the prescribed displacement is

\[
u_x=0.01L_x=1.04\times10^{-8}\ \mathrm m.
\]

In ANSYS, this was assigned to **Displacement 2** on named selection **X1**, with X displacement prescribed and Y/Z components free. The other boundary conditions established the zero-strain conditions. The exact face scopes remain recorded in the original Workbench project.

## 5. Reaction-Force Post-Processing

Force Reaction results were evaluated in ANSYS Mechanical with **Location Method: Boundary Condition** and the **global coordinate system**. The signed resultant **normal force** on each corresponding outer face was divided by its gross face area:

\[
\overline{\sigma}_{ii}=\frac{F_i}{A_i},\qquad
C_{ij}=\frac{\overline{\sigma}_{ii}^{(j)}}{0.01}.
\]

### Worked example — X loading

The following results were extracted for the X-loading analysis:

| ANSYS result | Boundary condition | Normal reaction |
|---|---|---:|
| Force Reaction | Displacement 2 (X1) | Fx = 0.047475 N |
| Force Reaction 2 | Displacement 4 | Fy = 3.7473 × 10⁻⁴ N |
| Force Reaction 3 | Displacement 6 | Fz = 2.1655 × 10⁻⁴ N |

Using \(L_x=1.04\), \(L_y=4.16\), and \(L_z=7.20\ \mu\mathrm m\), the respective gross face areas are:

\[
A_x=2.9952\times10^{-11},\quad
A_y=7.4880\times10^{-12},\quad
A_z=4.3264\times10^{-12}\ \mathrm{m^2}.
\]

The **first column** of the effective normal stiffness matrix is therefore

\[
C_{11}=\frac{0.047475}{0.01A_x}=158.503606\ \mathrm{GPa},
\]

\[
C_{21}=\frac{3.7473\times10^{-4}}{0.01A_y}=5.004407\ \mathrm{GPa},
\qquad
C_{31}=\frac{2.1655\times10^{-4}}{0.01A_z}=5.005316\ \mathrm{GPa}.
\]

These calculations use rounded values from the ANSYS screenshots. The X loading provides **column 1**; the **same displacement-control and reaction-force procedure** was repeated for Y and Z loading to obtain **columns 2 and 3**. The detailed Y/Z calculations are omitted from this guide.

The area calculation assumes the recorded axis mapping \(L_y=4.16\), \(L_z=7.20\ \mu\mathrm m\); verify that mapping against the original CAD model when reproducing the off-diagonal coefficients. A machine-readable calculation is supplied in [normal-x-reaction-summary.csv](../results/normal-x-reaction-summary.csv).

## 6. Engineering Constants and Comparison

After assembling the full normal stiffness matrix,

\[
\mathbf S_N=\mathbf C_N^{-1},\qquad
E_1=\frac1{S_{11}},\quad E_2=\frac1{S_{22}},\quad
\nu_{12}=-\frac{S_{21}}{S_{11}},\quad
\nu_{23}=-\frac{S_{32}}{S_{22}}.
\]

For the transversely isotropic lamina, \(G_{23}=E_2/[2(1+\nu_{23})]\). The longitudinal shear modulus \(G_{12}\) was calculated separately using the full RVE; see [02-shear-loading.md](02-shear-loading.md).

| Property | This model | Pawlik et al. (2019) |
|---|---:|---:|
| E1 (GPa) | 156.169300 | 156.23000 |
| E2 (GPa) | 11.156360 | 11.56000 |
| ν12 | 0.233086 | 0.23000 |
| ν23 | 0.472923 | 0.47000 |
| G12 (GPa) | 6.781739 | 6.78000 |

The comparison data are also available in [random-20pct-validation.csv](../results/random-20pct-validation.csv). Numerical agreement with the published values does **not** establish mesh convergence. Original X/Y/Z reaction-force output files and the configured Workbench project should be retained for a fully auditable reproduction.

## References

1. Pawlik, M., Le, H., and Lu, Y. (2019). *Effects of the graphene nanoplatelets reinforced interphase on mechanical properties of carbon fibre reinforced polymer – A multiscale modelling study*. **Composites Part B: Engineering, 177**, 107097.
2. Ji, X.-Y., Cao, Y.-P., and Feng, X.-Q. (2010). *Micromechanics prediction of the effective elastic moduli of graphene sheet-reinforced polymer nanocomposites*. **Modelling and Simulation in Materials Science and Engineering, 18**, 045005.
3. Barbero, E. J. (2014). *Finite Element Analysis of Composite Materials Using ANSYS*, 2nd ed., Chapter 6.
