---
description: Example of electrical conductivity simulations for a membrane with holes
---

# Electrons in holey membranes

In this example, we use `examples/electrons_in_holey_membrane.py` file to compute the electrical conductivity and Seebeck coefficient of a holey membrane, similar to those measured in Ref. \[1]. The input file sets electron mean free paths at a constant value of 15 nm.

```
ELECTRON_MFP = 15e-9
```

Moreover, since the electrical conductivity depends on the energy of electrons, we need to set the Fermi level of the crystal. For example, in _n_-doped silicon with $$3 \times 10^{18} cm^{-3}$$ dopant concentration, we can estimate the Fermi level at about -50 meV from the conduction band. This value can be set in the input file to get values directly in the plots.

```python
from scipy.constants import electron_volt
MEDIA_FERMI_LEVEL = -50e-3 * electron_volt
```

To run the simulation for electrons, use the `-e` flag as follows:&#x20;

```
freepaths -e electrons_in_holey_membrane.py
```

The simulation will trace the electrons in a 100-nm-thick porous silicon membrane:

<figure><img src="../.gitbook/assets/image (27).png" alt="" width="257"><figcaption></figcaption></figure>

From the trajectories, the simulator calculates the spectra of mean travel time $$\tau_s(E)$$ and transport distribution function $$\Xi (E)$$:

<div><figure><img src="../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure></div>

From the transport distribution function, the electrical conductivity $$\sigma$$ and Seebeck coefficient are evaluated as explained in [the Section of electrical conductivity](../theory/electrical-conductivity.md) and Ref. \[2]. In practice, they are calculated and plotted as a function of Fermi energy:

<div><figure><img src="../.gitbook/assets/image (30).png" alt="" width="534"><figcaption></figcaption></figure> <figure><img src="../.gitbook/assets/image (31).png" alt="" width="563"><figcaption></figcaption></figure></div>

Thus, the values obtained for our specific Fermi level are about $$\sigma$$ = 85 kS/m and S = 0.3 mV/K, roughly in the range reported in the experiments \[1]. The uncertainty comes from the fact that the Fermi level in the experimental samples is usually not known and only roughly estimated from the doping level, which in turn is also approximated. Thus, these simulations work best to compare different structures on the same wafer with the same doping rather than to obtain absolute values.

#### References

1. Lim et al.,[ _ACS Nano_ 10, 1, 124–132 (2016)](https://pubs.acs.org/doi/10.1021/acsnano.5b05385)
2. Priyadarshia and Neophytou, [J. Appl. Phys. 133, 054301 (2023)](https://doi.org/10.1063/5.0134466)
