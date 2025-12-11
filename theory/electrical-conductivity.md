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

and Seeback coefficient it given by:

<p align="center"><span class="math">S = \frac {qk_b}{\sigma} \int \Xi(E) \left(-\frac{\partial f}{\partial E} \right) \left(\frac{E - E_f}{k_bT} \right) dE</span></p>

Also, we want our prediction to match the bulk electron conductivity before we proceed to the calculations on nanostructures, so we need to compute the mapping constant as:

<figure><img src="../.gitbook/assets/image (2) (1).png" alt="" width="277"><figcaption></figcaption></figure>

See additional details in the Ref. \[1].

### References

1. Priyadarshia and Neophytou, [J. Appl. Phys. 133, 054301 (2023)](https://doi.org/10.1063/5.0134466)
