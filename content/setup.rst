.. _setup:

Setting up your system
======================

In order to follow this workshop, you will need access to VeloxChem.

You can work on the content of **Day 1** entirely online using `Binder
<https://mybinder.org>`_ to run the Jupyter notebooks in the cloud.
If you prefer to work locally, you will need to set up your Python environment correctly:
we recommend using the `Conda package and enviroment manager
<https://docs.conda.io/en/latest/>`_, as it provides a convenient way to install
binary packages, including VeloxChem, in an isolated, reproducible software environment.

.. todo::

   * Mention that on PDC there is a module (or there will be)
   * These instructions depend on the final release.

   For the content of Day 2, you will have to compile VeloxChem on your cluster.


Install Miniconda
^^^^^^^^^^^^^^^^^

Follow the official `Miniconda
<https://docs.conda.io/en/latest/miniconda.html>`_  installation instructions
for your platform:

- For Linux see https://docs.conda.io/en/latest/miniconda.html#linux-installers
- For macOS see https://docs.conda.io/en/latest/miniconda.html#macosx-installers
- For Windows see https://docs.conda.io/en/latest/miniconda.html#windows-installers

For all platforms, make sure to select the **Python 3.8**, **64 bit** installer.

.. note::

   The VeloxChem binary package is currently only avaiable for the `x86_64`
   architecture.


Creating an environment and installing packages
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Once you have ``conda`` installed and correctly configured you can use the
:download:`environment.yml <../environment.yml>` file to install the
dependencies.  First save it to your hard drive by clicking the link, and then
in a terminal (Anaconda Prompt on Windows) navigate to where you saved the file
and type::

  $ conda env create -f environment.yml


You then need to activate the new environment by::

  $ conda activate workshop


Now you should have VeloxChem, Python, JupyterLab and a few other packages
installed!


.. _compiling:

Compiling VeloxChem from source
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. todo::

   Write me!!! Or point to the VeloxChem main page
