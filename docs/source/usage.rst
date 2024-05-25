:github_url: https://rupaliprusty-rtd-tutorial.readthedocs-hosted.com/en/latest/

.. _installation:

Installation
============

Prerequisites:
###############

- python3 (an easy installation guide can be found at https://software.intel.com/content/www/us/en/develop/articles/how-to-install-the-python-version-of-intel-daal-in-linux.html)
- numpy
- scipy
- pandas
- geometric
- pyberny 
- ifort 
- mkl libraries
- cython
- pyscf (can be easily installed using pip)
- make



Installation steps
------------------

.. code-block:: shell 

   git clone https://github.com/achintya007/bagh.git

| set f90comp in the Makefile to the fortran compiler being used
| set MKLROOT according to the path of mkl libraries in the system being used
| set linking_opt also according to your system


.. code-block:: shell

   ./make

   


