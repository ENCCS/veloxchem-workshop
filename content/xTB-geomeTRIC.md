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
- Learn how to run geometry optimization using the semiempirical xTB method.
```

```{keypoints}
- Run a geometry optimization calculation.
- Plot the change of energy during optimization.
- Visualize the change of geometry during optimization.
- (Optional) Try geometry optimization with a different coordinate system.
```

## Introduction

In this exercise we will use [the semiempirical extended tight-binding (xTB)
method](https://onlinelibrary.wiley.com/doi/10.1002/wcms.1493),
combined with [the geomeTRIC optimization code](https://aip.scitation.org/doi/10.1063/1.4952956),
to optimize the geometry of zinc tetraphenylporphyrin dimer.

Geometry optimization is the procedure to find local minimum on the potential 
energy surface. A coordinate system is therefore necessary for describing the
geometry of the system of interest. The Cartesian coordinate system is the 
simplest; however, it is very inefficient due to the complexity of the potential
energy surface. In practice, it is common to employ the so-called internal coordinates
that describes the collective motion of atoms in a more efficient way. A displacement
in the internal coordinate $\Delta \mathbf{q}$ is related to the displacement in
Cartesian coordinates $\Delta \mathbf{x}$

$$
\Delta \mathbf{x} = \mathbf{B}^T \mathbf{G}^{-1} \Delta \mathbf{q}
$$

Here $\mathbf{B}$ is the Wilson B-matrix ($B_{ij} = \partial q_i / \partial x_j$)
and $\mathbf{G} = \mathbf{B} \mathbf{B}^T$.

In [the geomeTRIC optimization code](https://aip.scitation.org/doi/10.1063/1.4952956),
the translation-rotation internal coordinates
(TRIC) is employed. This coordinate system treats intra- and intermolecular 
coordinates separately by introducing translation and rotation coordinates
for the individual molecules in the system.

Efficient geometry optimization demands good prediction of the next step in the 
conformation space. This can be done based on a quadratic approximation for the local
shape of the potential energy, where an apprximate evaluation of the Hessian can be
provided by, for example, the Broyden-Fletcher-Goldfarb-Shanno (BFGS) method.

The gradient, or the first derivative of energy with respect to nuclear displacements,
is provided by [the semiempirical xTB method](https://onlinelibrary.wiley.com/doi/10.1002/wcms.1493),
which is an efficient tight-binding model
that covers almost the entire periodic table (Z $\le$ 86).

## System: zinc tetraphenylporphyrin dimer

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

## Input file

Below is the input file for geometry optimization of zinc tetraphenylporphyrin dimer.

```{literalinclude} inputs/zn-porphyrin-dimer.inp
```

## Results

The change of energy during optimization is printed at the end of the output file.

## Visualization of results

```{code-cell} ipython3
:class: thebe, thebe-init

import codecs
import re

import ipywidgets


class GeometryOptimizerUploader(ipywidgets.HBox):
    
    def __init__(self):
        super().__init__()
        self.geometries = []
        self.energies = []
        
        # define widgets
        uploader = ipywidgets.FileUpload(
            accept=".xyz",
            multiple=False
        )
        uploader.observe(self.on_upload_change, names='_counter')
        
        self.children = [uploader]

    def on_upload_change(self, change):
        if not change.new:
            return
        up = change.owner
        
        regex = re.compile(br"Iteration (?P<iteration>\d+) Energy (?P<energy>-\d+.\d+)", re.MULTILINE)
        for filename, data in up.value.items():
            print(f'uploaded {filename}')
            contents = data["content"]
            matches = regex.finditer(contents)
            self.energies = [float(m.group("energy")) for m in matches]
            # number of lines in each XYZ structure
            xyzs = codecs.decode(contents).splitlines()
            natoms = int(xyzs[0])
            lines_per_xyz = natoms + 2
            for lines in range(0, len(xyzs), lines_per_xyz):
                self.geometries.append("\n".join(xyzs[lines:lines+lines_per_xyz]))
        up.value.clear()
        up._counter = 0

up = GeometryOptimizerUploader()
up
```

```{code-cell} ipython3
:tags: [raises-exception]

import ipywidgets
import py3Dmol as p3d

%matplotlib widget
from matplotlib import pyplot as plt


# get list of geometries from uploader widget
geometries = up.geometries
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

# get list of energies from uploader widget
energies = up.energies
# output widget with energies
out_energies = ipywidgets.Output()
out_energies.clear_output(wait=True)

# display full trajectory plot with point for first geometry
with out_energies:
    fig, ax = plt.subplots(constrained_layout=True, figsize=(4, 2.5), num="Geometry optimization")
    line, = ax.plot(energies)
    ax.scatter(0, energies[0], s=10, c="red")

    # Labeling the axes
    ax.set_xlabel("Iteration")
    ax.set_ylabel("Energy (atomic units)")
    fig.canvas.toolbar_position = 'bottom'
    ax.grid(True)

@out_energies.capture(clear_output=True, wait=True)
def on_energy_change(change):
    idx = change["new"]
    ax.scatter(idx, energies[idx], s=10, c="red")
    fig.canvas.draw()
    
# a slider widgets, to select geometry to display
slider = ipywidgets.IntSlider(min=0, max=len(geometries)-1, step=1, continuous_update=True)
# a player widget, to show the whole optimization trajectory
player = ipywidgets.Play(min=0, interval=400)
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
