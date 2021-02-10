.. _icepack_in_fesom:

Icepack sea ice configuration
*****************************

This section describes the implementation of the Icepack sie ice column physics package in the FESOM2 model. The scope of the following paragraphs is to provide a practical guide to users interested in detailed simulations of the sea ice system with FESOM2, and not to describe the scientific features of Icepack. A detailed description of the sea ice parameterizations here implemented can be found on the website of the `CICE Consortium <https://github.com/CICE-Consortium/Icepack/wiki/Icepack-Release-Table>`_, which maintains and continuously develops this package. 

General information
===================

Icepack is licensed for use through the CICE Consortium. Therefore, we encourage the FESOM2 userbase interested in the Icepack features to be aware of the `License <https://github.com/CICE-Consortium/Icepack/blob/master/LICENSE.pdf>`_ when working with this model configuration. We report here a disclaimer from the `Icepack website <https://github.com/CICE-Consortium/Icepack/wiki>`_:

.. admonition::  

   Icepack releases are “functional releases” in the sense that the code runs, does not crash, passes various tests, and requires further work to establish its scientific validity. In general, users are not encouraged to use any of the CICE Consortium’s model configurations to obtain “scientific” results. The test configurations are useful for model development, but sea ice models must be evaluated from a physical standpoint in a coupled system because simplified configurations do not necessarily represent what is actually happening in the fully coupled system that includes interactive ocean and atmosphere components.

To acknowlege the development work behind Icepack in FESOM2 cite 

The Icepack sea ice column physics package
==========================================

Icepack in FESOM2
=================
