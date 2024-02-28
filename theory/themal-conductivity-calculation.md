---
description: Calculation of thermal conductivity via Fourier law or MFP
---

# Thermal conductivity

FreePATHS can calculate the thermal conductivity of the structure using two different approaches, via **Fourier law** or via **mean free path**. Although both quantities are called thermal conductivity, they are different quantities obtained in very different ways and capture different nanoscale effects. This page will explain both of these approaches.

## Fourier law approach

Traditionally, the thermal conductivity is a coefficient in the Fourier law:

$$
J = \kappa \nabla T,
$$

In this approach, we use the main algorithm of FreePATHS to obtain heat flux (J) and temperature gradient (dT/dL) in the steady state regime. An e[xample of such simulation is described here](../basic-tutorials/nanowire.md). To use this approach, one must understand the [time parameters of the simulation](../getting-started/config-file-creation-guide.md#simulation-time-parameters). Specifically, that over several timeframes, the simulation is trying to achieve steady state thermal flow and then measured the profiles used for the thermal conductivity calculation.

<figure><img src="../.gitbook/assets/image (14).png" alt="" width="563"><figcaption><p>Schematic of the simulation time.</p></figcaption></figure>

We can see the convergence of the temperature and heat flux profiles as the time progresses and the simulation reaches steady state:

<div>

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption><p>Temperature profiles at different time instervals converge to the linear profile.</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption><p>Heat flux profiles converge to the flat line.</p></figcaption></figure>

</div>

The temperature gradient is obtained by a linear regression and the heat flux value through the mean over the _y_ profile. As the simulation progresses, you can see the value for the thermal conductivity converge:

<figure><img src="../.gitbook/assets/image (15).png" alt="" width="563"><figcaption><p>Thermal conductivity converges as the system reaches steady state.</p></figcaption></figure>

There are two heat flux profiles calculated in the simulation. The one called `Heat flux profile effective.pdf` is calculated by averaging the heat flux over the entire simulation domain, and the `Heat flux profile material.pdf` only averages the heat flux where there is material, so not where there are holes. Then both the effective and material thermal conductivity are provided in `Thermal conductivity.pdf` (although in the example above they are the same).

## Mean free path approach

Alternatively, you can run FreePATHS in the mean free path sampling mode, which is designed to calculate the thermal conductivity by integrating phonon dispersion and sampling the phonon relaxation time (or phonon mean free paths) for each frequency. To run the program in this mode, reduce the number of phonons to about 30 and add the `-s` flag in the command:

```
freepaths -s your_input_file.py
```

In this mode, the thermal conductivity at a given temperature (_T_) is calculated as:

$$
\kappa = \frac{1}{6\pi^2} \sum_{j} \int \frac{\hslash^2\omega_j^2(q)}{k_bT^2} \frac{\exp\left[\hslash\omega_j(q)/k_bT\right]}{(\exp\left[\hslash\omega_j(q)/k_bT\right]-1)^2} v_j^2(q) \tau_j(q,T) q^2 dq
$$

where _k_ is the Boltzmann constant, ω(q) and _v_(q) are the frequency and group velocity on the branch _j_ of the phonon dispersion at the wavevector _q._ The phonon relaxation time _τ_ (or the phonon mean free path Λ = _v_(_q_)·τ) is measured by running phonons through the structure and recording the average of the distances between diffuse scattering events.

For example, if we take the same input file as in [the nanowire example](../basic-tutorials/nanowire.md), reduce the number of phonons to 30 and run it with `-s` flag:

```
freepaths -s nanowire.py
```

We obtain the thermal conductivity of about 51 W/m·K, consistent with the Fourier law approach above.

## Effective vs Material thermal conductivity

One must distinguish two different quantities, _material_ and _effective_ thermal conductivity. For simple structures without holes (like a [nanowire](../basic-tutorials/nanowire.md)) these quantities are the same. However, if the structure has non-uniform volume or pores or pillars, these quantities are not the same and the effective thermal conductivity is lower. For example, for a structure with some holes, FreePATHS will typical output a plot showing both quantities, as follows:

<figure><img src="../.gitbook/assets/image (18).png" alt="" width="563"><figcaption><p>Example of  material and effective thermal conductivities of porous structure.</p></figcaption></figure>

The **effective thermal conductivity** is essentially the conductivity of a "black box" structure with a given thickness, width, and length, regardless of how much material has been removed inside the box. This quantity is useful in engineering, but note that it can be physically misleading as one can achieve the effective conductivity even below the amorphous limit simply removing lots of material, for example by making dense holes.&#x20;

The **material thermal conductivity** "takes into account" the volume reduction due to material removal and essentially represents the thermal conductivity of the material between the holes. This quantity typically reflects the reduction in thermal conductivity caused by phonon scattering on the holes.
