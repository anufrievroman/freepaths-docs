---
description: Theory behind electron simulations
---

# Electrical conductivity

The goal is to compute the thermoelectric transport coefficients of a nanostructured material by solving the Boltzmann transport equation (BTE). In steady state and under the relaxation time approximation (RTA), the BTE reduces to:

$$
\mathbf{v} \cdot \nabla_r f = -\frac{f - f_0}{\tau(E)}
$$

where $$f$$ is the non-equilibrium carrier distribution, $$f_0$$ is the equilibrium Fermi–Dirac distribution, $$\mathbf{v}$$ is the carrier group velocity, and $$\tau(E)$$ is the energy-dependent relaxation time.

### Monte Carlo approach

Instead of solving the BTE analytically, FreePATHS solves it stochastically. Electrons are injected one by one from the hot side and traced through the structure until they either exit from the cold side or are scattered back. Only electrons that reach the cold side contribute to the flux. The average time of flight $$\langle ToF(E) \rangle$$ is the mean time those electrons take to cross the structure at a given energy:

$$
\langle ToF(E) \rangle = \frac{\sum_r t_r(E)}{N_r(E)}
$$

where the sum runs over the $$N_r(E)$$ electrons that successfully reach the cold side. The flux per simulated electron at each energy is then:

$$
F(E) = \frac{1}{\langle ToF(E) \rangle}
$$

### Transport distribution function

From the flux, the transport distribution function (TDF) is formed:

$$
\Xi(E) = C \times F(E) \times g(E)
$$

where $$g(E)$$ is the 3D parabolic-band density of states and $$C$$ is the mapping constant described below.

### Transport coefficients

All thermoelectric transport coefficients are computed as integrals of the TDF weighted by the Fermi–Dirac derivative $$(-\partial f / \partial E)$$, and are output as a function of the Fermi energy $$E_f$$:

**Electrical conductivity:**

$$
\sigma = q^2 \int \Xi(E) \left(-\frac{\partial f}{\partial E}\right) dE
$$

**Seebeck coefficient:**

$$
S = -\frac{qk_B}{\sigma} \int \Xi(E) \left(-\frac{\partial f}{\partial E}\right) \left(\frac{E - E_f}{k_BT}\right) dE
$$

**Power factor:**

$$
PF = \sigma S^2
$$

**Electronic thermal conductivity:**

$$
\kappa_{el} = \frac{1}{T} \int \Xi(E) \left(-\frac{\partial f}{\partial E}\right)(E - E_f)^2 \, dE - \sigma S^2 T
$$

These are computed as a function of $$E_f$$ and saved as PDF plots and CSV files (`Electron conductivity.pdf`, `Seebeck coefficient.pdf`, `Power factor.pdf`, `Electron thermal conductivity.pdf`). The value at the material's Fermi level is also marked on each plot.

{% hint style="warning" %}
**Phonon drag is not included.** The Seebeck coefficient computed here accounts only for the diffusion contribution. Phonon drag — the additional Seebeck enhancement arising from momentum transfer from the phonon system to electrons — is neglected. This effect can be significant at low temperatures or in high-purity materials, so the computed $$S$$ values may be underestimated in those regimes.
{% endhint %}

### Figure of merit ZT

The thermoelectric figure of merit is:

$$
ZT = \frac{S^2 \sigma}{\kappa_{el} + \kappa_{ph}} T
$$

FreePATHS computes ZT vs Fermi level and saves it to `Data/ZT.csv` and `ZT.pdf`, but only when the phonon thermal conductivity $$\kappa_{ph}$$ is available in the same output folder. This means **phonon simulations must be run first**, in the same `OUTPUT_FOLDER_NAME`, before running the electron simulation:

1. Run a phonon simulation (main tracing or MFP sampling mode) for your structure. This writes `Data/Average thermal conductivity.csv` or `Data/Thermal conductivity from MFP.csv` to the output folder.
2. Run the electron simulation (`-e` flag) with the same `OUTPUT_FOLDER_NAME`. FreePATHS will find the phonon κ file and compute ZT automatically.

If no phonon κ file is found, FreePATHS will print a warning and skip the ZT output.

### Mapping constant C

C is a calibration constant that bridges the MC simulation output to physically meaningful units. The MC simulation produces raw time-of-flight values, which do not carry the correct dimensions or scale to be used directly as a TDF. C corrects for this.

C is defined as the ratio of the analytical BTE conductivity for the pristine bulk material to the raw MC-derived conductivity for the same pristine material:

$$
C = \frac{\sigma_\mathrm{BTE}}{\sigma_\mathrm{MC,\,raw}}
$$

where the analytical (bulk) conductivity in the numerator is computed from:

$$
\Xi_\mathrm{bulk}(E) = \frac{\Lambda}{3} \cdot v(E) \cdot g(E)
$$

with $$\Lambda$$ the bulk electron mean free path, $$v(E) = \sqrt{2E/m^*}$$ the group velocity, and $$g(E)$$ the 3D density of states. The factor of 1/3 is the 3D angular average $$\langle v_x^2/v^2 \rangle$$. This is the exact BTE result for a pristine crystal with acoustic phonon scattering only. The denominator is the conductivity obtained from the MC simulation of the same pristine structure.

Once C is determined this way, it is kept fixed and reused for any nanostructured geometry. The nanostructuring effect then appears entirely through the change in the simulated travel times, not through C.

**Important:** C must be calibrated on a pristine simulation (no holes or scatterers). See the [MEAN\_MAPPING\_CONSTANT parameter](../getting-started/config-file-creation-guide.md#electron-parameters) for the required two-step workflow.

### References

1. Priyadarshi and Neophytou, [J. Appl. Phys. 133, 054301 (2023)](https://doi.org/10.1063/5.0134466)
