.. _chap_general_configuration:

General configuration (namelist.config)
***************************************

General configuation is defined in the ``namelist.configure``. Here you define time steping and restart frequency, details of the ALE and mesh geometry.

Section &modelname
==================

- **runid='fesom'** define name of the run. It will be used as part of the file name in model output and restarts. Don't change it if you don't have a good reason, since many post processing scripts assume it to be ``fesom``.

Section &timestep
=================

- **step_per_day=32** define how many steps per day the model will have. The variable ``step_per_day`` must be an integer multiple of 86400 ``(mod(86400,step_per_day)==0)``. Valid values are, for example: 32(45min), 36(40min), 48(30min), 60(24min), 72(20min), 144(10min), 288(5min), 1440(1min).
- **run_length= 62** length of the run in ``run_length_unit``.
- **run_length_unit='y'** units of the ``run_length``. Valid values are year (``y``), month (``m``), day (``d``), and model time step (``s``).


