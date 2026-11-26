# Periodic XY shear — full hexagonal RVE

I used a **full hexagonal RVE** in ANSYS Mechanical to calculate the effective longitudinal shear modulus $G_{12}$. This simulation used the same constituent materials and [homogenized random 20 vol% GnP interphase](00-interphase-properties.md) as my [normal-loading analysis](01-normal-loading.md).

## Model and boundary conditions

| Detail | Model value |
|---|---|
| RVE dimensions (X × Y × Z) | 2.08 × 8.32 × 14.40 µm |
| Fibre direction | Global X (material direction 1) |
| Mesh count | 1,048,906 nodes; 249,879 elements |
| Solver | ANSYS Mechanical / MAPDL 2026 R1 |
| Applied engineering shear | $\gamma_{12}=0.01$ |

I used APDL **constraint equations (CE)** to impose periodic displacement compatibility on opposite faces. My script implemented the XY shear displacement jumps

$$
\Delta u_y^{(X)}=\frac{\gamma_{12}L_x}{2},\qquad
\Delta u_x^{(Y)}=\frac{\gamma_{12}L_y}{2}.
$$

These offsets were $1.04\times10^{-8}$ m and $4.16\times10^{-8}$ m. I applied constraints to face-interior, edge, and corner nodes, with a reference corner fixed to remove rigid-body motion. The approach follows [Barbero (2014), Chapter 6](../README.md#references) and the RVE work in [Pawlik et al. (2019)](https://doi.org/10.1016/j.compositesb.2019.107097).

## Energy-based result

I used my [post-processing APDL script](../apdl-scripts/post-processing-APDL-script.inp) to sum the element elastic strain energies and volumes. I calculated the volume-averaged engineering XY shear strain and evaluated

$$
G_{12}=\frac{2U}{V\overline{\gamma}_{12}^{\,2}},
$$

where $U$ is total elastic strain energy, $V$ is the RVE volume, and $\overline{\gamma}_{12}$ is the volume-averaged engineering shear strain.

| Recorded quantity | Value |
|---|---:|
| Total RVE volume, $V$ | $2.49200639\times10^{-16}$ m³ |
| Elastic strain energy, $U$ | $8.45025999\times10^{-11}$ J |
| Average shear strain, $\overline{\gamma}_{12}$ | 0.01000011324 |
| **Calculated $G_{12}$** | **6.781739 GPa** |
| Pawlik et al. (2019) | 6.780000 GPa |
| Relative difference | **0.026%** |

The recorded run on 9 November 2026 converged after **219 PCG iterations** with no errors. The [solver summary](../results/random-20pct-shear-solver-summary.md) preserves the numerical inputs and warnings.
