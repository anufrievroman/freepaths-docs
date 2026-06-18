---
description: Calculating thermal conductivity by integrating the phonon dispersion
---

# MFP sampling mode

The MFP sampling mode is an alternative way to calculate the thermal conductivity that is faster and more direct than the Fourier law approach. Instead of tracing thousands of phonons to convergence and fitting a temperature gradient, it traces a small number of phonons (around 30) to measure the effective mean free path at each frequency, then integrates over the full phonon dispersion.

For a detailed explanation of the underlying formula, see the [thermal conductivity theory page](../theory/themal-conductivity-calculation.md#mean-free-path-approach).

### When to use it

| | Fourier law (main mode) | MFP sampling mode |
|---|---|---|
| Number of phonons needed | 5000+ | ~30 |
| What it measures | Temperature gradient + heat flux | Mean free path per frequency |
| Output | Full temperature maps, heat flux plots | Thermal conductivity in terminal |
| Best for | Visualizing transport, complex geometries | Quick thermal conductivity estimates |

Both approaches should give consistent results for simple structures, as shown in the [nanowire example](../basic-tutorials/nanowire.md).

### How to run it

Take any existing input file, reduce the number of particles to around 30, and add the `-s` flag:

```
freepaths -s your_input_file.py
```

The thermal conductivity is printed directly to the terminal. Statistical distributions and plots are still generated in the `Results` folder as usual.

### Example

Using the `examples/nanowire.py` input file with the number of particles reduced to 30:

```
freepaths -s nanowire.py
```

This produces a thermal conductivity of approximately 51 W/m·K, consistent with the Fourier law result of ~54 W/m·K from the same structure.

### Tips

* Around 30 phonons is usually sufficient for a good estimate. More phonons reduce the statistical noise but increase computation time.
* The result can vary slightly between runs due to the Monte Carlo randomness. Run a few times and average if precision is needed.
* The mode measures the average distance between diffuse scattering events as the effective mean free path Λ = v·τ for each frequency. Specular scattering events are not counted, so highly specular structures (very smooth walls at low temperature) will report a longer effective MFP.
* This mode does not produce reliable results if pillars are present in the structure.
