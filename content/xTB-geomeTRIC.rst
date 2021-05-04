.. _xTB-geomeTRIC:


Geometry optimizations and semiempirical Hamiltonians
=====================================================

.. objectives::

   - Learn how to run geometry optimization using the semiempirical xTB method.

.. keypoints::

   - Run a geometry optimization calculation.
   - Plot the change of energy during optimization.
   - Visualize the change of geometry during optimization.
   - (Optional) Try geometry optimization with a different coordinate system

Introduction
------------

In this exercise we will use `the semiempirical extended tight-binding (xTB)
method <https://onlinelibrary.wiley.com/doi/10.1002/wcms.1493>`_ :cite:`Bannwarth2021-ai`,
combined with `the geomeTRIC optimization code <https://aip.scitation.org/doi/10.1063/1.4952956>`_ :cite:`Wang2016-jn`,
to optimize the geometry of the zinc tetraphenylporphyrin dimer.

Geometry optimization is the procedure to find local minimum on the potential
energy surface. A coordinate system is therefore necessary for describing the
geometry of the system of interest. The Cartesian coordinate system is the
simplest; however, it is very inefficient due to the complexity of the potential
energy surface. In practice, it is common to employ the so-called internal coordinates
that describes the collective motion of atoms in a more efficient way. A displacement
in the internal coordinate :math:`\Delta \mathbf{q}` is related to the displacement in
Cartesian coordinates :math:`\Delta \mathbf{x}`:

.. math::

   \Delta \mathbf{x} = \mathbf{B}^T \mathbf{G}^{-1} \Delta \mathbf{q}

Here :math:`\mathbf{B}` is the Wilson B-matrix, with elements:

.. math::

   B_{ij} = \frac{\partial q_i}{\partial x_j}

and :math:`\mathbf{G} = \mathbf{B} \mathbf{B}^T`.

In the geomeTRIC optimization code, the translation-rotation internal coordinate
(TRIC) system is employed. This coordinate system treats intra- and
intermolecular coordinates separately by introducing translation and rotation
coordinates for the individual molecules in the system.

Efficient geometry optimizations demand good prediction of the next step in the
conformation space. This can be done based on a quadratic approximation for the local
shape of the potential energy, where an apprximate evaluation of the Hessian can be
provided by, for example, the Broyden-Fletcher-Goldfarb-Shanno (BFGS) method.

The gradient, or the first derivative of the energy with respect to nuclear
displacements, is provided by the semiempirical xTB method, which is an
efficient tight-binding model that covers almost the entire periodic table (Z
$\le$ 86).

System: zinc tetraphenylporphyrin dimer
---------------------------------------

.. raw:: html

   <div style="height: 400px; width: 600px; position: relative;" class='viewer_3Dmoljs' data-href='../_static/zn-porphyrin-dimer.xyz' data-type='xyz' data-backgroundcolor='0xffffff' data-style='stick'></div>


Input file
----------

Below is the input file for the geometry optimization of the zinc tetraphenylporphyrin dimer.

.. literalinclude:: inputs/zn-porphyrin-dimer.inp
   :emphasize-lines: 5-11

Results
-------

The change of energy during optimization is printed at the end of the output file.
