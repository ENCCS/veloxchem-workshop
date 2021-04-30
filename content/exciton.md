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

(exciton)=

# Exciton calculation

```{objectives}
- Learn how to run an exciton calculation.
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

with open("inputs/stacked-base-pairs.xyz", "r") as fh:
    base_stack_xyz = fh.read()

v.addModel(base_stack_xyz, "xyz")
v.setStyle({'stick':{}})
v.zoomTo()
v.show()
```
