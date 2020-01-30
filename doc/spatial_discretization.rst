.. _spatial_discretization


Spatial discretization of equations (mostly in horizontal directions)
*********************************************************************

Finite-volume basics
====================

To obtain the finite-volume discretization, the governing equations are integrated over the selected control volumes. The flux divergence terms are then, by virtue of Gauss theorem, transformed to the net fluxes leaving the control volumes. Basic discrete fields such as horizontal velocities and scalar tracers are considered to be  mean over the respective finite volumes. We already described layered equations, i.e., the basic part of vertical discretization, where fluxes through top and bottom layer surfaces were appearing. In this section we discuss only the horizontal part.  The discrete fields are understood as

.. math::
   A_ch_c{\bf u}_c=\int_c {\bf u}dV,

and similarly for the temperature and other scalars,

.. math::
   A_{kv}h_{kv}T_{kv}=\int_{kv}TdV.

Here :math:`A_c` and :math:`A_{kv}` are the horizontal areas of cells and scalar prisms. The scalar areas vary with depth, hence the index :math:`k` in :math:`A_{kv}` in the formula above (the index :math:`k` will be suppressed in some cases). For layer k, :math:`A_{kv}` is the area of the prism :math:`kv` including its top face. The area of bottom face is :math:`A_{(k+1)v}` and may differ from that of the top one if the bottom is encountered. To be consistent in spherical geometry, we us

.. math::
   A_{kv}=\sum_{c\in\overline C(v)}A_c/3,

where :math:`\overline C(v)` is the list of wet prisms containing :math:`v` in layer :math:`k`.

Since the horizontal velocity is at centroids, its cell-mean value :math:`{\bf u}_c` can be identified with the value of the field :math:`{\bf u}` at the centroid of cell :math:`c` with the second order of spatial accuracy. For scalar quantities a similar rule is valid only on uniform meshes, but even in this case it is violated in the vicinity of boundaries or topography. This has some implications for the accuracy of transport operators.

Horizontal operators
====================

Scalar gradient
---------------

Scalar gradient takes vertex values of a scalar field and returns the gradient at the cell center:

.. math::
   A_c(\nabla p)_c=\int_c\nabla pdS=\sum_{e\in E(c)}l_e{\bf n}_e\sum_{v\in V(e)}p_v/2,

where :math:`{\bf n}_e` is the outer normal to cell :math:`c`. Clearly :math:`l_e{\bf n}_e=-{\bf k}\times{\bf l}_e` if :math:`c` is the first (left) cell of :math:`c(e)`. This procedure introduces :math:`{\bf G}_{cv}=(G^x_{cv},G^y_{cv})` with the :math:`x`- and :math:`y` component matrices :math:`G^x_{cv}` and :math:`G^y_{cv}`. They have three non-zero entries for each cell (triangle) which are stored in the order of triangle vertices, first for :math:`x` and then for :math:`y` component in the array `gradient_sca(1:6,1:myDim\_elem2D)` in the code. In contrast to FESOM1.4, where similar arrays are stored for each tetrahedron (and for 4 vertices and 3 directions), here only surface cells are involved.


Vector gradient
---------------

Vector gradient takes the values of velocity components at neighbor triangles of a given triangle and returns their derivatives at this triangle. They are computed through the least squares fit based on the velocities on neighboring cells sharing edges with cell :math:`c`. Their set is :math:`N(c)`. The derivatives :math:`(\alpha_x, \alpha_y)` of the velocity component :math:`u` are found by minimizing

.. math::
   \mathcal{L}=\sum_{n(c)}(u_{c}-u_{n}+(\alpha_x, \alpha_y)\cdot{\bf r}_{cn})^2={\rm min}.

Here :math:`{\bf r}_{cn}=(x_{cn}, y_{cn})` is the vector connecting the center of :math:`c` to that of its neighbor :math:`n`. The solution of the minimization problem can be represented as two matrices :math:`g_{cn}^x` and :math:`g_{cn}^y`, acting on velocity differences :math:`u_n-u_c` and returning the derivatives. Computations for :math:`v`-component result in the same matrices. The explicit expressions for matrix entries are:

.. math::
   g_{cn}^x=(x_{cn}Y^2-y_{cn}XY)/d, {} \\ {} g_{cn}^y=(y_{cn}X^2-x_{cn}XY)/d. {}

Here :math:`d=X^2Y^2-(XY)^2`, :math:` X^2=\sum_{n\in N(c)} x_{cn}^2`,
:math:`Y^2=\sum_{n(c)} y_{cn}^2` and :math:`XY=\sum_{n\in N(c)} x_{cn}y_{cn}`. The matrix entries stored in the array `gradient_vec(1:6,1:myDim\_elem2D)` in the order the neighbor elements are listed, first for :math:`x` and then for :math:`y` component. They are computed only once.

On the cells touching the lateral walls or bottom topography we use ghost cells (mirror reflections with respect to boundary edge). Their velocities are computed either as :math:`{\bf u}_{n}=-{\bf u}_{c}` or :math:`{\bf u}_{n}={\bf u}_{c}-2({\bf u}_{c}\cdot{\bf n}_{nc}){\bf n}_{nc}` for the no-slip or free-slip cases respectively. Here :math:`n` is the index of the ghost cell, and :math:`{\bf n}_{nc}` is the vector of unit normal to the edge between cells :math:`c` and :math:`n`. Note that filing ghost cells takes additional time, but allows using matrices :math:`g_{cn}^x` and :math:`g_{cn}^y` related to the surface cells only. Otherwise separate matrices will be needed for each layer. Note also that ghost cells are insufficient to implement the free-slip condition. In addition, the tangent component of viscous stress should be eliminated directly.


We stress that matrices :math:`g_{cn}^x` and :math:`g_{cn}^y` return derivatives of velocity components, and not the components of the tensor of velocity derivatives. The latter includes additional metric terms that need to be taken into account separately.

Flux divergence
---------------

Flux divergence takes fluxes defined at boundaries of scalar control volume (and in the simplest case just on cells) and returns their divergence on scalar control volumes:

.. math::
   A_{kv}(\nabla\cdot {\bf F})_vh_v=\sum_{e\in E(v)}\sum_{c\in C(e)}{\bf F}_ch_c\cdot {\bf n}_{ec}d_{ec},

where :math:`{\bf n}_{ec}` is the outer normal to control volume :math:`v`. Clearly, if :math:`v` is the first vertex in the list :math:`v(e)`, :math:`{\bf n}_{ec}d_{ec}=-{\bf k}\times{\bf d}_{ec}` if :math:`c` is the first in the list :math:`c(e)` (signs are changed accordingly in other cases). While these rules may sound difficult to memorize, in practice computations are done in a cycle over edges, in which case signs are obvious.

In contrast to the scalar gradient operator, the operator of divergence depends on the layer (because of bottom topography), which is one of the reasons why it is not stored in advance. Besides, except for the simplest cases such as :math:`\mathbf{F}=\mathbf{u}`, the fluxes :math:`{\bf F}` involve estimates of the scalar quantity being transported. Computing these estimates requires a cycle over edges in any case, so there would be no economy even if the matrices of the divergence operator were introduced.


Velocity curl
-------------

Velocity curl takes velocities at cells and returns the relative vorticity at vertices using the circulation theorem:

.. math::
   A_{kv}\int_v(\nabla\times{\bf u})\cdot {\bf k}dS=\sum_{e\in E(v)}\sum_{c\in C(e)}{\bf u}_c\cdot{\bf t}_{ec}d_{ec},

where :math:`{\bf t}_{ec}` is the unit vector along :math:`{\bf d}_{ec}` oriented so as to make an anticlockwise turn around vertex :math:`v`. If :math:`v` is the first in the list :math:`v(e)` and :math:`c` is the first in the list :math:`c(e)`,  :math:`{\bf t}_{ec}d_{ec}={\bf d}_{ec}`. This operator also depends on the layer and is not stored.

Mimetic properties
------------------

It can be verified that the operators introduced above are mimetic, which means that they satisfy the properties of their continuous analogs. For example, the scalar gradient and divergence are negative adjoint of each other in the sense of scalar products that provide energy norm, and the curl operator applied to the scalar gradient operator gives identically zero. The latter property implies that the curl of discrete pressure gradient is identically zero, which is a prerequisite of a PV conserving discretization.

Momentum advection
==================

FESOM2.0 has three options for momentum advection. Two of them are different implementations of the flux form and the third one relies on the vector invariant form. In spherical geometry the flux form acquires an additional term :math:`M{\bf k}\times{\bf u}` on the lhs, where :math:`M=u\tan\lambda/r_E` is the metric frequency, with :math:`\lambda` the latitude and :math:`r_E` the Earth radius. All the options are based on the understanding that the cell-vertex discretization has an excessive number of velocity degrees of freedom on triangular meshes. The implementation of momentum advection must contain certain averaging in order to suppress the appearance of grid-scale noise.

Flux form with velocities averaged to vertices
----------------------------------------------

The momentum flux is computed based on vertex velocities. We compute vertex velocities by averaging from cell to vertex locations

.. math::
   A_{kv}{\bf u}_{kv}h_{kv}=\sum_{c\in\overline C(v)}{\bf u}_{kc}h_{kc}A_c/3,

and use them to compute the divergence of horizontal momentum flux:

.. math::
   A_c(\nabla\cdot(h{\bf u u}))_c=\sum_{e\in E(c)}l_e(\sum_{v\in V(e)}{\bf n}_e\cdot{\bf u}_vh_v)(\sum_{v\in V(e)}{\bf u}_v/4).

Here :math:`{\bf n}_e` is the external normal and :math:`l_e{\bf n}_e=-{\bf k}\times{\bf l}_e` if :math:`c` is the first one in the list :math:`c(e)`. Since the horizontal velocity appears as the product with the thickness, the expressions here can be rewritten in terms of transports :math:`{\bf U}={\bf u}h^*`.

Flux form relying on scalar control volumes
-------------------------------------------

Instead of using vector control volumes, we assemble the flux divergence on the scalar control volumes and then average the result from the vertices to the cells. Here the same idea of averaging as in the previous case is applied to the momentum advection term instead of velocities. For the horizontal part,

.. math::
   A_{v}(\nabla\cdot(h{\bf u u}))_v=\sum_{e(v)}\sum_{c\in C(e)}{\bf u}_ch_c\cdot {\bf n}_{ec}{\bf u}_cd_{ec},

with the same rule for the normals as in the computations of the divergence operator.
The contributions from the top and bottom faces of scalar control volume are obtained by summing the contributions from the cells:

.. math::
   A_{v}(w_v {\bf u}^t)=w_v\sum_{c\in \overline C(v)}{\bf u}^t_cA_c/3

for the top surface, and similarly for the bottom one. The estimate of :math:`{\bf u}^t` can be either centered or upwind as above.

This option of momentum advection is special in the sense that the continuity is treated here in the same way as for the scalar quantities.

The fluxes through the top and bottom faces are computed with :math:`w_c=\sum_{v\in V(c)}w_v/3` using either the second or fourth order centered, or high-order upwind algorithms.


