.. _visualization:

Visualization
=============

.. objectives::

   - Learn how to use `py3Dmol <http://3dmol.csb.pitt.edu/>`_ for molecular visualization with VeloxChem.


.. code-block:: python
   :class: thebe

   import sys

   from mpi4py import MPI

   import veloxchem as vlx


.. code-block:: python
   :class: thebe

   # create geometry in XYZ format
   pyridine_xyz = """11

   C       -0.180226841      0.360945118     -1.120304970
   C       -0.180226841      1.559292118     -0.407860970
   C       -0.180226841      1.503191118      0.986935030
   N       -0.180226841      0.360945118      1.29018350
   C       -0.180226841     -0.781300882      0.986935030
   C       -0.180226841     -0.837401882     -0.407860970
   H       -0.180226841      0.360945118     -2.206546970
   H       -0.180226841      2.517950118     -0.917077970
   H       -0.180226841      2.421289118      1.572099030
   H       -0.180226841     -1.699398882      1.572099030
   H       -0.180226841     -1.796059882     -0.917077970
   """

   # just coordinates
   mol_str = "\n".join(pyridine_xyz.split("\n")[1:])

   molecule = vlx.Molecule.read_str(mol_str, units="angstrom")


.. code-block:: python
   :class: thebe

   basis = vlx.MolecularBasis.read(molecule, "def2-sv(p)")

   scf_settings = {'conv_thresh': 1.0e-6}
   method_settings = {'xcfun': 'slda', 'grid_level': 4}

   comm = MPI.COMM_WORLD
   ostream = vlx.OutputStream(sys.stdout)

   ostream.print_block(molecule.get_string())
   ostream.print_block(basis.get_string("Atomic Basis", molecule))
   ostream.flush()


.. code-block:: python
   :class: thebe

   scfdrv = vlx.ScfRestrictedDriver(comm, ostream)
   scfdrv.update_settings(scf_settings, method_settings)
   scfdrv.compute(molecule, basis)


.. code-block:: python
   :class: thebe

   # get the MOs and the density matrix
   mos = scfdrv.mol_orbs
   D = scfdrv.density

   # initialize visualization driver
   visdrv = vlx.VisualizationDriver(comm)

   # generate cube files
   visdrv.gen_cubes(cube_dict={"cubes": "mo(homo),mo(lumo)",}, molecule=molecule, basis=basis, mol_orbs=mos, density=D)


.. code-block:: python
   :class: thebe

   import py3Dmol as p3d

   # generate view
   v = p3d.view(width=400, height=400)

   v.addModel(pyridine_xyz, "xyz")
   v.setStyle({'stick':{}})

   with open("cube_2.cube", "r") as f:
       cube = f.read()

   # negative lobe
   v.addVolumetricData(cube, "cube", {"isoval": -0.02, "color": "blue", "opacity": 0.75})
   # positive lobe
   v.addVolumetricData(cube, "cube", {"isoval": 0.02, "color": "red", "opacity": 0.75})

   v.show()

And adding a button:

.. thebe-button:: Click me!
