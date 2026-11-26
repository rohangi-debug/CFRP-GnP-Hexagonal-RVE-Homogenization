# Periodic XY Shear Loading — Full Hexagonal RVE

> **Status:** Original APDL commands recovered. The method is documented below; force/energy output and solver run logs are still required for a complete numerical audit.
>
> **Scope:** Baseline CFRP composite with **20% randomly oriented GnPs within the homogenized interphase**. The material-1/fibre axis is **global X**, material-2 is **global Y**, and material-3 is **global Z**.

**APDL inputs:** [original periodic CE script](../apdl-scripts/xy-shear-periodic-original.inp), [sign-consistent review version](../apdl-scripts/xy-shear-periodic-reviewed.inp), and [energy post-processing commands](../apdl-scripts/xy-shear-energy-post1.inp). The review version changes the signed constants in the `CE` commands; all scripts require verification on the original mesh before reuse.

## 1. Objective

Determine the effective longitudinal shear modulus, \(G_{12}\), of the GnP-reinforced CFRP lamina. The **full RVE** was used for periodic shear loading; the **one-eighth RVE** was used for normal loading, documented separately in [01-normal-loading.md](01-normal-loading.md).

## 2. Model and Materials

The full hexagonal RVE contains three regions: AS4 carbon fibre, EPON 828 epoxy matrix, and the equivalent isotropic randomly oriented 20% GnP/epoxy interphase. See the normal-loading guide for baseline elastic properties and composition.

The original GitHub image [full-rve-ansys.png](../images/full-rve-ansys.png) illustrates the geometry and mesh but **shows the material name “Aligned 20% GnPs Interface” in the ANSYS tree**. It must not be represented as proof of the **random-case** material assignment. A random-case full-RVE screenshot or solved project must establish the actual material assignments.

The original full-RVE dimensions, total modeled volume, mesh statistics, and solver model archive have not yet been recovered. The APDL pre-solve code automatically queries the mesh coordinate limits rather than hard-coding geometric dimensions.

## 3. Applied Macroscopic XY Shear

The pre-solve APDL script explicitly specifies the **engineering shear strain**

\[
\boxed{\gamma_{12}=\gamma_{XY}=0.01.}
\]

For pure engineering shear, the desired macroscopic strain tensor is

\[
\overline{\boldsymbol\varepsilon}=
\begin{bmatrix}
0 & 0.005 & 0 \\
0.005 & 0 & 0 \\
0 & 0 & 0
\end{bmatrix}.
\]

The script extracts \(X_{\min}\), \(X_{\max}\), and the corresponding Y/Z limits from the selected nodes, then defines

\[
L_X=X_{\max}-X_{\min}, \qquad
L_Y=Y_{\max}-Y_{\min}, \qquad
L_Z=Z_{\max}-Z_{\min}.
\]

The coded offset magnitudes are

\[
\mathrm{DUYX}=\frac{\gamma_{12}L_X}{2}=0.005L_X,
\qquad
\mathrm{DUXY}=\frac{\gamma_{12}L_Y}{2}=0.005L_Y.
\]

They correspond to a **symmetric macroscopic shear displacement gradient**: relative displacement in global Y across the X faces and relative displacement in global X across the Y faces.

## 4. APDL Periodic Boundary Conditions

### 4.1 Coordinate systems and initial setup

The supplied script begins immediately before the ANSYS solve operation and enters `/PREP7`, applies `ALLSEL,ALL`, and sets `CSYS,0` (global Cartesian coordinates). It calls `CEDELE,ALL` before generating new constraint equations, so **any pre-existing CE constraints would be removed**; the complete Workbench model must be checked before relying on that reset.

The script defines

- `GAM = 0.01` (engineering XY shear);
- `TOL = 1e-10` m (boundary-node coordinate selection tolerance);
- `PAIRTOL = 1e-12` m (face-node coordinate matching tolerance);
- `BADPAIR = 0` (counter for unmatched interior face-node pairs).

The coordinate tolerances must be checked against mesh-coordinate precision and actual node spacing. The face matching code requires essentially coincident corresponding transverse coordinates, so **a conforming/matched periodic boundary mesh is assumed**.

### 4.2 Face-interior constraints

The APDL script separates face interiors from edge and corner nodes. For opposing X faces, it pairs nodes with identical Y and Z coordinates; for Y faces, it pairs equal X and Z; for Z faces, it pairs equal X and Y.

For the positive-shear convention, the intended relative-displacement relations are:

| Face pair | Relative UX | Relative UY | Relative UZ |
|---|---:|---:|---:|
| XMAX − XMIN | 0 | DUYX | 0 |
| YMAX − YMIN | DUXY | 0 | 0 |
| ZMAX − ZMIN | 0 | 0 | 0 |

The original APDL input uses the `CE,NEXT,...` command to enforce these equations and a nearest-selected-node search `NODE(...)` to find the paired target node. The face pairing loops check the two matching coordinates against `PAIRTOL` and increment `BADPAIR` on failure.

**Sign convention:** ANSYS uses `CONST + sum(COEF * DOF) = 0` for a `CE` equation. In the archived source, the positive `DUYX` and `DUXY` constants with coefficients `+1` on the positive-face node and `−1` on the negative-face node therefore impose the **negative** of the displacement jump described in the code comments. Consequently, the imposed signed macroscopic shear is expected to be **−0.01**, even though `GAM` is set to `+0.01`. The elastic energy and recovered modulus are unchanged by reversing the shear direction in a linear elastic model. A separate sign-consistent version of the script changes the nonzero CE constants; that version has not been solved in ANSYS.

### 4.3 Edges and vertices

The script explicitly implements Barbero's separate treatment of boundary intersections:

- Two diagonally opposite XY edge pairs (Barbero Eqs. 6.35–6.36);
- Two XZ edge pairs (Eq. 6.37);
- Two YZ edge pairs (Eq. 6.38);
- Four diagonally opposite corner pairs (Eq. 6.39).

These constraints assign the summed displacement differences appropriate to the combined boundary translations and avoid using ordinary face-interior equations on edge/corner nodes.

`BADPAIR` is incremented only for mismatched **face-interior** nodes, and the script reports the final count without halting on a nonzero value. The edge/corner `NODE(...)` queries lack explicit zero-ID and coordinate checks. These should be checked before using the script for a new mesh.

### 4.4 Rigid-body suppression

The script identifies one corner node,

```
NREF=NODE(XMIN,YMIN,ZMIN)
D,NREF,UX,0
D,NREF,UY,0
D,NREF,UZ,0
```

This is the intended means of removing the **three uniform rigid-body translations** that the periodic displacement-difference equations leave unconstrained. The node must be present and correctly selected. The rest of the periodic constraints, rather than extra arbitrary supports, should control the deformation.

## 5. Static Solution and ANSYS Post-Processing

The separately supplied post-processing snippet is inserted **immediately after `/POST1`**. It requests the final result set (`SET,LAST`), selects all elements, and sets `RSYS,0` so result components are interpreted in global coordinates.

The procedure creates an element table for:

| APDL item | Meaning | Intended units |
|---|---|---|
| `ETABLE,VOL,VOLU` | Each element's physical volume | m³ |
| `ETABLE,SENE,SENE` | Each element's elastic strain energy | J |
| `ETABLE,GXY,EPEL,XY` | Local XY elastic shear strain component | dimensionless |

It then multiplies each element's XY elastic strain by its volume (`SMULT,GXYVOL,GXY,VOL`) and uses `SSUM` to calculate the following totals:

\[
V_{\mathrm{TOT}}=\sum_e V_e,\qquad
U_{\mathrm{TOT}}=\sum_e U_e,\qquad
Q=\sum_e (\gamma_{XY,e}V_e).
\]

The volume-weighted average shear component is

\[
\boxed{\gamma_{\mathrm{AVG}}=\frac{Q}{V_{\mathrm{TOT}}}.}
\]

**Definition check:** Confirm that the ANSYS `EPEL,XY` component is returned as **engineering shear strain** for the active solid element formulation. Also check whether `GAMAVG` is numerically close to the imposed \(\pm0.01\). A value near \(\pm0.005\) would indicate a shear-strain convention mismatch and would make the energy-derived modulus four times too large if treated as engineering shear. This comparison is not yet possible because the solver's printed values have not been supplied.

The analysis is a baseline small-strain linear elastic model; thus elastic shear strain should coincide with total shear strain under the stated assumptions. If later material models include inelastic strains, this equality must be reconsidered.

## 6. Effective Longitudinal Shear Modulus from Energy

For a macroscopic **pure XY shear** strain state, the stored elastic energy is

\[
U_{\mathrm{TOT}}=\frac{1}{2}V_{\mathrm{TOT}}G_{12}\gamma_{12}^2.
\]

The original post-processing script evaluates

\[
\boxed{G_{12}=\frac{2U_{\mathrm{TOT}}}{V_{\mathrm{TOT}}\gamma_{\mathrm{AVG}}^2}}
\]

and converts to gigapascals using

```
G12ENG  = 2*UTOT/(VTOT*GAMAVG*GAMAVG)
G12ENGGPA = G12ENG/1E9
```

This is appropriate if the full RVE is included in the element selection, the macroscopic loading is pure shear, and `GAMAVG` is the **engineering** shear strain consistent with the energy equation.

### 6.1 Unit and expected-output checks

With SI solver units, the ratio \(U/V\) has units of Pa. The original APDL output prints `VTOT` in m³, `UTOT` in J, `GAMAVG` as dimensionless, and `G12ENGGPA` in GPa.

For the **recorded** modulus \(G_{12}=6.781739\) GPa and the **coded** \(|\gamma_{12}|=0.01\), the corresponding expected energy density is

\[
\boxed{\frac{U}{V}=\frac12(6.781739\times10^9)(0.01)^2
=339{,}086.95\ \mathrm{J/m^3}.}
\]

This is a **calculated consistency target**, not a recovered ANSYS measurement. When the original solution log is provided, verify `UTOT / VTOT` and `GAMAVG` directly rather than assuming that they agree.

## 7. Published Benchmark

The project's recorded result for randomly oriented 20% GnPs in the interphase is:

| Property | Recorded implementation | Pawlik et al. (2019) | Difference |
|---|---:|---:|---:|
| \(G_{12}\) | 6.781739 GPa | 6.780000 GPa | 0.0256% |

The comparison is recorded in [random-20pct-validation.csv](../results/random-20pct-validation.csv). Agreement with the reference does **not** independently establish correct equation signs, element-wise shear-strain conventions, exact material assignment, or mesh convergence.

## 8. Verification Register

| Priority | Check | Why it matters |
|---|---|---|
| **High** | Print `GAMAVG`, `UTOT`, `VTOT`, `G12ENGGPA` from the actual run | Confirms the stated numerical result can be reconstructed from the implementation |
| **High** | Check `GAMAVG ≈ +0.01` or `−0.01` and verify `EPEL,XY` convention | Identifies imposed shear sign and possible factor-of-two error |
| **High** | Verify actual displacement jumps on matched face-node pairs / ANSYS `CE` constant sign | The author's intended sign and enforced equation may differ |
| **High** | Confirm the **random-20% interphase** was assigned to the solved full RVE | Available full-RVE screenshot instead names an aligned interphase |
| **High** | Check `BADPAIR = 0` and audit **edge/corner** node matching | The script counts unmatched face interiors but not edge/corner failures |
| **Medium** | Confirm the recorded `CE` count and solver warnings | Avoids undetected overconstraint, dropped constraints or skipped nodes |
| **Medium** | Confirm `CEDELE,ALL` did not remove required pre-existing equations | This command resets all CEs before generating periodic constraints |
| **Medium** | Confirm all desired structural elements were included by `ESEL,ALL` | Energy must cover the intended material regions and physical volume |
| **Medium** | Archive full-RVE mesh/geometry and compare refined-mesh shear result | Required to establish numerical mesh independence |
| **Medium** | Where possible, independently derive average shear stress and verify \(G_{12}=\bar\tau_{12}/\gamma_{12}\) | Independent check on strain-energy method |

## 9. References

1. Pawlik, M., Le, H., and Lu, Y. (2019). *Effects of the graphene nanoplatelets reinforced interphase on mechanical properties of carbon fibre reinforced polymer – A multiscale modelling study*. **Composites Part B: Engineering, 177**, 107097. https://doi.org/10.1016/j.compositesb.2019.107097.
2. Barbero, E. J. (2014). *Finite Element Analysis of Composite Materials Using ANSYS*, 2nd ed., Chapter 6, especially Eqs. (6.29)–(6.40).
3. Ji, X.-Y., Cao, Y.-P., and Feng, X.-Q. (2010). *Micromechanics prediction of the effective elastic moduli of graphene sheet-reinforced polymer nanocomposites*. **Modelling and Simulation in Materials Science and Engineering, 18**, 045005. https://doi.org/10.1088/0965-0393/18/4/045005.

**Reproducibility:** The script is included as provided, with a separate sign-corrected candidate. The original solver output and material assignment of the random-GnP case remain to be archived.
