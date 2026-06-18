---
description: Theory behind electron simulations
---

# Electrical conductivity

Essentially, we want to solve the Boltzmann transport equation:

<figure><img src="../.gitbook/assets/image (3) (1).png" alt="" width="375"><figcaption></figcaption></figure>

We use the relaxation time approximation to compute the collision term:

<figure><img src="../.gitbook/assets/image (1) (1).png" alt="" width="375"><figcaption></figcaption></figure>

And we can compute the average time of flight $$\langle ToF \rangle$$ _i.e_. the average time it takes for an electron to travel from one side of the structure to another. Once this time is known, we can compute the flux per simulated electron as $$F(E) = \langle ToF \rangle^{-1}$$, from which, we can compute the transport distribution function:

<p align="center"> <span class="math">\Xi (E) = C \times F(E) \times g(E)</span></p>

where $$g(E)$$ is the density of states, and $$C$$ is the mapping constant. Then, the electrical conductivity is given by the:

<p align="center"><span class="math">\sigma = q^2 \int \Xi(E) \left(-\frac{\partial f}{\partial E} \right) dE</span></p>

and Seebeck coefficient is given by:

<p align="center"><span class="math">S = \frac {qk_b}{\sigma} \int \Xi(E) \left(-\frac{\partial f}{\partial E} \right) \left(\frac{E - E_f}{k_bT} \right) dE</span></p>

### Mapping constant C

C is a calibration constant that bridges the MC simulation output to physically meaningful units. The MC simulation produces raw time-of-flight values, which do not carry the correct dimensions or scale to be used directly as a TDF. C corrects for this.

C is defined as the ratio of the analytical BTE conductivity for the pristine bulk material to the raw MC-derived conductivity for the same pristine material:

$$C = \frac{\sigma_\mathrm{BTE}}{\sigma_\mathrm{MC,\,raw}}$$

where the analytical (bulk) conductivity in the numerator is computed from:

$$\Xi_\mathrm{bulk}(E) = \Lambda \cdot v(E) \cdot g(E)$$

with Λ the bulk electron mean free path, $$v(E) = \sqrt{2E/m^*}$$ the group velocity, and $$g(E)$$ the 3D density of states. This is the exact BTE result for a pristine crystal with acoustic phonon scattering only. The denominator is the conductivity obtained from the MC simulation of the same pristine structure.

Once C is determined this way, it is kept fixed and reused for any nanostructured geometry. The nanostructuring effect then appears entirely through the change in the simulated travel times, not through C.

**Important:** C must be calibrated on a pristine simulation (no holes or scatterers). See the [MEAN\_MAPPING\_CONSTANT parameter](../getting-started/config-file-creation-guide.md#electron-parameters) for the required two-step workflow.

See additional details in Ref. \[1].

### References

1. Priyadarshia and Neophytou, [J. Appl. Phys. 133, 054301 (2023)](https://doi.org/10.1063/5.0134466)
