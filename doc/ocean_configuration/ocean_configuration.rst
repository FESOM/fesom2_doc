.. _chap_ocean_configuration:

Ocean configuration (namelist.oce)
**********************************

Sections of the namelist
========================

Ocean dynamics &oce_dyn
"""""""""""""""""""""""

Ocean tracers &oce_tra
""""""""""""""""""""""

Initial conditions &oce_init3d
""""""""""""""""""""""""""""""

Initial conditions are loaded from netCDF file (or files) that contain values on regular grid. On the format o netCDF files see :ref:`chap_data_processing`.

One have to specify several parameters in the ``namelist.oce``. The section of the ``namelist.oce`` might look like this:

::

    &oce_init3d                               ! initial conditions for tracers
    n_ic3d   = 2                              ! number of tracers to initialize
    idlist   = 1, 0                           ! their IDs (0 is temperature, 1 is salinity, etc.). The reading order is defined here!
    filelist = 'phc3.0_winter.nc', 'phc3.0_winter.nc' ! list of files in ClimateDataPath to read (one file per tracer), same order as idlist
    varlist  = 'salt', 'temp'                 ! variables to read from specified files
    t_insitu = .true.                         ! if T is insitu it will be converted to potential after reading it



- ``n_ic3d`` - how many tracers you want to initialise. As a minimum you have to initialise temperature and salinity. They have reserved id's 0 and 1 respectivelly (see ``idlist`` below). In this example only two tracers (temperature and salinity) are initialised.
- ``idlist`` - IDs of tracers. In this variable you define the reading order. Here we on purpose change the order of temperature and salinity to demonstrate that the order can be arbitrary. Once again remember that ID 0 and 1 are reserved for temperature and salinity respectively.
- ``filelist`` - coma separated list of files (each in qoutation marks) that contain initial conditions (see the section below about requirements to the file format). The path to the folder with this files is defined in ``namelist.config`` (``ClimateDataPath`` variable). In this case the file ``phc3.0_winter.nc`` is the same for temperature and salinity since it contains both variables.
- ``varlist`` - names of the variables in the netCDF files specified above. Note again the order of the variables in the example, it can be arbitrary and in this case temperature comes after salinity.
- ``t_insitu`` - most of climatologies are distributed with in situ temperature, while model needs potential temperature. This flag allows to do the conversion (UNESCO equation) on the fly.

