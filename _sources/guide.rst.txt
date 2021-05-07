Instructor's guide
==================

Editing the material
^^^^^^^^^^^^^^^^^^^^

This website uses `sphinx-lesson <https://coderefinery.github.io/sphinx-lesson/>`_. Pages can be added in a variety of formats:

- Jupyter notebooks (``.ipynb``), like all the pages for Day 1.
- reStructuredText (``.rst``), as usual for Sphinx-based documentation pages.
- Markdown (``.md``), like all the pages for Day 2.

However, note that we **do not** use plain Markdown, but the `MyST
<https://myst-parser.readthedocs.io/en/latest/>`_ parser (short for  *Markedly
Structured Text*) that allows for richer content. For example, it is possible to
embed Jupyter notebook input and/or output cells *via* `MyST-nb
<https://myst-nb.readthedocs.io/en/latest/index.html>`_ or even write *entire*
notebooks in plain-text!

Here are some quick guides to ``.rst`` and ``.md`` formats:

- For ``MyST`` https://myst-parser.readthedocs.io/en/latest/using/syntax.html
- For reStructuredText https://docutils.sourceforge.io/docs/user/rst/quickref.html
- For ``MyST-nb`` https://myst-nb.readthedocs.io/en/latest/use/markdown.html

Useful notes on Jupyter notebooks
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Sometimes it's necessary to hide and/or remove content from a notebook when
using it as training material. This can be accomplished with *tags*.

In JupyterLab
-------------

In this example, we want to remove the cell entirely when rendering it as a webpage.

.. figure:: img/tags-lab.png
   :scale: 50%
   :alt: Setting tags in with the JupyterLab interface.
   :align: center


In Jupyter notebooks
--------------------

In this example, we want to remove the cell entirely when rendering it as a webpage.

.. figure:: img/tags-notebook.png
   :scale: 50%
   :alt: Setting tags in with the notebook interface.
   :align: center


In ``MyST-nb``
--------------

In this example, we only want to show the *output* of executing the cell.

.. code-block:: md

   ```{code-block} ipython3
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


Learning outcomes
^^^^^^^^^^^^^^^^^

- Familiarize with VeloxChem

First iteration
^^^^^^^^^^^^^^^

**Day 1 - Thursday 6 May 2021**

- Day 1 is based entirely on notebooks.
- The first segment (up until the first break) can be conducted as a type-along
  with all attendees in the same room.
- Subsequent segments are in breakout rooms.

.. csv-table::
   :widths: auto
   :align: center
   :delim: ;

    9:00 -  9:10 ; Welcome and introduction to the training course
    9:10 -  9:25 ; :doc:`notebooks/first-steps`
    9:25 -  9:40 ; :doc:`notebooks/next-steps`
    9:40 -  9:50 ; Break
    9:50 - 10:50 ; :doc:`notebooks/rh-scf`
   10:50 - 11:00 ; Break
   11:00 - 12:00 ; :doc:`notebooks/mp2`
   12:00 - 12:10 ; Break
   12:10 - 12:30 ; Wrap-up

**Day 2 - Friday 7 February 2021**

- Day 2 shows how to run on HPC infrastructure and we assume that has been taken
  care of properly beforehand.
- The first segment goes through the set up of VeloxChem. This is already
  part of the installation instructions, so it might be skipped.


.. csv-table::
   :widths: auto
   :align: center
   :delim: ;


    9:00 -  9:10 ; What did we cover yesterday?
    9:10 -  9:40 ; :ref:`hpc-setup`
    9:40 -  9:50 ; Break
    9:50 - 10:30 ; :ref:`x-ray-cpp`
   10:30 - 10:40 ; Break
   10:40 - 11:20 ; :ref:`exciton`
   11:20 - 11:30 ; Break
   11:30 - 12:10 ; :ref:`xTB-geomeTRIC`
   12:10 - 12:15 ; Break
   12:15 - 12:30 ; Wrap-up
