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

you might make a link to some other directory located on the part of the system where you have a lot of storage. In the results directory, you have to create `runid.clock` file, where `runid` is usually `fesom`, so the file is most often named `fesom.clock`. Inside the file you have to put two identical lines:

::

    0 1 1948
    0 1 1948

This is initial date of the model run, or the time the `cold start` of your model. More detailed explination of the clock file



Ubuntu based Docker container (to get first impression of the model)
====================================================================


Troubleshooting
===============

Adding new platform for compilation
-----------------------------------



