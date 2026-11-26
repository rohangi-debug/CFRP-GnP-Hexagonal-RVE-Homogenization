# Interphase homogenization — random 20 vol% GnP

I calculated the elastic properties of the **GnP-reinforced epoxy interphase** before assigning it to the CFRP finite-element models. I used the constituent inputs and multiscale approach from [Pawlik, Le and Lu (2019), §2.1 and Table 1](https://doi.org/10.1016/j.compositesb.2019.107097), the Hill-parameter and Mori–Tanaka equations from [Ji, Cao and Feng (2010), Eqs. (1)–(4), (11)–(13)](https://doi.org/10.1088/0965-0393/18/4/045005), and the RVE method in [Barbero (2014), Chapter 6](../README.md#references).

## Constituent properties

| Material | Elastic properties |
|---|---|
| EPON 828 epoxy (isotropic) | $E_m=3.45$ GPa; $\nu_m=0.34$ |
| GnP (transversely isotropic) | $E^r_{1'}=770$ GPa; $E^r_{3'}=400$ GPa; $G^r_{1'3'}=78$ GPa; $\nu^r_{1'2'}=0.03$; $\nu^r_{1'3'}=0.81$ |
| AS4 carbon fibre (transversely isotropic) | $E_1=224$ GPa; $E_2=E_3=17.2$ GPa; $G_{12}=G_{13}=27.6$ GPa; $G_{23}=5.73$ GPa; $\nu_{12}=\nu_{13}=0.20$; $\nu_{23}=0.50$ |

These are the constituent inputs in Pawlik et al., Table 1. Carbon fibre is a **separate RVE phase**, not part of the epoxy–GnP interphase mixture. I used **20 vol% GnP within the interphase** ($c_r=0.20$, $c_m=0.80$), not 20 vol% throughout the CFRP.

## Calculation using Hill's parameters

**1. Converted the constituent elastic constants to Hill's parameters.** I used $(k,l,n,m,p)$ as defined in Ji's Eqs. (1)–(4). For isotropic epoxy:

$$
\begin{aligned}
k_m &= \frac{E_m}{2(1+\nu_m)(1-2\nu_m)},&
l_m &= \frac{\nu_mE_m}{(1+\nu_m)(1-2\nu_m)},\\
n_m &= \frac{(1-\nu_m)E_m}{(1+\nu_m)(1-2\nu_m)},&
m_m=p_m &= \frac{E_m}{2(1+\nu_m)}.
\end{aligned}
$$

This gives $k_m=4.022854$, $l_m=2.735541$, $n_m=5.310168$, and $m_m=p_m=1.287313$ GPa. I converted GnP's five elastic constants into its Hill parameters using Pawlik's Eq. (1) and Ji's transversely isotropic constitutive relation.

**2. Calculated the intermediate coefficients from Ji's Eq. (12).**

$$
\begin{aligned}
\alpha_r &= \frac{3k_m+2n_r-2l_r}{3n_r},\\
\beta_r &= \frac{4\mu_m+7n_r+2l_r}{15n_r}+\frac{2\mu_m}{5p_r},\\
\delta_r &= \frac{3k_m(n_r+2l_r)+4(k_rn_r-l_r^2)}{3n_r},\\
\eta_r &= \frac{2}{15}\left(k_r+6m_r+8\mu_m-\frac{l_r^2+2\mu_m l_r}{n_r}\right).
\end{aligned}
$$

For the epoxy, $\kappa_m=3.593750$ GPa and $\mu_m=1.287313$ GPa. My calculation gives $\alpha_r=-0.421116$, $\beta_r=0.686222$, $\delta_r=546.165746$ GPa, and $\eta_r=352.771403$ GPa.

**3. Applied Ji's Eq. (11) for randomly oriented platelets.** I calculated the effective bulk and shear moduli using Ji's Eq. (11):

$$
\kappa_I=\kappa_m+
\frac{c_r(\delta_r-3\kappa_m\alpha_r)}{3(c_m+c_r\alpha_r)},
\qquad
\mu_I=\mu_m+
\frac{c_r(\eta_r-2\mu_m\beta_r)}{2(c_m+c_r\beta_r)}.
$$

I obtained $\kappa_I=54.885887$ GPa and $\mu_I=38.738023$ GPa.

**4. Converted the isotropic effective moduli into ANSYS material inputs.** With Ji's Eq. (13) and the isotropic Poisson-ratio relation:

$$
E_I=\frac{9\kappa_I\mu_I}{3\kappa_I+\mu_I},
\qquad
\nu_I=\frac{3\kappa_I-E_I}{6\kappa_I}.
$$

| Calculated interphase property | Value |
|---|---:|
| Young's modulus, $E_I$ | **94.080348 GPa** |
| Poisson's ratio, $\nu_I$ | **0.2143153** |
| Shear modulus, $G_I=\mu_I$ | **38.738 GPa** |
| Bulk modulus, $K_I=\kappa_I$ | **54.886 GPa** |

I entered $E_I$ and $\nu_I$ as the homogeneous isotropic interphase material in ANSYS. The subsequent CFRP RVE had **66% carbon fibre, 26.3% epoxy and 7.7% reinforced interphase** by volume. I then used [normal loading](01-normal-loading.md) and [shear loading](02-shear-loading.md) to calculate the lamina properties.

The interphase properties above were calculated using the approaches described by **Pawlik et al. (2019)** and **Ji et al. (2010)**, then supplied to the RVE analysis following **Barbero (2014)**.
