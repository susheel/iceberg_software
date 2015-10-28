CASTEP
======

.. sidebar:: CASTEP

   :Latest Version:  8.0
   :URL: http://www.castep.org/
   :location: /usr/local/packages6/apps/intel/15/castep/8.0

Licensing
---------
Only licensed users of CASTEP are entitled to use it and license details are available on `CASTEP's website <http://www.castep.org/CASTEP/GettingCASTEP>`_. Access to CASTEP on the system is controlled using a Unix group. That is, only members of the ``castep`` group can access and run the program. To be added to this group, you will need to contact the Iceberg email the team at research-it@sheffield.ac.uk and provide evidence of your eligibility to use CASTEP.

Interactive Usage
-----------------
The serial version of CASTEP should be used for interactive usage. After connecting to iceberg (see :ref:`ssh`),  start an interactive session with the ``qrsh`` or ``qsh`` command. Make the serial version of CASTEP available using the command ::

    module load apps/intel/15/castep/8.0-serial

The CASTEP executable is called ``castep-serial`` so if you execute ::

    castep.serial

You should get the following ::

  Usage:
  castep <seedname>                : Run files <seedname>.cell [and <seedname>.param]
    "    [-d|--dryrun] <seedanme>  : Perform a dryrun calculation on files <seedname>.cell
    "    [-s|--search] <text>      : print list of keywords with <text> match in description
    "    [-v|--version]            : print version information
    "    [-h|--help] <keyword>     : describe specific keyword in <>.cell or <>.param
    "         "      all           : print list of all keywords
    "         "      basic         : print list of basic-level keywords
    "         "      inter         : print list of intermediate-level keywords
    "         "      expert        : print list of expert-level keywords
    "         "      dummy         : print list of dummy keywords

If, instead, you get ::

    -bash: castep.serial: command not found

It is probably because you are not a member of the ``castep`` group. See the section on Licensing above for details on how to be added to this group.

Interactive usage is fine for small CASTEP jobs such as the Silicon example given at http://www.castep.org/Tutorials/BasicsAndBonding

To run this example, you can do ::

  # Get the files, decompress them and enter the directory containing them
  wget http://www.castep.org/files/Si2.tgz
  tar -xvzf ./Si2.tgz
  cd Si2

  #Run the CASTEP job in serial
  castep.serial Si2

  #Read the output using the more command
  more Si2.castep

CASTEP has a built in help system. To get more information on using castep use ::

  castep.serial -help

Alternatively you can search for help on a particular topic ::

  castep.serial -help search keyword

or list all of the input parameters ::

  castep.serial -help search all

Batch Submission - Parallel
---------------------------
The parallel version of CASTEP is called ``castep.mpi``. To make the parallel environment available, use the module command ::

    module load apps/intel/15/castep/8.0-parallel

As an example of a parallel submission, we will calculate the bandstructure of graphite following the tutorial at http://www.castep.org/Tutorials/BandStructureAndDOS

After connecting to iceberg (see :ref:`ssh`),  start an interactive session with the ``qrsh`` or ``qsh`` command. Download and decompress the example input files with the commands ::

  wget http://www.castep.org/files/bandstructure.tgz
  tar -xvzf ./bandstructure.tgz

Enter the directory containing the input files for graphite ::

  cd bandstructure/graphite/

Create a file called ``submit.sge`` that contains the following ::

  #!/bin/bash
  #$ -pe openmpi-ib 4    # Run the calculation on 4 CPU cores
  #$ -l rmem=4G          # Request 4 Gigabytes of real memory per core
  #$ -l mem=4G           # Request 4 Gigabytes of virtual memory per core
  module load apps/intel/15/castep/8.0-parallel

  mpirun castep.mpi graphite

Submit it to the system with the command ::

    qsub submit.sge

After the calculation has completed, get an overview of the calculation by looking at the file ``graphite.castep`` ::

    more graphite.castep

Installation Notes
------------------
These are primarily for system administrators.

**CASTEP Version 8**

Serial (1 CPU core) and Parallel versions of CASTEP were compiled. Both versions were compiled with version 15.0.3 of the Intel Compiler Suite and the Intel MKL versions of BLAS and FFT were used. The parallel version made use of OpenMPI 1.8.8

The Serial version was compiled and installed with ::

  module load compilers/intel/15.0.3
  install_dir=/usr/local/packages6/apps/intel/15/castep/8.0

  tar -xzf ./CASTEP-8.0.tar.gz
  cd CASTEP-8.0

  #Compile Serial version
  make INSTALL_DIR=$install_dir  FFT=mkl MATHLIBS=mkl10
  make INSTALL_DIR=$install_dir  FFT=mkl MATHLIBS=mkl10 install install-tools

The directory ``CASTEP-8.0`` was then deleted and the parallel version was installed with ::

  #!/bin/bash
  module load libs/intel/15/openmpi/1.8.8
  #The above command also loads Intel Compilers 15.0.3
  #It also places the MKL in LD_LIBRARY_PATH

  install_dir=/usr/local/packages6/apps/intel/15/castep/8.0
  mkdir -p $install_dir

  tar -xzf ./CASTEP-8.0.tar.gz
  cd CASTEP-8.0

  #Compile parallel version
  make COMMS_ARCH=mpi  FFT=mkl MATHLIBS=mkl10
  mv ./obj/linux_x86_64_ifort15/castep.mpi $install_dir

Testing
-------
The following script was submitted via ``qsub`` ::

    #!/bin/bash
   #$ -pe openmpi-ib 4
   module load libs/intel/15/openmpi/1.8.8

   cd CASTEP-8.0
   make check COMMS_ARCH=mpi  MAX_PROCS=4 PARALLEL="--total-processors=4 --processors=4"

All tests passed.
