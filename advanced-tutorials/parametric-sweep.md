---
description: How to run multiple simulations with varied parameters
---

# Parametric sweep

The `examples/sweep_parameter_script.py`, shows an example of how one can run several simulations one after another so that the input file remains the same except for one parameter. In this example, we sweep the temperature parameter `T` in the input file called `point_line.py` as defined as `temperatures = [4, 100, 200, 300]` list.

```
import subprocess

FILENAME = "point_line.py"

def update_config_file(config_file, temperature):
    with open(config_file, 'r') as f:
        lines = f.readlines()
    for i, line in enumerate(lines):
        if line.startswith("OUTPUT_FOLDER_NAME"):
            lines[i] = f"OUTPUT_FOLDER_NAME = {temperature}\n"
        if line.startswith("T "):
            lines[i] = f"T = {temperature}\n"

    with open(config_file, 'w') as f:
        f.writelines(lines)

temperatures = [4, 100, 200, 300]
for temp in temperatures:
    update_config_file(FILENAME, temp)
    subprocess.run(['python', '-m', 'freepaths', FILENAME])
```

Essentially, for each temperature, the script changes and saves the input file, runs the simulation, and proceeds to the next temperature. As a result, several folders will appear in the `Results` folder with different temperatures.
