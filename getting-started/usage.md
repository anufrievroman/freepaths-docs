---
description: How to run FreePATHS simulations
---

# Usage

FreePATHS is a command line application, so it runs inside Linux, MacOS, or Windows terminal. It takes an input file or config file from the user, which contains all the settings, and outputs the results in a new folder. For an extensive guide on creating config files, please see [config-file-creation-guide.md](config-file-creation-guide.md).

There are two modes of using the program.

* The **main mode** traces many phonons through a structure and collects statistics about their paths. This mode calculates the thermal flux and Temperature profile of the sample and uses this to calculate effective thermal conductivity.
* The **MFP sampling mode** measures phonon mean free paths using a few phonons and calculates the thermal conductivity by integrating phonon dispersion.

### Demo

First, if you simply run `freepaths` without specifying an input file, the program will run a [demo simulation](../basic-tutorials/nanowire.md) and output some demo results.

### Main mode

In the main mode, the program traces many phonons through a structure and calculates various statistical distributions and maps. In this mode, the thermal conductivity will be calculated via Fourier law, as [explained here](../theory/themal-conductivity-calculation.md#fourier-law-approach).

Run the program as:

```
freepaths your_input_file.py
```

In the `examples` folder, you will find example input files. Try using one of them, for instance, as:

```
freepaths nanowire.py
```

After the simulation, see the results in a newly created `Results` folder.

### Mean free path sampling mode

Alternatively, you can run FreePATHS in the mean free path sampling mode, which is designed to calculate the thermal conductivity by integrating phonon dispersion, as [explained here](../theory/themal-conductivity-calculation.md#mean-free-path-approach). To run the program in this mode, reduce the number of phonons to about 30 and add the `-s` flag in the command:

```
freepaths -s simple_nanowire.py
```

The thermal conductivity will be output in the terminal. However, other statistical quantities and plots will still be calculated and output in the `Results` folder.

### Troubleshooting

* If simulations are too slow, try using [multiprocessing](config-file-creation-guide.md#multiprocessing-parameter).
* Rarely, phonons may enter a hole in the structure or break out of structure boundaries. To reduce the impact of this bug, reduce the `TIMESTEP` parameter. However, this usually happens once per thousands of collisions and has negligible impact on the final statistics.
* If you have an error similar to `Cannot mix incompatible Qt library (5.15.7) with this library (5.15.8)` it likely means that you have a program like `qt5-styleplugins` that didn't upgrade to the latest Qt library with the rest of the system.
