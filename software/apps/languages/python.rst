Python
======

.. sidebar:: Python

   :Support Level: gold
   :Dependancies: None
   :URL: https://python.org
   :Version: multiple


There are multiple ways of accessing Python on iceberg, this section gives an
overview, and details on the recommended method.

Avalible Python Installations
-----------------------------

Anaconda Python
###############

Unless you need to install custom modules beyond those included in Anaconda on 
Python 2 this is the recommended method. See :ref:`anaconda` for more details.

conda Python
############

This is the primary method of using Python on iceberg if you need to install 
custom packages, the rest of this document will be talking about this system.

System Python
#############

The system Python 2.6 is available by default as it is installed with the
cluster OS Scientific Python 5, it is not recommended for general use.


Using conda Python
------------------
After connecting to iceberg (see :ref:`ssh`),  start an interactive session
with the ``qsh`` or ``qrsh`` command.

Conda Python can be loaded with::

        module load apps/python-conda


The ``root`` conda environment (the default) provides Python 3 and no extra
modules, it is automatically updated, and not recommended for general use, just
as a base for your own environments. There is also a ``python2`` environment,
which is the same by with a Python 2 installation.


Using conda Environments
########################

Once the system conda module is loaded you have to load or create the desired
conda environments. For the documentation on conda environments see
`here <http://conda.pydata.org/docs/using/envs.html>`_.

You can load a conda environment with::

    source activate python2

and unload one with::

    source deactivate

which will return you to the ``root`` environment.

It is possible to list all the available environments with::

    conda env list

Provided system-wide are a set of anaconda environments, these will be
installed with the anaconda version number in the environment name, and never
modified. They will therefore provide a static base for derivative environments
or for using directly.


Creating an Environment
#######################

Every user can create their own environments, and packages shared with the
system-wide environments will not be reinstalled or copied to your file store,
they will be ``symlinked``, this reduces the space you need in your ``/home``
directory to install many different Python environments.

To create a clean environment with just Python 2 and numpy you can run::

    conda create -n mynumpy python=2.7 numpy

This will download the latest release of Python 2.7 and numpy, and create an
environment named ``mynumpy``.

If you wish to modify an existing environment, such as one of the anaconda
installations, you can ``clone`` that environment::

    conda create --clone anaconda3-2.3.0 -n myexperiment

This will create an environment called ``myexperiment`` which has all the
anaconda 2.3.0 packages installed with Python 3.

Installing Packages Inside an Environment
#########################################

Once you have created your own environment you can install additional packages
or different versions of packages into it. There are two methods for doing
this, ``conda`` and ``pip``, if a package is available through conda it is
strongly recommended that you use conda to install packages. You can search for
packages using conda::

    conda search pandas

then install the package using::

    conda install pandas

if you are not in your environment you will get a permission denied error
when trying to install packages, if this happens, create or activate an
environment you own.

If a package is not available through conda you can search for and install it
using pip::

    pip search colormath

    pip install colormath

Installation Notes
------------------
These are primarily for administrators of the system.

The conda package manager is installed in ``/usr/share/packages6/conda``, it
was installed using the `miniconda <http://conda.pydata.org/miniconda.html>`_
installer.

The two "root" environments ``root`` and ``python2`` can be updated using the
update script located in
``/usr/local/packages6/conda/_envronments/conda-autoupdate.sh``. This should be
run regularly to keep this base environments upto date with Python, and more
importantly with the conda package manager itself.

Installing a New Version of Anaconda
####################################

Perform the following::

    $ cd /usr/local/packages6/conda/_envronments/
    $ cp anaconda2-2.3.0.yml anaconda2-x.y.z.yml

then edit that file modifying the environment name and the anaconda version
under requirements then run::

    $ conda env create -f anaconda2-x.y.z.yml

then repeat for the Python 3 installation.

