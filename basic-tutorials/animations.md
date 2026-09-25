---
description: Creating animations of phonon trajectories
---

# Animations

To create animated gif files of phonon trajectories, simply add the following to your input file:

```
NUMBER_OF_TIMESTEPS            = 300
LOW_MEMORY_USAGE               = False

# Animation parameters:
OUTPUT_PATH_ANIMATION          = True
OUTPUT_TRAJECTORIES_OF_FIRST   = 30
OUTPUT_ANIMATION_FPS           = 24
```

The `LOW_MEMORY_USAGE = False` line matters: in the low memory mode the particle position is not recorded at the timesteps where scattering occurs, which leaves the saved trajectories incomplete.

Note that `NUMBER_OF_TIMESTEPS` should not be too large, otherwise the generation of animation may take a very long time, because one frame for each time step will be created. A few hundred time steps is a reasonable value. The file below shows an example of animated phonon trajectories:

{% file src="../.gitbook/assets/Collimation.mp4" %}
Example of animated phonon paths.
{% endfile %}

