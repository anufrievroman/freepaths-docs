---
description: Example of electrical conductivity simulations for a membrane with holes
---

# Electrons in holey membranes

In this example, we use `electrons_in_holey_membrane.py` file to compute the electrical conductivity of a holey membrane, similar to those measured in \[1]. The input file sets electron mean free paths at a constant value of 15 nm.

```
ELECTRON_MFP = 15e-9
```

Moreover, since the electrical conductivity depends on the energy of electrons, we need to set the Fermi level of the crystal. For example, in _n_-doped silicon with $$3 \times 10^{18} cm^{-3}$$ dopant concentration, we can estimate the Fermi level at about $$50 meV$$ below the conduction band. This value can be set in the input file to get the electrical conductivity in the plots.

```python
from scipy.constants import electron_volt
MEDIA_FERMI_LEVEL = -50e-3 * electron_volt
```

To run the simulation for electrons, use the `-e` flag as follows:&#x20;

```
freepaths -e electrons_in_holey_membrane.py
```

The simulation will trace the electrons in a 100-nm-thick porous silicon membrane:

<figure><img src="../.gitbook/assets/image (20).png" alt="" width="209"><figcaption></figcaption></figure>

From the trajectories, the simulator calculates the spectra of mean travel time $$\tau_s(E)$$ and transport distribution function $$\Xi (E)$$:

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

From the transport distribution function, the electrical conductivity $$\sigma$$ is evaluated as explained in [the Section of electrical conductivity](../theory/electrical-conductivity.md) and Ref. \[2] using this equation:

<figure><img src="../.gitbook/assets/image (25).png" alt="" width="186"><figcaption></figcaption></figure>

In practice, the electrical conductivity is calculated and plotted as a function of Fermi energy:

<figure><img src="../.gitbook/assets/image (24).png" alt="" width="532"><figcaption></figcaption></figure>

The value obtained for our specific Fermi level is about 95 kS/m, which is roughly in the range reported in the experiments \[1]. The uncertainty comes from the fact that the Fermi level in the experimental samples is usually not known and only roughly estimated from the doping level, which in turn is also approximated. Thus, these simulations work best to compare different structures on the same wafer with the same doping rather than to obtain absolute values of electrical conductivity.

#### References

1. Lim et al.,[ _ACS Nano_ 10, 1, 124–132 (2016)](https://pubs.acs.org/doi/10.1021/acsnano.5b05385)
2. Priyadarshia and Neophytou, [J. Appl. Phys. 133, 054301 (2023)](https://doi.org/10.1063/5.0134466)
