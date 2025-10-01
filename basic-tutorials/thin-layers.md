---
description: Thin layers of another material in the crystal
---

# Thin layers

This example shows how to add layers in a specific material to your structure and can be reproduced with `examples/layers.py` input file. The structure contains an array of layers (or "interfaces") defined in the input file as:

```
INTERFACES = []
period = 20e-9 # modify this value to change the distance between two interfaces
start_x = -WIDTH / 2 + period
end_x = WIDTH / 2 - period
INTERFACE_ROUGHNESS = 1.5e-9  # roughness of the interfaces

x = start_x
while x <= end_x:
    INTERFACES.append(VerticalPlane(position_x=x, roughness=INTERFACE_ROUGHNESS, inner_material='Ge', outer_material=MEDIA, depth = THICKNESS)) # add the material of the layers here
    x += period
```

Here, the `INTERFACE` list is filled with the `VerticalPlane` objects. This will create a structure that looks like the figure below, where the thin layers are indicated by the black lines:\


<figure><img src="../.gitbook/assets/image.png" alt="" width="333"><figcaption></figcaption></figure>

If we increase the number of phonons to several thousand, we can see some interesting curves. For example, the transmission factor as a function of the incident angles. Each curve corresponds to one frequency, and each color corresponds to one mode.\


<figure><img src="../.gitbook/assets/image (2).png" alt="" width="375"><figcaption></figcaption></figure>

To better observe the influence of frequency on transmission, this curve shows the phonon incident angle as a function of its frequency, with the transmission factor represented as a color map.

<figure><img src="../.gitbook/assets/image (3).png" alt="" width="375"><figcaption></figcaption></figure>

The file `information.txt` also contains various statistical insights, like the average transmission:

```
95% of particles reached the cold side

8.62% - scattering on side walls (99.88% - diffuse, 0.12% - specular)
1.60% - scattering on top and bottom walls (90.00% - diffuse, 10.00% - specular)
0.78% - rethermalization at the hot side
71.27% - internal scattering processes
18.33% - scattering on interfaces (24.08% - diffuse, 75.92% - specular)
67.81% - transmission through interfaces (24.72% - diffuse, 75.28% - specular)
```
