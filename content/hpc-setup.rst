.. _hpc-setup:


Setting up VeloxChem on a HPC cluster
=====================================

.. objectives::

   - Learn how to download and compile VeloxChem on your HPC cluster.


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


Tetralith (NSC)
^^^^^^^^^^^^^^^

`Tetralith <https://www.nsc.liu.se/systems/tetralith/>`_ is the main cluster at
`NSC <https://www.nsc.liu.se/>`_.

You can install VeloxChem on Tetralith via the following steps:

- Load modules::

    module load buildenv-intel/2018a-eb
    module load Python/3.6.4-nsc2-intel-2018a-eb
    module load CMake/3.19.2

- Get veloxchem source code::

    cd /path/to/your/installation/folder/
    git clone https://gitlab.com/veloxchem/veloxchem.git
    cd veloxchem

- Create virtual environment::

    python3 -m venv --system-site-packages venv
    source venv/bin/activate

    python3 -m pip install --upgrade pip setuptools

    CC=icc CC=mpiicc python3 -m pip install mpi4py --no-binary=mpi4py
    python3 -m pip install numpy pybind11 h5py pytest

- Install XTB::

    mkdir -p dependencies/src
    cd dependencies/src

    git clone -b v6.3.3 https://github.com/grimme-lab/xtb
    cd xtb; mkdir build; cd build
    CC=icc CXX=icpc FC=ifort cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX:PATH=/path/to/your/installation/folder/veloxchem/dependencies/xtb ..
    make
    make install
    cd ../..

    cd ../..

    export XTBHOME=/path/to/your/installation/folder/veloxchem/dependencies/xtb

- Install and test VeloxChem::

    cd /path/to/your/installation/folder/
    salloc -N 1 -t 55 -A ... --reservation devel
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

    source /path/to/your/installation/folder/veloxchem/venv/bin/activate
    export OMP_NUM_THREADS=32

    job=porphyrin
    mpirun vlx ${job}.inp ${job}.out
