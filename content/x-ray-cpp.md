---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

(x-ray-cpp)=

# Complex polarization propagator in the X-ray region

```{objectives}
- Learn how to run a complex polarization propagator (CPP) calculation.
```

```{keypoints}
- write me
```

In this section we will use an efficient implementation of the complex
polarization propagator approach (CPP) to compute the near-edge X-ray
absorption fine-structure spectrum of free-base porphyrin.

Conventional response theory solves a generalized eigenvalue problem and
provides excitation energies starting from the lowest excited states, and is
impractical to study spectral regions with high density-of-states. The CPP
approach introduces a damping term which, from a purely computational
perspective, removes the singularities of the response functions at resonance
frequencies. The damped response theory can be applied to any frequency region
of interest.


$$
\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}
$$


```{code-cell} ipython3
:tags: [remove-input]

import py3Dmol as p3d

v = p3d.view(width=400, height=400)

with open("inputs/porphyrin.xyz", "r") as fh:
    porphyrin_xyz = fh.read()

v.addModel(porphyrin_xyz, "xyz")
v.setStyle({'stick':{}})
v.zoomTo()
v.show()
```

Here is the input file for running the CPP calculation

.. include:: inputs/porphyrin.inp
