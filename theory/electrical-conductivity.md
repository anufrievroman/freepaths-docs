---
description: How to run electron simulations
---

# Electrical conductivity

{% hint style="danger" %}
This is work in progress
{% endhint %}

To run the simulation for electrons, use the `-e` flag as follows:

`freepaths -e my_input_file.py`

### Formalism

Essentially, we want to solve the Boltzmann transport equation:

<figure><img src="../.gitbook/assets/image (3).png" alt="" width="375"><figcaption></figcaption></figure>

We use the relaxation time approximation to compute the collision term:

<figure><img src="../.gitbook/assets/image (1).png" alt="" width="375"><figcaption></figcaption></figure>

And we can compute the average time of flight _τ_(_E_) , i.e. the time it takes for an electron to travel from one side of the structure to another. Once it is known, we can compute the physical quantities:

<figure><img src="../.gitbook/assets/image.png" alt="" width="563"><figcaption></figcaption></figure>

Also, we want our prediction to match the bulk electron conductivity before we proceed to the calculations on nanostructures, so we need to compute the mapping constant as:

<figure><img src="../.gitbook/assets/image (2).png" alt="" width="277"><figcaption></figcaption></figure>

To be continued...



### References

1. Priyadarshia and Neophytou, [J. Appl. Phys. 133, 054301 (2023)](https://doi.org/10.1063/5.0134466)
