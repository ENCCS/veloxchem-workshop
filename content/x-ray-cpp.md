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
