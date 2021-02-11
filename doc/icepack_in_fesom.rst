.. _icepack_in_fesom:

Icepack sea ice configuration
*****************************

This section describes the implementation of the Icepack sie ice column physics package in the FESOM2 model. The scope of the following paragraphs is to provide a practical guide to users interested in detailed simulations of the sea ice system with FESOM2, and not to describe the scientific features of Icepack. A detailed description of the sea ice parameterizations here implemented can be found on the website of the `CICE Consortium <https://github.com/CICE-Consortium/Icepack/wiki/Icepack-Release-Table>`_, which maintains and continuously develops this package. 

General information
===================

Icepack–the column physics package of the sea-ice model CICE–is a collection of physical parameterizations that account for thermodynamic and mechanic sub-grid processes not explicitly resolved by the hosting sea-ice model, in our case FESOM2. The modular implementation of Icepack allows the users to vary substantially the complexity of the sea-ice model, with the possibility of choosing between several schemes and a broad set of active and passive tracers that describe the sea-ice state. Icepack v1.2.1 has been implemented in FESOM2 and can be used as an alternative to the standard FESIM thermodynamic module. As the standard FESIM implementation, the Icepack column-physics subroutines run every ocean time step. All the Icepack variables are defined directly on the nodes of the FESOM2 mesh, ensuring an optimal consistency between the ocean and the sea-ice components of the model. The inclusion of Icepack in FESOM2 required a revision of the calling sequence within the sea-ice model, which now follows that of the CICE model as illustrated in :numref:`call_seq`.

.. _call_seq:
.. figure:: img/call_seq.png

   Schematic describing the calling sequences of the Standard FESOM2 and FESOM2-Icepack implementations.

Icepack is licensed for use through the CICE Consortium. Therefore, we encourage the FESOM2 userbase interested in the Icepack features to be aware of the `License <https://github.com/CICE-Consortium/Icepack/blob/master/LICENSE.pdf>`_ when working with this model configuration. We report here a disclaimer from the `Icepack website <https://github.com/CICE-Consortium/Icepack/wiki>`_:

.. important::  
   Icepack releases are “functional releases” in the sense that the code runs, does not crash, passes various tests, and requires further work to establish its scientific validity. In general, users are not encouraged to use any of the CICE Consortium’s model configurations to obtain “scientific” results. The test configurations are useful for model development, but sea ice models must be evaluated from a physical standpoint in a coupled system because simplified configurations do not necessarily represent what is actually happening in the fully coupled system that includes interactive ocean and atmosphere components.

The current Icepack version implemented in FESOM2 is Icepack 1.2.1. To acknowledge the development work behind the implementation of Icepack in FESOM2 please cite `Zampieri et al. (2021) <https://search.proquest.com/docview/2469422827?fromopenview=true&pq-origsite=gscholar>`_:, part of which used to compile this documentation, and `Hunke et al. (2020) <https://zenodo.org/record/3712299#.Xvn3DPJS9TZ>`_:, in addition to the usual FESOM2 papers.

Implementation
==============

The implementation of Icepack in FESOM2 is fully modular, meaning that the users are free to vary the configuration via namelist parameters. When Icepack is used, ``namelist.icepack`` controls all settings related to the sea ice subgrid parameterizations, thus overriding the content of ``namelist.ice``. The dynamics (EVP) and advection schemes are still controlled by the standard ``namelist.ice``. Below we describe some of the most important namelist parameters, while we recommend consulting the `official Icepack documentation <https://buildmedia.readthedocs.org/media/pdf/cice-consortium-icepack/icepack1.2.1/cice-consortium-icepack.pdf>`_: for a more comprehensive description.

Section &env_nml
""""""""""""""""

- **nicecat** Defines the number of sea ice thickness categories.
- **nfsdcat** Defines the number of categories of the floe size distribution. This parameter should be set to 1 as the floe size distribution has never been tested in FESOM2.
- **nicelyr** and **nsnwlyr** Defines the number of vertical layers in sea ice and snow.

Section &grid_nml
""""""""""""""""

- **kcatbound** Specifies which criteria is followed to discretize the Ice Thickness Distribution (ITD). Setting **kcatboundequal** to 0, 1, or 3 gives lower thickness boundaries for any number of thickness categories. Setting **kcatboundequal=2** corresponds to World Meteorological Organization ITD classification, and it is compatible only with **nicecat=5,6,7**.

Section &tracer_nml
"""""""""""""""""""

Logical parameters to specify parameterizations and passive tracers. Only **tr_pond_cesm** has been tested extensively.  

Section &nml_list_icepack
"""""""""""""""""""""""""

Regulates the type, frequency, and precision of the output for Icepack variables. Most of the Icepack variables can be defined as average over the grid cell (e.g. **aice**: average sea ice area fraction), or for separately for each thickness class (e.g. **aicen**: sea ice area fraction in each thickness class). In the latter case, the ITD is saved as a vertical dimension in the netCDF file. At the moment, variables defined over multiple vertical layers are output in separated files. For example, ``qice_1.fesom.yyyy.nc`` contains the sea ice enthalpy of the first vertical layer averaged over the grid cell, while ``qicen_1.fesom.yyyy.nc`` contains the sea ice enthalpy of the first vertical layer for each thickness class.

Code structure
==============

Communication between Icepack and FESOM2
========================================

Compilation
===========

Running the model
=================

.. attention::
   Restarting the model after changing the number of ice thickness classes, the vertical discretization of ice and/or snow, and the number of passive tracers is currently not possible. Also, changing the thermodynamic and melt pond schemes during the run is not recommended. In these cases consider a cold start and repeat your spinup run.       

Frequently asked questions
==========================

**Is FESOM2 slower when run with Icepack?**

Yes, the model integration is slower for two reasons: 1. The sea ice subgrid parameterizations are more complex compared to the standard FESIM. 2. Much more sea-ice tracers need to be advected. Overall, the sea ice component of FESOM2 becomes approximately four times slower with Icepack. Including additional output related to a more complex sea ice description can also contribute to deteriorating the model performances.    

**Can Icepack be configured as the standard FESIM?**

Yes, in principle it is possible to run Icepack with a single thickness class and with the 0-layer thermodynamics. However, the results obtained during the testing phase with this configuration were not very convincing and they seemed not compatible with the standard FESOM2 results. More investigations are needed to understand the cause of this behavior, which is likely related to a different implementation of the thermodynamic processes in the model.   

