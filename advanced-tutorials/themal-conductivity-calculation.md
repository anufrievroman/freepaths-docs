---
description: Calculation of thermal conductivity via Fourier law
---

# Thermal conductivity calculation

FreePATHS will calculate the thermal conductivity of the structure after the simulation. This page will give some additional information on this feature.

## How the calculation is done

The basis of the calculation is the Fourier law:

$$
J = \kappa \nabla T,
$$

The thermal flux and temperature gradient are obtained through the temperature and heat flux profiles:

<div>

<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption><p>Temperature profiles at different time instervals converge to the linear profile.</p></figcaption></figure>

 

<figure><img src="../.gitbook/assets/image (17).png" alt=""><figcaption><p>Heat flux profiles converge to the flat line.</p></figcaption></figure>

</div>

The temperature gradient is obtained by a linear regression and the heat flux value through the mean over the _y_ profile. As the simulation progresses, you can see the value for the thermal conductivity converge:

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption><p>Thermal conductivity converges as the system reaches steady state.</p></figcaption></figure>

There are two heat flux profiles calculated in the simulation. The one called `Heat flux profile effective.pdf` is calculated by averaging the heat flux over the entire simulation domain, and the `Heat flux profile material.pdf` only averages the heat flux where there is material, so not where there are holes. This means that both the effective and material thermal conductivity are provided in `Thermal conductivity.pdf` (although if you are interested in the material thermal conductivity, the MFP sampling mode could be more interesting).

## Useful tips

* The software can currently only calculate the thermal conductivity in the y direction. Also, make sure that the phonons are generated on the very side of the simulation.
* The software does not give correct results if pillars are present in the simulation.
* Because the calculation relies on the pixel grid to calculate the profiles, make sure that it is small enough and looks good (some discontinuities in the pixels adjacent to holes are expected and correct).
* Make sure that you simulate enough phonons (> 5000) to get valid results.
* Make sure that [time parameters](../getting-started/config-file-creation-guide.md#simulation-time-parameters) are set correctly.
