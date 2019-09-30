.. _chap_meshes:

Meshes
******

Mesh files
==========

All files that describe the mesh are currently ASCII files. To define FESOM mesh only three files is enough: ``nod2d.out``, ``elem2d.out`` and ``aux3d.out``. However diring mesh partitioning several additional files are created that are nessesary for FESOM to work.

nod2d.out
---------

This file contains node numbers, positions of the nodes in geographical coordinates and a flag that indicate if the node is a wet point (0), located on the closed boundary (1) or is an open boundary (2). The columns are as follows:

::

    | Node number | x (lon) | y (lat) | Flag |

elem2d.out
----------

This file contains triples of numbers that form one elment of the grid (triangle, or face in therminology of UGRID). The node numbers are takein from the nod2d.out file. The columns are as follows:

::

    | First element | Second element | Third element |

aux3d.out
---------

This file contains information on the number of layers (first line), vertical layers (size of number of layers), and depths at nodes (size of number of nodes). The depths will be converted to model levels inside FESOM2.

