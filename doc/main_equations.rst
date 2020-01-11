.. _main_equations:

Main equations
**************

.. _sec_cequations:

Main equations
==============

The code solves the standard set of equations derived under the standard set of approximations (Boussinesq, hydrostatic, and traditional approximations).
These equations include the momentum equation for horizontal velocity

.. math::
   \partial_t\mathbf{u}+f\mathbf{e}_z\times\mathbf{u}+(\mathbf{u}\cdot\nabla_h+w\partial_z)\mathbf{u}+\nabla_h p\rho_0=D_h\mathbf{u}+\partial_z\nu_v\partial_z\mathbf{u},
   :label: eq_cmom

the hydrostatic equation

.. math::
   \partial_zp=-g\rho,
   :label: eq_chydrost

the Boussinesq form of the continuity equation

.. math::
   \partial_zw=-\nabla\cdot\mathbf{u},
   :label: eq_ccont

and the equations for potential temperature (FESOM is still using potential temperature, which will be replaced by conservative temperature in the nearest future) and salinity

.. math::
   \partial_t T+\nabla\cdot (\mathbf{u}T)+\partial_z(wT)=\nabla\cdot\mathbf{K}\nabla T,
   :label: eq_cT}


.. math::
   \partial_t S+\nabla\cdot (\mathbf{u}S)+\partial_z(wS)=\nabla\cdot\mathbf{K}\nabla S.
   :label: eq_cS}

In these equations :math:`\mathbf{u}=(u,v)` is the horizontal velocity, :math:`f` the Coriolis parameter, :math:`\mathbf{e}_z` the unit vertical vector, :math:`\rho_0` the reference density, :math:`p` the pressure, :math:`D_h` the horizontal viscosity operators to be specified further, :math:`\nu_v` the vertical viscosity coefficient, :math:`g` the gravitational acceleration, :math:`T, S`, the potential temperature and salinity and :math:`\mathbf{K}` the diffusivity tensor directing mixing in deep ocean to be isoneutral. The operator :math:`\nabla` is two-dimensional, :math:`\nabla_h=(\partial_x,\partial_y)`, and :math:`\nabla=(\nabla, \partial_z)`. The equations above have to be complemented by the equation of state connecting density with the temperature, salinity and pressure. In the Boussinesq approximation the pressure featuring in the equation of state is the fluid depth up to a factor :math:`g\rho_0`, so we formally write

.. math::
   \rho=\rho(T,S,z).

They also need appropriate initial and boundary conditions. The walls and bottom of the ocean basin are traditionally considered as isolated and impermeable, implying no flux boundary conditions. Flux conditions are imposed on the surface. Bottom acts as a momentum sink through the drag force applied there.

Ocean free surface denoted :math:`\eta` vary in space and time. An equation governing it is obtained by integrating :eq:`eq_ccont` vertically from the bottom at :math:`z=-H(x,y)` to the top at :math:`z=\eta(x,y,t)`

.. math::
   \partial_t\eta+\nabla_h\int^{\eta}_{-H}\mathbf{u}dz=-W,
   :label: eq_ceta

where :math:`W` is water flux leaving the ocean through the surface (specified as a part of boundary conditions). We stress that the last equation is not an independent one, but the consequence of the equations written before.

Transition from continuous to discrete equation includes the steps of spatial and temporal discretization. The spatial discretization is very different for the vertical and horizontal direction and is treated separately. We begin with vertical discretization, followed by temporal discretization and then by the horizontal discretization.

Vertical discretization: Layer thicknesses and layer equations
==============================================================

FESOM2 uses Arbitrary Lagrangian Eulerian (ALE) vertical coordinate. This implies that level surfaces are allowed to move. ALE vertical coordinate on its own only the framework enabling moving level surfaces. The way how they are moving depend on one's particular goal and may require additional algorithmic steps. Two limiting cases obviously include the case of fixed :math:`z`-levels and the case when levels are isopycnal surfaces. At present only vertical coordinates that slightly deviate from :math:`z`-surfaces are supported in FESOM, but many other options will follow.

The implementation of ALE vertical coordinate in FESOM2 basically follows  :cite:`Ringler2013`. An alternative approach, used by MOM6, see, e.g., :cite:`Adcroft_Hallberg_2006` :cite:`Adcroft2019`, is in the exploratory phase.

The essential step toward the ALE vertical coordinate lies in confining equations of section :ref:`sec_cequations` to model layers.

- Introduce layer thicknesses :math:`h_k=h_k(x,y,t)`, where :math:`k=1:K` is the layer index and :math:`K` the total number of layers. They are functions of the horizontal coordinates and time in a general case. Each layer consists of prisms defined by the surface mesh but partly masked by bottom topography.

- Layers communicate via the transport velocities :math:`w_{kv}` through the top and bottom boundaries of the prisms. The transport velocities are the differences between the physical velocities in the direction normal to the layer interfaces and the velocities due to the motion of the interfaces. These velocities are defined at the interfaces (the yellow points in :numref:`vertical`. For layer :math:`k` the top interface has index :math:`k` and the bottom one is :math:`k+1`. Note that :math:`w_{kv}` coincides with the vertical velocity only if the level surfaces are flat.

- All other quantities - horizontal velocities :math:`{\bf u}`, temperature :math:`T`, salinity :math:`S` and pressure :math:`p` are defined at mid-layers. Their depths will be denoted as :math:`Z_k`, and the notation :math:`z_k` is kept for the depths of mesh levels (the layer interfaces). They are functions of horizontal coordinates and time in a general case.

The equations of motion, continuity and tracer balance are integrated vertically over the layers. We will use :math:`T` as a representative of an arbitrary tracer.


- The continuity equation becomes the equation on layer thicknesses

.. math::
   \partial_t h_k+\nabla\cdot({\bf u}h)_k+(w^{t}-w^b)_k+W\delta_{k1}=0,
   :label: eq_thickness


- and the tracer equation becomes

.. math::
   \partial_t(hT)_k+\nabla\cdot({\bf u}hT)_k+(w^{t}T^t-w^bT^b)_k+WT_W\delta_{k1}=\nabla\cdot h_k{\bf K}\nabla T_k.
   :label: eq_tracer


Here, :math:`W` is the water flux leaving the ocean at the surface, it contributes to the first layer only (hence the delta-function); :math:`T_W` is the property transported with the surface water flux and the indices :math:`t` and :math:`b` imply the top and the bottom of the layer.

The right hand side of :eq:`eq_tracer` contains the 3 by 3 diffusivity tensor :math:`{\bf K}`. We still use :math:`\nabla` in :eq:`eq_tracer` for the 3D divergence (the outer :math:`\nabla`) for brevity, but assume the discrete form :math:`\nabla_h(...)+((...)^t-(...)^b)/h_k`, where :math:`(...)` are the placeholders for the horizontal and vertical components of 3D vector it acts on. A correct discretization of the diffusivity term is cumbersome and will be explained below.
