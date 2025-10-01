---
description: Formalism used to model transmission through thin layers or another material
---

# Transmission equations

## Spectral hybrid transmission model

#### Scope and geometry

For example, we want to model phonon transport through a SiGe layer embedded in Si crystal.\
A phonon crossing this inclusion experiences two sequential interface events:

1. entrance: Si → Ge
2. exit: Ge → Si

Thus, the total transmission probability is the product of the two steps:

$$
\alpha^{\mathrm{tot}}_{2T}(\theta,\omega,p)
= T_{1}(\theta_i,\omega,p)\,\times\,T_{2}(\theta_t,\omega,p)
$$

where θ<sub>i</sub> is the incident angle in Si, θ<sub>t</sub> is the transmitted angle inside Ge determined by Snell’s law (4), ω is the angular frequency, and _p_ the branch/polarization. The SMMM model used in the simulation and is summarized here. A detailed description is provided in \[1].

***

#### Equation used in the code

For each material _m_ and branch _p_, the code interpolates the dispersion relation to obtain _k_<sub>_m_</sub> and thus the wavelength:

$$
\lambda_m(\omega,p) = \frac{2\pi}{k_m(\omega,p)}
$$

The interface specularity $$P_m$$ depends on the roughness $$\eta$$ and follows Ziman’s exponential law. It is different from the angle-dependent specularity equation used in FreePATHS. For the SMMM model, this equation is angle-independent. (Eq. 32 in \[1]).

$$
P_m(\omega,p) = \exp\!\left(-\frac{16\pi^2 \eta^2}{\lambda_m(\omega,p)^2}\right)
$$

In case of specular scattering, the transmitted phonons are diverted, satisfying Snell's law (Eq. 18 in \[1]):

$$
\frac{v_{g,i}(\omega,p)}{\sin\theta_i} =
\frac{v_{g,j}(\omega,p)}{\sin\theta_j}
$$

with _θ_<sub>_i_</sub> the incident angle between the projection to the x-y plane and y-axis and _θ_<sub>_j_</sub> is the exit angle, _v_<sub>_i_</sub> and _v_<sub>j</sub> are the group velocities of the incident and transmitted material, respectively. In case of:

$$
\bigl|\tfrac{v_{g,j}}{v_{g,i}}\sin\theta_i\bigr|>1
$$

no real transmitted angle exists: the code sets $$\alpha^{\mathrm{Spec}}_{i\to j}=0$$

and returns a specular reflection. In that case, only the diffuse channel can still transmit phonons across the layer.

In scattering primitives with “2T” signature, two tests are made:

1. Entry Si → Ge
2. Exit Ge → Si

Both must lie in $$[-1, 1]$$, otherwise the code sends a specular reflection.

**Specular (AMM-like) transmission.**

For the specular specularity we use the classical AMM form with acoustic impedances&#x20;

$$Z_m=\rho_m v_{g,m}$$&#x20;

(which is simplified Eq. 25 in \[1]):

$$
\alpha^{\mathrm{Spec}}_{i\to j}(\theta_i,\omega,p) =
\frac{4 Z_i Z_j |\cos\theta_i|\,|\cos\theta_j|}
{(Z_i |\cos\theta_i| + Z_j |\cos\theta_j|)^2}
$$

where θ<sub>j</sub> is obtained from Snell’s law.

**Diffuse (DMM-like) transmission.**

For the diffuse scattering, we use the equation below (Eq. 30 in \[1]):

$$
\alpha^{\mathrm{Diff}}_{i\to j}(\omega,p) =
\frac{(1-P_j)\,k_j(\omega,p)^2}
{(1-P_i)\,k_i(\omega,p)^2+(1-P_j)\,k_j(\omega,p)^2}
$$

***

#### One interface: spectral convex combination (SMMM form)

Following the spectral mixed-mismatch idea, the total transmissivity of one interface is the convex combination of the specular and diffuse parts weighted by the _incident-side_ specularity:

$$
\begin{aligned}
\alpha_{i\to j}(\theta_i,\omega,p)
&= P_i(\omega,p)\,\alpha^{\mathrm{Spec}}_{i\to j}(\theta_i,\omega,p) + (1-P_i(\omega,p))\,\alpha^{\mathrm{Diff}}_{i\to j}(\omega,p)
\end{aligned}
$$

(The reverse direction $$j \to i$$ uses $$P_j$$ and the corresponding angles and specularity.)

***

#### Two interfaces in series (Si→Ge then Ge→Si)

For the Ge mini-layer, we evaluate:

$$
T_{1}(\theta_i,\omega,p) =
P_i\,\alpha^{\mathrm{Spec}}_{i\to j}(\theta_i,\omega,p) +
(1-P_i)\,\alpha^{\mathrm{Diff}}_{i\to j}(\omega,p)
$$

$$
T_{2}(\theta_t,\omega,p) =
P_j\,\alpha^{\mathrm{Spec}}_{j\to i}(\theta_t,\omega,p) +
(1-P_j)\,\alpha^{\mathrm{Diff}}_{j\to i}(\omega,p)
$$

and combine them as:

$$
\alpha^{\mathrm{tot}}_{2T}(\theta,\omega,p) = T_1 \times T_2
$$

In practice, we compute wave vectors from the tabulated dispersions, then wavelengths, then P<sub>i</sub>, P<sub>j</sub>; we obtain _θ_<sub>_t_</sub> from Snell’s law, and finally evaluate the equations above.

***

#### Modeling notes (as coded)

* **Elastic, branch-conserving scattering:** no inelasticity or polarization conversion; transmissions are branch-wise (LA/TA) and elastic.
* **Total internal reflection (TIR):** if $$(v_{g,i}/v_{g,j})\sin\theta_i > 1$$, then $$\alpha^{\mathrm{Spec}}_{i\to j}=0$$ and only the diffuse formula contributes.
* **Independent sequential events:** the two interfaces (entry/exit) are treated as independent; coherent interference and internal multiple reflections are neglected for simplicity; this matches the intended use for thin, rough, incoherent mini-layers.
* **Angle handling:** $$|\cos\theta|$$ is used in the AMM formula to avoid sign issues for grazing angles in numerical implementation.

***

#### Justification for the 2T construction

Representing the SiGe layer as a finite _mini-rectangle_ naturally yields two heterogeneous boundaries.\
The 2T product allows the inclusion to act as a virtual slab, forcing an entry (Si→Ge) and an exit (Ge→Si) event, each mixed by roughness-dependent specularity via the SMMM equations.

A simple per-frequency, per-branch model that captures roughness at both interfaces while respecting detailed balance between AMM and DMM.

By contrast, if the geometry involves only a single Si/Ge boundary (for example, a rectangle of SiGe inside the Si sample), the proper choice is the 1T case: drop the 2T formula and use the single-interface SMMM instead.

***

#### References

1. X. Ran and B. Cao, _Roughness dependence of phonon-interface thermal transport: Theoretical model and Monte Carlo simulation_, Phys. Rev. B **110**, 024302 (2024).
