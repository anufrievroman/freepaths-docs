---
description: Silicon nanowire at 300 K
---

# Nanowire

The example input file `examples/nanowire.py`, located in the [examples](https://github.com/anufrievroman/freepaths/tree/master/examples) folder, shows thermal transport simulation in a simple nanowire at room temperature. Let's run this input file as:

```
freepaths nanowire.py
```

After the simulation, we can see the output files in the Results folder. For example, phonon paths in such a structure showing mostly diffusive behavior:

<figure><img src="../.gitbook/assets/paths.jpg" alt="" width="563"><figcaption><p>Example of phonon paths in the structure.</p></figcaption></figure>

Also, the algorithm calculates the thermal profiles, heat flux, and the thermal conductivity at different time intervals. From the profile plots, we can see how the temperature and heat flux profiles converge after about 6th timeframe, as the system is reaching the state:

<figure><img src="../.gitbook/assets/image (16).png" alt="" width="375"><figcaption><p>Temperature profiles at different time intervals.</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (17).png" alt="" width="375"><figcaption><p>Heat flux profiles converge to the flat line.</p></figcaption></figure>

This can be used to estimate the [thermal conductivity](../theory/themal-conductivity-calculation.md) of the structure via the Fourier law. The  plot of thermal conductivity shows the convergence and the averaging in the steady state interval:

<figure><img src="../.gitbook/assets/image (15).png" alt="" width="563"><figcaption><p>Thermal conductivity convergance and averaging over the steady state period.</p></figcaption></figure>

For a simple nanowire, the [material and effective](../theory/themal-conductivity-calculation.md#effective-vs-material-thermal-conductivity) thermal conductivities are identical and equal to about 54 W/m·K, which is consistent with experimental observations.

Now, we can try another approach to the [thermal conductivity calculation](../theory/themal-conductivity-calculation.md#mean-free-path-approach). We take the same input file but reduce the number of phonons to 30 and run it with `-s` flag:

```
freepaths -s nanowire.py
```

The program calculates [the integral over the phonon branches](../theory/themal-conductivity-calculation.md#mean-free-path-approach), and we obtain a similar value of the thermal conductivity of about 51 W/m·K.

## Tips for thermal conductivity calculations

* The software can currently only calculate the thermal conductivity in the y direction. Also, make sure that the phonons are generated at the edge of the simulation domain.
* The software does not give correct results if pillars are present in the simulation.
* Because the calculation relies on the pixel grid to calculate the profiles, make sure that pixels are small enough and maps look good (some discontinuities in the pixels adjacent to holes are expected and correct).
* Simulate enough phonons (> 5000) to get valid results.
* Ensure that [time parameters](../getting-started/config-file-creation-guide.md#simulation-time-parameters) are set correctly.
