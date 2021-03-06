.. _usersguide_install:

==============================
Installation and Configuration
==============================

-------------
Prerequisites
-------------

In order to compile OpenMC, you will need to have a Fortran compiler installed
on your machine. Since a number of Fortran 2003/2008 features are used in the
code, it is recommended that you use the latest version of whatever compiler you
choose. For gfortran_, it is recommended that you use version 4.5.0 or above.

If you are using Debian or a Debian derivative such as Ubuntu, you can install
the gfortran compiler using the following command::

    sudo apt-get install gfortran

To compile with support for parallel runs on a distributed-memory architecture,
you will need to have a valid implementation of MPI installed on your
machine. The code has been tested and is known to work with the latest versions
of both OpenMPI_ and MPICH2_. Note that if using OpenMPI, make sure that
--with-mpi-f90-size is not set to medium or large since this may prevent MPI
calls from completing successfully in OpenMC. OpenMPI and/or MPICH2 can be
installed on Debian derivatives with::

    sudo apt-get install mpich2
    sudo apt-get install openmpi-bin

To compile with support for HDF5_ output (highly recommended), you will need to
have HDF5 installed on your computer. The installed version will need to have
been compiled with the same compiler you intend to compile OpenMC with.

To enable CMFD acceleration, you will need to have PETSc_ installed on your
computer. The installed version will need to have been compiled with the same
compiler you intend to compile OpenMC with.

.. _gfortran: http://gcc.gnu.org/wiki/GFortran
.. _OpenMPI: http://www.open-mpi.org
.. _MPICH2: http://www.mcs.anl.gov/mpi/mpich/
.. _HDF5: http://www.hdfgroup.org/HDF5/
.. _PETSc: http://www.mcs.anl.gov/petsc/

--------------------
Obtaining the Source
--------------------

All OpenMC source code is hosted on GitHub_. You can download the source code
directly from GitHub or, if you have the git_ version control software installed
on your computer, you can use git to obtain the source code. The latter method
has the benefit that it is easy to receive updates directly from the GitHub
repository. GitHub has a good set of `instructions
<http://help.github.com/set-up-git-redirect>`_ for how to set up git to work
with GitHub since this involves setting up ssh_ keys. With git installed and
setup, the following command will download the full source code from the GitHub
repository::

    git clone git@github.com:mit-crpg/openmc.git

.. _GitHub: https://github.com/mit-crpg/openmc
.. _git: http://git-scm.com
.. _ssh: http://en.wikipedia.org/wiki/Secure_Shell

-------------------
Build Configuration
-------------------

All configuration for OpenMC is done within the Makefile located in
``src/Makefile``. In the Makefile, you will see that there are a number of User
Options which can be changed. It is recommended that you do not change anything
else in the Makefile unless you are experienced with compiling and building
software using Makefiles. The following parameters can be set from the User
Options sections in the Makefile:

COMPILER
  This variable tells the Makefile which compiler to use. Valid options are
  gnu, intel, pgi, ibm, and cray. The default is gnu (gfortran).

DEBUG
  Enables debugging when compiling. The flags added are dependent on which
  compiler is used.

PROFILE
  Enables profiling using the GNU profiler, gprof.

OPTIMIZE
  Enables high-optimization using compiler-dependent flags. For gfortran and
  Intel Fortran, this compiles with -O3.

MPI
  Enables parallel runs using the Message Passing Interface. The MPI_DIR
  variable should be set to the base directory of the MPI implementation.

HDF5
  Enables HDF5 output in addition to normal screen and text file output. The
  HDF5_DIR variable should be set to the base directory of the HDF5
  installation.

PETSC
  Enables PETSc for use in CMFD acceleration. The PETSC_DIR variable should be
  set to the base directory of the PETSc installation.

It is also possible to change these options from the command line itself. For
example, if you want to compile with DEBUG turned on without actually change the
Makefile, you can enter the following from a terminal::

    make DEBUG=yes

-------------------------------
Compiling on Linux and Mac OS X
-------------------------------

To compile OpenMC on Linux or Max OS X, run the following commands from within
the root directory of the source code:

.. code-block:: sh

    cd src
    make

This will build an executable named ``openmc``.

--------------------
Compiling on Windows
--------------------

To compile OpenMC on a Windows operating system, you will need to first install
Cygwin_, a Linux-like environment for Windows. When configuring Cygwin, make
sure you install both the gfortran compiler as well as git. Once you have
obtained the source code, run the following commands from within the source code
root directory:

.. code-block:: sh

    cd src
    make

This will build an executable named ``openmc``.

.. _Cygwin: http://www.cygwin.com/

---------------------------
Cross-Section Configuration
---------------------------

In order to run a simulation with OpenMC, you will need cross-section data for
each nuclide in your problem. Since OpenMC uses ACE format cross-sections, you
can use nuclear data distributed with MCNP_ or Serpent_.

To use cross sections distributed with MCNP, change the <directory> element in
the ``cross_sections.xml`` file in the root directory of the OpenMC distribution
to the location of the MCNP cross-sections. Then, either set the
:ref:`cross_sections` in a settings.xml file or the :envvar:`CROSS_SECTIONS`
environment variable to the absolute path of the ``cross_sections.xml`` file.

Similarly, to use cross-sections distributed with Serpent, change the
<directory> element in the ``cross_sections_serpent.xml`` file in the root
directory of the OpenMC distribution to the location of the Serpent
cross-sections. Then, either set the :ref:`cross_sections` in a settings.xml
file or the :envvar:`CROSS_SECTIONS` environment variable to the absolute path
of the ``cross_sections_serpent.xml`` file.

.. _MCNP: http://mcnp.lanl.gov
.. _Serpent: http://montecarlo.vtt.fi

--------------
Running OpenMC
--------------

Once you have a model built (see :ref:`usersguide_input`), you can either run
the openmc executable directly from the directory containing your XML input
files, or you can specify as a command-line argument the directory containing
the XML input files. For example, if the path of your OpenMC executable is
``/home/username/openmc/src/openmc`` and your XML input files are in the
directory ``/home/username/somemodel/``, one way to run the simulation would be:

.. code-block:: sh

    cd /home/username/somemodel
    openmc

Alternatively, you could run from any directory:

.. code-block:: sh

    openmc /home/username/somemodel

Note that in the latter case, any output files will be placed in the present
working directory which may be different from ``/home/username/somemodel``.

-----------------------------------------------------
Configuring Input Validation with GNU Emacs nXML mode
-----------------------------------------------------

The `GNU Emacs`_ text editor has a built-in mode that extends functionality for
editing XML files. One of the features in nXML mode is the ability to perform
real-time `validation`_ of XML files against a `RELAX NG`_ schema. The OpenMC
source contains RELAX NG schemas for each type of user input file. In order for
nXML mode to know about these schemas, you need to tell emacs where to find a
"locating files" description. Adding the following lines to your ``~/.emacs``
file will enable real-time validation of XML input files:

.. code-block:: common-lisp

    (require 'rng-loc)
    (add-to-list 'rng-schema-locating-files "~/openmc/schemas.xml")

Make sure to replace the last string on the second line with the path to the
schemas.xml file in your own OpenMC source directory.

.. _GNU Emacs: http://www.gnu.org/software/emacs/
.. _validation: http://en.wikipedia.org/wiki/XML_validation
.. _RELAX NG: http://relaxng.org/
