.. _exciton:


Exciton calculation
===================

.. objectives::

   - Learn how to run an *ab initio* exciton model calculation.

.. keypoints::

   - Run an *ab initio* exciton model calculation.
   - Plot the UV-Vis absorption and ECD spectra.
   - Analyze the character of the excitations.

Introduction
------------

In this exercise we will use an *ab initio* exciton model to compute
the UV-Vis absorption and electronic ciruclar dichroism (ECD) of
stacked base-pairs.

The Frenkel exciton model describes the electronic structure of
multi-chromophoric system by dividing the system into subgroups.
It has been most successful in the weak-coupling limit where
the excitons are localized on inidividual chromophores.
The *ab initio* exciton model expands the Frenkel exciton model by
taking into account charge-transfer between the chromophores, and
is therefore more useful for studies of singly excited states.
You may read more in `this paper <https://pubs.acs.org/doi/abs/10.1021/acs.jctc.7b00171>`_
:cite:`Li2017-eq`

In the exciton model, the Hamiltonian adopts the following matrix form

.. math::

   \mathbf{H} =
   \sum_I^N E_I |\phi_I\rangle \langle \phi_I| +
   \sum_{I \ne J}^N V_{IJ} |\phi_I\rangle \langle \phi_J|

where :math:`N` is the total number of excited states, :math:`\phi_I` is
the :math:`I`-th excitonic basis function, and :math:`E` and :math:`V` are the
excitation energies and couplings, respectively.
Diagonalization of the Hamiltonian gives the eigenvalues and
eigenvectors for the excited states.

System: stacked base-pairs
--------------------------

.. raw:: html

   <div style="height: 400px; width: 600px; position: relative;" class='viewer_3Dmoljs' data-href='../_static/stacked-base-pairs.xyz' data-type='xyz' data-backgroundcolor='0xffffff' data-style='stick'></div>


Input file
----------

Below is the input file for *ab initio* exciton model calculation of stacked
base-pairs. You can read more about the VeloxChem input keywords in
`this page <https://docs.veloxchem.org/inputs/keywords.html>`_.

.. literalinclude:: inputs/stacked-base-pairs.inp
   :emphasize-lines: 10-16

Exercise
--------

- Submit a job

    Runs the above example.
    On Beskow this calculation can finish within 15 minutes on
    2 nodes. You can choose to run on more nodes.

- Plot the spectrum

    The excitation energies, oscillator strengths, and rotatory strengths will be
    printed at the end of the output file.
    You can plot the UV-Vis and ECD spectra by line broadening using e.g.
    `Gaussian function <https://en.wikipedia.org/wiki/Gaussian_function>`_.

- Examine the character of the excitations

    The character of the excitations will also be printed at the end of the output
    file. Find out the characters of the important excitations.

- Compare with full TDDFT-TDA result

    Compare the spectra from exciton model and from full TDDFT-TDA calculation
    (:download:`link to TDDFT-TDA output file<inputs/tddft-tda.out>`).
