.. _hpc-setup:


Setting up VeloxChem on a HPC cluster
=====================================

.. objectives::

   - Learn how to compile VeloxChem on your HPC cluster.


.. keypoints::

   - Make sure the Python dependencies are in a virtual environment.


Beskow (PDC)
^^^^^^^^^^^^

`Beskow <https://www.pdc.kth.se/hpc-services/computing-systems/beskow-1.737436>`_
is a Cray XC40 system at `PDC <https://www.pdc.kth.se/>`_.

VeloxChem is available as a module on Beskow::

  $ module load veloxchem/1.0rc2

Example submission script::

  #!/bin/bash

  #SBATCH -J myjob
  #SBATCH -t 00:30:00

  #SBATCH -A edu21.veloxchem
  #SBATCH --reservation workshop1

  #SBATCH --nodes=2
  #SBATCH --ntasks-per-node=1
  #SBATCH -C Haswell

  module load veloxchem/1.0rc2
  export OMP_NUM_THREADS=32

  job=porphyrin
  srun vlx ${job}.inp ${job}.out

.. note::

   To log in to Beskow, you'll need a PDC account, a Kerberos installation, and
   a SSH implementation that supports Kerberos. Please follow the instructions
   on `PDC's documentation page on login
   <https://www.pdc.kth.se/support/documents/login/login.html>`_


.. _Tetralith (NSC):

Tetralith (NSC)
^^^^^^^^^^^^^^^

`Tetralith <https://www.nsc.liu.se/systems/tetralith/>`_ is the main cluster at
`NSC <https://www.nsc.liu.se/>`_.

On Tetralith we can use the ``buildenv-intel/2018a-eb`` module that provides
Intel compilers, Intel MPI and Intel MKL, and
``Python/3.6.4-nsc2-intel-2018a-eb`` that provides Python.

You can install VeloxChem on Tetralith via the following steps:

- Load modules::

    module load buildenv-intel/2018a-eb
    module load Python/3.6.4-nsc2-intel-2018a-eb
    module load CMake/3.19.2

- Get veloxchem source code::

    mkdir -p $HOME/software
    cd $HOME/software

    git clone https://gitlab.com/veloxchem/veloxchem.git
    cd veloxchem

- Create virtual environment::

    python3 -m venv --system-site-packages venv
    source venv/bin/activate

    python3 -m pip install --upgrade pip setuptools

    CC=icc MPICC=mpiicc python3 -m pip install mpi4py --no-binary=mpi4py
    python3 -m pip install numpy pybind11 h5py pytest

- Install XTB::

    mkdir -p dependencies/src
    cd dependencies/src

    git clone -b v6.3.3 https://github.com/grimme-lab/xtb
    cd xtb; mkdir build; cd build
    CC=icc CXX=icpc FC=ifort cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX:PATH=$HOME/software/veloxchem/dependencies/xtb ..
    make
    make install
    cd ../..

    cd ../..

    export XTBHOME=$HOME/software/veloxchem/dependencies/xtb

- Install and test VeloxChem::

    cd $HOME/software/veloxchem/
    salloc -N 1 -t 30 -A ... --reservation devel
    VLX_NUM_BUILD_JOBS=32 mpirun -n 1 python3 setup.py install
    OMP_NUM_THREADS=16 mpirun -n 2 pytest -v python_tests

- Example submission script::

    #!/bin/bash

    #SBATCH --job-name=myjob
    #SBATCH --account=...
    #SBATCH --time=00:30:00

    #SBATCH --nodes=2
    #SBATCH --ntasks-per-node=1
    #SBATCH --cpus-per-task=32

    module load buildtool-easybuild/3.5.3-nsc17d8ce4
    module load intel/2018a
    module load Python/3.6.4-nsc2-intel-2018a-eb

    source $HOME/software/veloxchem/venv/bin/activate
    export OMP_NUM_THREADS=32

    job=porphyrin
    mpirun vlx ${job}.inp ${job}.out


.. _Kebnekaise (HPC2N):

Kebnekaise (HPC2N)
^^^^^^^^^^^^^^^^^^

`Kebnekaise <https://www.hpc2n.umu.se/resources/hardware/kebnekaise>`_ is the
latest supercomputer at `HPC2N <https://www.hpc2n.umu.se/>`_.

On Kebnekaise we can use the ``foss/2020b`` module that provides GNU compilers,
OpenMPI and OpenBLAS, and ``Python/3.8.6`` that provides Python.

You can install VeloxChem on Kebnekaise via the following steps:

- Load modules::

    module load foss/2020b
    module load Python/3.8.6
    module load CMake/3.18.4

- Get veloxchem source code::

    cd $HOME/software/
    git clone https://gitlab.com/veloxchem/veloxchem.git
    cd veloxchem

- Create virtual environment::

    python3 -m venv --system-site-packages venv
    source venv/bin/activate

    python3 -m pip install --upgrade pip setuptools

    CC=gcc MPICC=mpicc python3 -m pip install mpi4py --no-binary=mpi4py
    python3 -m pip install numpy pybind11 h5py pytest

- Install XTB::

    mkdir -p dependencies/src
    cd dependencies/src

    git clone -b v6.3.3 https://github.com/grimme-lab/xtb
    cd xtb; mkdir build; cd build
    CC=gcc CXX=g++ FC=gfortran cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX:PATH=$HOME/software/veloxchem/dependencies/xtb ..
    make
    make install
    cd ../..

    cd ../..

    export XTBHOME=$HOME/software/veloxchem/dependencies/xtb

- Install and test VeloxChem::

    export OPENBLASROOT=$EBROOTOPENBLAS

    cd $HOME/software/veloxchem/
    salloc -N 1 -t 30 -A ...
    VLX_NUM_BUILD_JOBS=28 mpirun -n 1 python3 setup.py install
    OMP_NUM_THREADS=14 mpirun -n 2 pytest -v python_tests

- Example submission script::

    #!/bin/bash

    #SBATCH --job-name=myjob
    #SBATCH --account=...
    #SBATCH --time=00:30:00

    #SBATCH --nodes=2
    #SBATCH --ntasks-per-node=1
    #SBATCH --cpus-per-task=28

    module load foss/2020b
    module load Python/3.8.6
    module load CMake/3.18.4

    source $HOME/software/veloxchem/venv/bin/activate
    export OMP_NUM_THREADS=28

    job=porphyrin
    mpirun vlx ${job}.inp ${job}.out

- Known issue

  On Kebnekaise you may encounter the ``fock()`` warning from OpenMPI, if you import MPI before h5py::

    $ python3
    Python 3.8.6 (default, Feb 19 2021, 13:45:45)
    [GCC 10.2.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> from mpi4py import MPI
    >>> import h5py
    [1620306933.125286] [b-an01:921717:0]          ib_md.c:1140 UCX  WARN  IB: ibv_fork_init() was disabled or failed, yet a fork() has been issued.
    [1620306933.125305] [b-an01:921717:0]          ib_md.c:1141 UCX  WARN  IB: data corruption might occur when using registered memory.

  This warning is similar to that documented in `this link
  <https://github.com/h5py/h5py/issues/1079#issuecomment-516816031>`_, which
  disappears if h5py is imported prior to MPI::

    $ python3
    Python 3.8.6 (default, Feb 19 2021, 13:45:45)
    [GCC 10.2.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    >>> import h5py
    >>> from mpi4py import MPI

  In case of this rare problem a practical workaround for VeloxChem is to add
  ``import h5py`` in line 25 of ``src/pymodule/__init__.py`` and then rerun the
  ``mpirun -n 1 python3 setup.py install`` command.


Other HPC cluster
^^^^^^^^^^^^^^^^^

If you use Intel compiler you can refer to the installation steps for :ref:`Tetralith (NSC)`.

If you use GNU compiler you can refer to the installation steps for :ref:`Kebnekaise (HPC2N)`.


Exercise
^^^^^^^^

The purpose of the exercise is to check that your VeloxChem can run a simple
calculation.  You can test with the `cpp.inp
<https://gitlab.com/veloxchem/veloxchem/-/raw/master/docs/inputs/cpp.inp>`_
file.

- On Beskow::

    salloc -N 1 -t 00:20:00 -A edu21.veloxchem --reservation workshop1
    module load veloxchem/1.0rc2
    export OMP_NUM_THREADS=16
    wget https://gitlab.com/veloxchem/veloxchem/-/raw/master/docs/inputs/cpp.inp
    srun -n 2 vlx cpp.inp
    exit

- On Tetralith::

    salloc -N 1 -t 00:20:00 -A <your-project-ID> --reservation devel
    # You can use pre-installed VeloxChem, if you like
    #source /home/x_lixin/Public/tetralith/veloxchem/env.sh
    # or load your own virtual environment
    export OMP_NUM_THREADS=16
    wget https://gitlab.com/veloxchem/veloxchem/-/raw/master/docs/inputs/cpp.inp
    mpirun -n 2 vlx cpp.inp
    exit

- On Kebnekaise::

    salloc -N 1 -t 00:20:00 -A <your-project-ID>
    # You can use pre-installed VeloxChem, if you like
    #source /home/l/lxin/Public/kebnekaise/env.sh
    # or load your own virtual environment
    export OMP_NUM_THREADS=14
    wget https://gitlab.com/veloxchem/veloxchem/-/raw/master/docs/inputs/cpp.inp
    mpirun -n 2 vlx cpp.inp
    exit

- On other HPC cluster

  You can refer to the examples for the above HPC clusters.
