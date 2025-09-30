---
description: Spectral hybrid transmission model used in FreePATHS (2T case)
---

# Spectral hybrid transmission model used in FreePATHS (2T case)

### Scope and geometry
We model a SiGe inclusion embedded in Si as a thin *mini-layer*.  
A phonon crossing this inclusion experiences two sequential interface events:

1. entrance: Si → Ge  
2. exit: Ge → Si  

The total transmission probability is the product of the two steps:

$$
\alpha^{\mathrm{tot}}_{2T}(\theta,\omega,p)
= T_{1}(\theta_i,\omega,p)\,\times\,T_{2}(\theta_t,\omega,p)
$$

where $\theta_i$ is the incident angle in Si, $\theta_t$ is the transmitted angle inside Ge determined by Snell’s law (4), $\omega$ is the angular frequency, and $p$ the branch/polarization. The SMMM model is utilized for the simulation and is summarized in this document; a detailed description is provided in [RanCao2024].

---

### Equation used in the code

**Dispersion → wavenumber and wavelength.**

For each material $m \in \{i,j\}$ and branch $p$, the code interpolates the dispersion tables to obtain $k_m(\omega,p)$ and thus the wavelength. Here, classes Si and Ge are called. Note that the Ge class has been calculated approximately and will need to be verified in the future, but the Si class already exists and nothing has changed.

$$
\lambda_m(\omega,p) = \frac{2\pi}{k_m(\omega,p)}
$$

**Spectral specularity (roughness model).**

The interface specularity follows Ziman’s exponential law. It is different from the Spectral Specularity equation used in FreePATHS. For the SMMM model, this equation is angle-independent. (In document [RanCao2024] it corresponds to Equation 32).

$$
P_m(\omega,p) = \exp\!\left(-\frac{16\pi^2 \eta^2}{\lambda_m(\omega,p)^2}\right)
$$

with $\eta$ is the roughness.  

**Snell's law.**

In the SMMM method, for specular scattering, the transmitted phonons are deverted satisfying Snell's law (Equation 18 in the paper [RanCao2024]):

$$
\frac{v_{g,i}(\omega,p)}{\sin\theta_i} =
\frac{v_{g,j}(\omega,p)}{\sin\theta_j}
$$

with $\theta_i$ the incident angle between the projection to the x-y plane and y-axis and $\theta_j$ the exit angle. $v_{g,i}$ and $v_{g,j}$ are the group velocities of the incident and transmitted material, respectively.  

To satisfy Snell’s relation, the computed $\sin\theta_j$ must lie in $[-1,1]$.  
If $\bigl|\tfrac{v_{g,j}}{v_{g,i}}\sin\theta_i\bigr|>1$ (equivalently $|\sin\theta_j|>1$), no real transmitted angle exists: the code sets $\alpha^{\mathrm{Spec}}_{i\to j}=0$ and returns a specular reflection. In that case, only the diffuse channel can still transmit phonons across the layer.  

In scattering primitives with “2T” signature, two tests are made:  
(i) entry Si→Ge  
(ii) exit Ge→Si  
Both must lie in $[-1,1]$, otherwise the code sends a specular reflection.

**Specular (AMM-like) transmission.**

For the specular specularity we use the classical AMM form with acoustic impedances $Z_m=\rho_m v_{g,m}$ (simplification of Equation 25 in [RanCao2024]):

$$
\alpha^{\mathrm{Spec}}_{i\to j}(\theta_i,\omega,p) =
\frac{4 Z_i Z_j |\cos\theta_i|\,|\cos\theta_j|}
{(Z_i |\cos\theta_i| + Z_j |\cos\theta_j|)^2}
$$

where $\theta_j$ is obtained from Snell’s law.  
(The absolute values ensure nonnegative $\cos$ factors in code.)

**Diffuse (DMM-like) transmission.**

For the diffuse scattering we use the equation below (Equation 30 in [RanCao2024]):

$$
\alpha^{\mathrm{Diff}}_{i\to j}(\omega,p) =
\frac{(1-P_j)\,k_j(\omega,p)^2}
{(1-P_i)\,k_i(\omega,p)^2+(1-P_j)\,k_j(\omega,p)^2}
$$

---

### One interface: spectral convex combination (SMMM form)

Following the spectral mixed-mismatch idea, the total transmissivity of one interface is the convex combination of the specular and diffuse parts weighted by the *incident-side* specularity:

$$
\alpha_{i\to j}(\theta_i,\omega,p) =
P_i(\omega,p)\,\alpha^{\mathrm{Spec}}_{i\to j}(\theta_i,\omega,p)
+ (1-P_i(\omega,p))\,\alpha^{\mathrm{Diff}}_{i\to j}(\omega,p)
$$

(The reverse direction $j \to i$ uses $P_j$ and the corresponding angles and specularity.)

---

### Two interfaces in series (Si→Ge then Ge→Si)

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

In practice, we compute $k_i,k_j$ from the tabulated dispersions, then $\lambda_i,\lambda_j$, then $P_i,P_j$; we obtain $\theta_t$ from Snell’s law, and finally evaluate the equations above.

---

### Modeling notes (as coded)

- **Elastic, branch-conserving scattering:** no inelasticity or polarization conversion; transmissions are branch-wise (LA/TA) and elastic.  
- **Total internal reflection (TIR):** if $(v_{g,i}/v_{g,j})\sin\theta_i > 1$, then $\alpha^{\mathrm{Spec}}_{i\to j}=0$ and only the diffuse formula contributes.  
- **Independent sequential events:** the two interfaces (entry/exit) are treated as independent; coherent interference and internal multiple reflections are neglected for simplicity; this matches the intended use for thin, rough, incoherent mini-layers.  
- **Angle handling:** $|\cos\theta|$ is used in the AMM formula to avoid sign issues for grazing angles in numerical implementation.  

---

### Justification for the 2T construction

Representing the SiGe layer as a finite *mini-rectangle* naturally yields two heterogeneous boundaries.  
The 2T product allows the inclusion to act as a virtual slab, forcing an entry (Si→Ge) and an exit (Ge→Si) event, each mixed by roughness-dependent specularity via the SMMM equations.  

A simple per-frequency, per-branch model that captures roughness at both interfaces while respecting detailed balance between AMM and DMM.  

By contrast, if the geometry involves only a single Si/Ge boundary (for example, a rectangle of SiGe inside the Si sample), the proper choice is the 1T case: drop the 2T formula and use the single-interface SMMM instead.

---

### Reference

X. Ran and B. Cao, *Roughness dependence of phonon-interface thermal transport: Theoretical model and Monte Carlo simulation*, Phys. Rev. B **110**, 024302 (2024).
