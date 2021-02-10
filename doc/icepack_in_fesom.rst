.. _icepack_in_fesom:

Icepack sea ice configuration
*****************************

This section describes the implementation of the Icepack sie ice column physics package in the FESOM2 model. The scope of the following paragraphs is to provide a practical guide to users interested in detailed simulations of the sea ice system with FESOM2, and not to describe the scientific features of Icepack. A detailed description of the sea ice parameterizations here implemented can be found on the website of the `CICE Consortium <https://github.com/CICE-Consortium/Icepack/wiki/Icepack-Release-Table>`_, which maintains and continuously develops this package. 

General information
===================

Icepack is licensed for use through the CICE Consortium. Therefore, we encourage the FESOM2 userbase interested in the Icepack features to be aware of the `License <https://github.com/CICE-Consortium/Icepack/blob/master/LICENSE.pdf>`_ when working with this model configuration. We report here a disclaimer from the `Icepack website <https://github.com/CICE-Consortium/Icepack/wiki>`_:

.. important::  
   Icepack releases are “functional releases” in the sense that the code runs, does not crash, passes various tests, and requires further work to establish its scientific validity. In general, users are not encouraged to use any of the CICE Consortium’s model configurations to obtain “scientific” results. The test configurations are useful for model development, but sea ice models must be evaluated from a physical standpoint in a coupled system because simplified configurations do not necessarily represent what is actually happening in the fully coupled system that includes interactive ocean and atmosphere components.

The current Icepack version implemented in FESOM2 is Icepack 1.2.1. To acknowledge the development work behind the implementation of Icepack in FESOM2 please cite `Zampieri et al. (2021) <https://search.proquest.com/docview/2469422827?fromopenview=true&pq-origsite=gscholar>`_:, part of which used to compile this documentation, and `Hunke et al. (2020) <https://zenodo.org/record/3712299#.Xvn3DPJS9TZ>`_:, in addition to the usual FESOM2 papers.

Implementation of Icepack in FESOM2
===================================

Icepack–the column physics package of the sea-ice model CICE–is a collection of physical parameterizations that account for thermodynamic and mechanic sub-grid processes not explicitly resolved by the hosting sea-ice model, in our case FESOM2. The modular implementation of Icepack allows the users to vary substantially the complexity of the sea-ice model, with the possibility of choosing between several schemes and a broad set of active and passive tracers that describe the sea-ice state. Icepack v1.2.1 has been implemented in FESOM2 and can be used as an alternative to the standard FESIM thermodynamic module. As the standard FESIM implementation, the Icepack column-physics subroutines run every ocean time step. All the Icepack variables are defined directly on the nodes of the FESOM2 mesh, ensuring an optimal consistency between the ocean and the sea-ice components of the model. The inclusion of Icepack in FESOM2 required a revision of the calling sequence within the sea-ice model, which now follows that of the CICE model as illustrated in Fig. :numref:`call_seq`.

.. _call_seq:
.. figure:: img/call_seq.png

   Calling sequences of the Standard FESOM2 and FESOM2-Icepack implementations.]{Schematic describing the calling sequences of the Standard FESOM2 and FESOM2-Icepack implementations.

Technical aspects of this implementation
========================================
