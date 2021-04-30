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
- Run a CPP calculation.
- Plot and analyze the absorption spectrum.
- Perform scalability test of the CPP calculation.
```

## Introduction

In this exercise we will use an efficient implementation of the complex
polarization propagator approach (CPP) to compute the near-edge X-ray
absorption fine-structure spectrum of free-base porphyrin.

Conventional response theory solves a generalized eigenvalue problem and
provides excitation energies starting from the lowest excited states. It is therefore
impractical to study spectral regions with high density-of-states. The CPP
approach introduces a damping term which, from a purely computational
perspective, removes the singularities of the response functions at resonance
frequencies. The damped response theory can be applied to any frequency region
of interest. You may read more in [this paper](https://pubs.acs.org/doi/10.1021/ct500114m).

The CPP solver in VeloxChem will solve multiple frequencies simultaneously. The
complex response equations for a given frequency can be expressed in terms of a
coupled set of linear equations for a real symmetric matrix

$$
\begin{pmatrix}
 E^{[2]} & -\omega S^{[2]}  & \gamma S^{[2]}  & 0 \\
 -\omega S^{[2]} & E^{[2]} & 0 & \gamma S^{[2]} \\
 \gamma S^{[2]} & 0 & -E^{[2]} &\omega S^{[2]} \\
 0 & \gamma S^{[2]}  & \omega S^{[2]} & -E^{[2]}
\end{pmatrix}
\begin{pmatrix}
X^R_g\\
X^R_u\\
X^I_u\\
X^I_g
\end{pmatrix} =
\begin{pmatrix}
G^R_g\\
G^R_u\\
-G^I_u\\
-G^I_g
\end{pmatrix}
$$

Here, $\omega$ is the frequency, $\gamma$ is the damping parameter, $E^{[2]}$
and $S^{[2]}$ are the Hessian and metric matrices, respectively, and $G$ is the
gradient vector. The $R$/$I$ superscripts denote real/imagnary components,
while the $g$/$u$ subscripts denote $gerade$/$ungerade$ symmetry.

## Molecule: free-base porphyrin

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

## Input file

Below is the input file for CPP calculation of free-base porphyrin.
You can find more about the VeloxChem input keywords in
[this page](https://docs.veloxchem.org/inputs/keywords.html).

```{literalinclude} inputs/porphyrin.inp
```

## Results

The absorption spectrum will be printed at the end of the output file.
Plot the spectrum and fit it to a sum of variable-width Gaussians.

## Scalability test

Run the CPP calculation on different number of nodes and plot the speedup
with respect to the number of nodes.
