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
:tags: [remove-input]

import re
import codecs
import ipywidgets

class GeometryOptimizerUploader(ipywidgets.HBox):
    
    def __init__(self):
        super().__init__()
        self.geometries = []
        self.energies = []
        
        # define widgets
        uploader = ipywidgets.FileUpload(
            accept=".xyz",  # Accepted file extension e.g. '.txt', '.pdf', 'image/*', 'image/*,.pdf'
            multiple=False  # True to accept multiple files upload else False
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
            natoms = contents[0]
            lines_per_xyz = natoms + 2
            for lines in range(0, len(xyzs), lines_per_xyz):
                self.geometries.append("\n".join(xyzs[lines:lines+lines_per_xyz]))
        up.value.clear()
        up._counter = 0

up = GeometryOptimizerUploader()
display(up)
```

```{code-cell} ipython3
:tags: [remove-input]

import numpy as np
from pathlib import Path
import ipywidgets
import py3Dmol as p3d

%matplotlib widget
from matplotlib import pyplot as plt


class GeometryOptimizationVisualizer(ipywidgets.HBox):
     
    def __init__(self, energies, geometries):
        super().__init__()
        self.energies = energies
        self.geometries = geometries

        # iteration vs energy plot
        out_plot = ipywidgets.Output()
        out_plot.clear_output(wait=True)
        with out_plot:
            self.fig, self.ax = plt.subplots(constrained_layout=True, figsize=(4, 2.5), num="Geometry optimization")
        self.line, = self.ax.plot(self.energies)
        self.ax.scatter(0, self.energies[0], s=20, c="red")

        # Labeling the axes
        self.ax.set_xlabel("Iteration")
        self.ax.set_ylabel("Energy (atomic units)")
        self.fig.canvas.toolbar_position = 'bottom'
        self.ax.grid(True)
        
        # molecular visualization
        out_mol = ipywidgets.Output()
        out_mol.clear_output(wait=True)
        with out_mol:
            view = p3d.view(width=400, height=400)
            view.addModel(self.geometries[0], "xyz")
            view.setStyle({"stick": {}})
            view.zoomTo()
            view.show()
         
        int_slider = ipywidgets.IntSlider(
            value=0, 
            min=0, 
            max=len(self.energies)-1, 
            step=1, 
            description='Iteration',
            continuous_update=True,
        )
        player = ipywidgets.Play(
            min=0,
            max=len(self.energies)-1,
            interval=100,
        )
 
        controls = ipywidgets.VBox([
            int_slider,
            player,
        ])
        #controls.layout = make_box_layout()
         
        out_box = ipywidgets.Box([out_plot, out_mol])
        out_plot.layout = make_box_layout()
        out_mol.layout = make_box_layout()
 
        # observe stuff
        int_slider.observe(self.update, 'value')
        # link player and slider
        widgets.jslink((player, 'value'), (int_slider, 'value'))
 
        # add to children
        self.children = [controls, out_plot, out_mol]
    
    def update(self, change):
        """Draw line in plot"""
        idx = change["new"]
        self.ax.scatter(idx, self.energies[idx], s=20, c="red")
        self.fig.canvas.draw()
        
        view = p3d.view(width=400, height=400)
        view.addModel(self.geometries[idx], "xyz")
        view.setStyle({"stick": {}})
        view.zoomTo()
        view.show()
        
GeometryOptimizationVisualizer(up.energies, up.geometries)
```
