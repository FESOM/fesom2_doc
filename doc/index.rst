.. fesom2 documentation master file, created by
   sphinx-quickstart on Sat Sep 28 22:37:42 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to fesom2's documentation!
==================================

Proposed structure:

::

    Introduction
    Getting started
        TL;DR version for supported HPC systems
        Detailed steps of compiling and runing the code
        Ubuntu based Docker container (to get first impression of the model)
        Troubleshooting
    Looking at the results
        Notebooks that comes with the model
        pyfesom2 (short description with link to documentation)
    Fine tuning
        General configuration (namelist.configure)
            Time stepping
            Restarts
            ALE options
            Mesh geometry and partitioning
        Ocean configuration (namelist.oce)
            Ocean dynamics
            Ocean tracers
            Adding a new tracer
            Initial conditions
        Sea ice configuration (namelist.ice)
            Ice dynamics
            Ice thermodynamics
        Atmospheric forcing (namelist.forcing)
        Output (namelist.io)
            Adding new output variable
        Meshes
            Mesh format
            Mesh generation?
        Partitioning
            MESSY
            Hierarchical partitioning
    FAQ
    Data pre/post processing
        Initial conditions
        Convert grid to netCDF that CDO understands
    Discretizations and Algorithms
    Example experiments
    History

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   getting_started/getting_started



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
