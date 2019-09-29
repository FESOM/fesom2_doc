.. _chap_getting_started:

Getting Started with FESOM2
***************************

This chapter describes several ways of getting started with FESOM2. First we show a minimum set of comands that will lead to a working setup on systems where FESOM2 is used activelly. We also have instructions for Docker/Singularity and Ubumtu. 

TL;DR version for supported HPC systems
=======================================

Supported systems are: `ollie` at AWI, `mistral` at DKRZ, `jureca` at JSC, HLRN, HAZELHEN. During configuration the system should be recognised and apropriate environmen variables and compiler options will be used.
::

    git clone https://gitlab.dkrz.de/FESOM/fesom2.git
    cd fesom2
    bash -l configure.sh

create file fesom.clock in the output directory with the following content:

::

    0 1  1948
    0 1  1948

then one has to adjust the run script for the target sustem and run it:
::

    cd ../bin
    sbatch job_ollie

Detailed steps of compiling and runing the code
===============================================

First thing is to checkout FESOM 2 code from the repository. The code is developed in private GitLab repository at DKRZ (`<https://gitlab.dkrz.de/FESOM/fesom2/>`_), but there is a public version `available on GitHub`_. It is simple to choose from the two. If you just want to try out FESOM2 or use the latest stable version, use GitHub and skip the rest of the paragraph. If you would like to participate in model development or need the bleeding edge code state, the GitLab on DKRZ is your choice. To join the development you have to have an active DKRZ account (more information on how to register here_). Before your account can be added to the members of the project you have to at least once log in to `<https://gitlab.dkrz.de/>`_ with your DKRZ account so that it appears in the GitLab data base. To be added as a member of the FESOM project on GitLab you have to send an email to Dmitry Sidorenko (dmitry.sidorenko@awi.de) with the request. Please make sure that you are able to login to `<https://gitlab.dkrz.de/>`_ before sending the request.

.. _available on GitHub: https://github.com/FESOM/fesom2/
.. _here: https://www.dkrz.de/up/my-dkrz/getting-started/account/DKRZ-user-account

Build model executable with Cmake
---------------------------------

Clone the GitHub repository with a git command:

::

    git clone https://github.com/FESOM/fesom2.git

or GitLab repository:

::

    git clone https://gitlab.dkrz.de/FESOM/fesom2.git

The repository contains model code and two additional libraries: `Metis` (domain partitioner) and `Parms` (solver), necessary to run FESOM2. To build FESOM2 executable one have to compile Parms library and the code of the model (`src` folder). In order to build executable that is used for model domain partitioning (distribution of the model mesh between CPUs) one have to compile `Metis` library and also some code located in the src directory. Building of the model executable and the pertitioner is usually done automatically with the use of CMake. If you going to build the code not on one of the supported platforms (ollie, DKRZ, HLRN, and HAZELHEN, general Ubuntu), you might need to do some (usually small) modifications described in Troubleshooting_ section.

Change to the `fesom2` folder and execute:

::

    bash -l configure.sh

In the best case scenario, your platform will be recognized and the Parms library and model executable will be built and copied to the bin directory. If something went wrong have a look at Troubleshooting_ section.


Download data and mesh files
----------------------------

The fesom2 repository do not contain any data or meshes, so you have to download them separatelly. The easiest way to start is to download example set from `DKRZ cloud`_ for example by executing:

::

    curl https://swift.dkrz.de/v1/dkrz_035d8f6ff058403bb42f8302e6badfbc/FESOM2.0_tutorial/FESOM2_minimum_input.tar > FESOM2_minimum_input.tar

and doing dearchivation:

::

    tar -xvf FESOM2_minimum_input.tar

You will have a folder named `FESOM2_minimum_input` that contain all the data you need to do initial run of the model. The `mesh` directory contains two meshes: `pi` and `CORE`. The `pi` mesh is very small global FESOM2 mesh, that can run relativelly fast even on a laptop. The `CORE` mesh is our 1 degree equivalent mesh, that is used in many tuning and testing studies. Mesh folders already include several pre-prepeared partitionings (`dist_` folders), so you don't have to worry about partitioning during your first steps with FESOM.

The `input` folder contains files with initial conditions (`phc3.0`) and atmospheric forcing (`CORE2`) for one year (1948).


.. _DKRZ cloud: https://swift.dkrz.de/v1/dkrz_035d8f6ff058403bb42f8302e6badfbc/FESOM2.0_tutorial/FESOM2_minimum_input.tar


Prepearing the run
------------------

You have to do several basic things in order to prepare the run. First, create a directory where results will be stored. Usually, it is created in the model root directory:

::

    mkdir results

you might make a link to some other directory located on the part of the system where you have a lot of storage. In the results directory, you have to create ``runid.clock`` file, where ``runid`` is usually ``fesom``, so the file is most often named ``fesom.clock``. Inside the file you have to put two identical lines:

::

    0 1 1948
    0 1 1948

This is initial date of the model run, or the time the cold start of your model. More detailed explination of the clock file will be given in the `The clock file`_ section.

The next step is to make some changes in the model configuration. All runtime options can be set in the namelists that are located in the config directory:

::

    cd ../config/

There are several configuration files, but we are only interested in the ``namelist.config`` for now. The options that you might want to change for your first FESOM2 run are:

- ``run_length`` length of the model run in run_length_unit (see below).
- ``run_length_unit`` units of the run_length. Can be ``y`` (year), ``m`` (month), ``d`` (days), ``s`` (model steps).
- ``MeshPath`` - path to the mesh you would like to use (e.g. ``/youdir/FESOM2_minimum_input/mesh/pi/``, slash at the end is important!)
- ``ClimateDataPath`` - path to the folder with the file with model temperature and salinity initial conditions (e.g. ``/youdir/FESOM2_minimum_input/input/phc3.0/``). The name of the file is defined in `namelist.oce`, but during first runs you probably don't want to change it.
- ``ForcingDataPath`` - path to the forcing data (e.g. ``/youdir/FESOM2_minimum_input/input/CORE2/``)

Running the model
-----------------

Change to the ``work`` directory. You should find several batch scripts that are used to submit model jobs to HPC machines. The scripts also link ``fesom.x`` executable to the ``work`` directory and copy namelists with configurations from config folder. **NOTE, model executable, configuration namelists and job script have to be located in the same directory (usually work)**.  If you are working on AWI's ``ollie`` supercomputer, you have to use ``job_ollie``, in other case use the job script for your specific HPC platform, or try to modify one of the existing ones.

On ``ollie` the submission of your job is done by executing the following command:

::

    sbatch job_ollie

The job is then submitted to the supercomputer. In order to check the status of your job on ollie you can execute:

::

    squeue -u yourusername

Results of the model run should appear in the ``results`` directory that you have specified in the ``namelist.config``. After the run is finished the ``fesom.clockfile`` (or if you change your runid, ``runid.clock``)  will be updated with information about the time of your run's end, that allows running the next time portion of the model experiment by just resubmitting the job with sbatch ``job_ollie``.

Other things you need to know earlier on
========================================

The clock file
--------------

The clock file is usually located in your output directory (e.g. ``results``) and controls the time. At the start of a new experiment that we want to be initialized from climatology (a so-called cold start), the ``fesom.clock`` file would usually look like this:

::

    0 1 1948
    0 1 1948

In this example, ``1948`` is the first available year of the atmospheric ``CORE2`` forcing. The two identical lines tell the model that this is the start of the experiment and that there is no restart file to be read.

Let's assume that we run the model with a timestep of 30 minutes (= 1800 seconds) for a full year (1948). After the run is successfully finished, the clock file will then automatically be updated and look like this:

::

    84600.0 365 1948
    0.0     1   1949

where the first row is the second last time step of the model, and the second row gives the time where the simulation is to be continued. The first row indicates that the model ran for 365 days (in 1948) and 84600 seconds, which is ``1 day - 1 FESOM timestep`` in seconds. In the next job, FESOM2 will look for restart files for the year 1948 and continue the simulation at the 1st of January in 1949.

Since 1948 is a leap year (366 days), this is an exceptional case and the fesom.clock file after two full years (1948--1949) would look like this:
84600.0 364 1949

::

    84600.0 364 1949
    0.0     1   1950

Note that dependent on the forcing data set (using a different calendar), a year could only have 360 days.


Build partitioner executable
----------------------------

In the beggining the meshes will come with

Running mesh partitioner
------------------------

You have to do this step only if your mesh does not have partitioning for the desired amount of CPUs yet. You can understand if the partitioning exists by the presence of the dist_XXXX folder in your mesh folder, where XXX is the number of CPUs. If the folder contains files with partitioning, you can just skip this step.

Partitioning is going to split your mesh into pieces that correspond to the number of CPUs you going to request. Now FESOM2 scales until 300 nodes per CPU, further increase in the amount of CPU will probably have relatively small effect.

In order to tell the partitioner how many CPUs you need the partitioning for, one has to edit ``&machine`` section in the ``namelist.config`` file. There are two options: ``n_levels`` and ``n_part``. FESOM mesh can be partitioned with use of several hierarchy levels and ``n_levels`` define the number of levels while ``n_part`` the number of partitions on each hierarchy level. The simplest case is to use one level and ``n_part`` just equal to the number of CPUs and we recoment to use it at the beggining:

::

    n_levels=1
    n_part= 288

This will prepear your mesh to run on 288 CPU.

In order to run the partitioner change to the ``work`` directory. You should find several batch scripts that are used to submit model jobs to HPC machines. The scripts also link ``fvom_ini.x`` executable to the ``work`` directory and copy namelists with configurations from ``config`` folder. **NOTE, for the model to run model executable, configuration namelists and job script have to be located in the same directory (usually ``work``)**.

If you are working on AWI's ``ollie`` supercomputer, you have to use ``job_ini_ollie``, in other case use the job script for your specific HPC platform, or try to modify one of the existing ones. For relativelly small meshes (up to 1M nodes) and small partitions it is usually fine just to run the partitioner on a login node (it is serial anyway), like this:

::

    ./fesom_ini.x

If you trying to partition large mesh, then on ``ollie`` for example the submission of your partitioning job is done by executing the following command:

::

    sbatch job_ini_ollie


Ubuntu based Docker container (to get first impression of the model)
====================================================================


Troubleshooting
===============

Adding new platform for compilation
-----------------------------------



