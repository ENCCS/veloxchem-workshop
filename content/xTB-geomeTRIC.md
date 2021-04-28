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

(xTB-geomeTRIC)=

# Geometry optimizations and semiempirical Hamiltonians

```{objectives}
- Learn how to drive a semiempirical calculation with xTB from VeloxChem.
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

with open("inputs/zn-porphyrin-dimer.xyz", "r") as fh:
    zn_porphyrin_dimer_xyz = fh.read()

v.addModel(zn_porphyrin_dimer_xyz, "xyz")
v.setStyle({'stick':{}})
v.zoomTo()
v.show()
```

```{code-cell} ipython3
:tags: [hide-output, raises-exception]

import re

import ipywidgets
import py3Dmol as p3d

%matplotlib widget
from matplotlib import pyplot as plt

# upload the file and change the name here
fname = "???_optim.xyz"

# this we know beforehand
natoms = 178

# read the whole file in
with open(fname, "r") as fh:
    contents = fh.read()

# get a list of all energies
regex = re.compile(r"Iteration (?P<iteration>\d+) Energy (?P<energy>-\d+.\d+)", re.MULTILINE)
matches = regex.finditer(contents)
energies = [float(m.group("energy")) for m in matches]

# number of lines in each XYZ structure
lines_per_xyz = natoms + 2

contents = contents.splitlines()
geometries = []
for lines in range(0, len(contents), lines_per_xyz):
    geometries.append("\n".join(contents[lines:lines+lines_per_xyz]))
    
# output widget with geometries
out_geometries = ipywidgets.Output()
out_geometries.clear_output(wait=True)

# display first geometry
with out_geometries:
    v = p3d.view(width=300, height=300)
    v.addModel(geometries[0], "xyz")
    v.setStyle({"stick": {}})
    v.zoomTo()
    v.show()
    
@out_geometries.capture(clear_output=True, wait=True)
def on_geometry_change(change):
    idx = change["new"]
    v = p3d.view(width=300, height=300)
    v.addModel(geometries[idx], "xyz")
    v.setStyle({"stick": {}})
    v.zoomTo()
    v.show()

# output widget with energies
out_energies = ipywidgets.Output()
out_energies.clear_output(wait=True)

# display full trajectory plot with point for first geometry
with out_energies:
    fig, ax = plt.subplots(constrained_layout=True, figsize=(4, 2.5), num="Geometry optimization")
    line, = ax.plot(energies)
    ax.scatter(0, energies[0], s=20, c="red")

    # Labeling the axes
    ax.set_xlabel("Iteration")
    ax.set_ylabel("Energy (atomic units)")
    fig.canvas.toolbar_position = 'bottom'
    ax.grid(True)

@out_energies.capture(clear_output=True, wait=True)
def on_energy_change(change):
    idx = change["new"]
    ax.scatter(idx, energies[idx], s=20, c="red")
    fig.canvas.draw()
    
# a slider widgets, to select geometry to display
slider = ipywidgets.IntSlider(min=0, max=len(geometries)-1, step=1, continuous_update=True)
# a player widget, to show the whole optimization trajectory
player = ipywidgets.Play(min=0, interval=100)
# put control widget in a vertical box widget
controls = ipywidgets.VBox([slider, player])

# link slider widget with geometry change
slider.observe(on_geometry_change, 'value')
# link slider widget with energy change
slider.observe(on_energy_change, 'value')
# link player and slider widgets
ipywidgets.jslink((player, 'value'), (slider, 'value'))
# put controls and output widget in horizontal box widget
ipywidgets.HBox([controls, out_geometries, out_energies])
```
