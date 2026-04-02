.. _installation:

Installation
============

This guide covers installing BAGH on Linux and macOS systems. BAGH is primarily
a Python package, but it relies on compiled Fortran and Cython extensions for
performance-critical routines.

System Requirements
-------------------

- **Operating System:** Linux (recommended) or macOS
- **Python:** 3.8 or later
- **Fortran Compiler:** ``gfortran`` (macOS) or Intel Fortran ``ifort``/``ifx`` (Linux)
- **C Compiler:** Required by Cython (e.g., ``gcc``, ``clang``)
- **CMake:** 3.18 or later
- **Make:** GNU Make
- **Meson** and **Ninja:** Required by NumPy's f2py backend for Python >= 3.12

Hardware Recommendations
^^^^^^^^^^^^^^^^^^^^^^^^

- At least 8 GB RAM for small molecules; 32+ GB for larger systems
- Multi-core CPU recommended (NumPy/MKL will use multiple threads)
- Sufficient disk space for MO integrals stored in HDF5 format


Prerequisites
-------------

Python Dependencies
^^^^^^^^^^^^^^^^^^^

The following Python packages are required:

.. list-table::
   :header-rows: 1
   :widths: 25 55 20

   * - Package
     - Purpose
     - Install
   * - `NumPy <https://numpy.org/>`_
     - Array operations and linear algebra
     - ``pip install numpy``
   * - `SciPy <https://scipy.org/>`_
     - Sparse linear algebra and special functions
     - ``pip install scipy``
   * - `PySCF <https://pyscf.org/>`_
     - Integral generation, SCF, and molecular data
     - ``pip install pyscf``
   * - `Cython <https://cython.org/>`_
     - Compilation of ``.pyx`` extension modules
     - ``pip install cython``
   * - `h5py <https://www.h5py.org/>`_
     - HDF5 file I/O for integrals and amplitudes
     - ``pip install h5py``
   * - `psutil <https://github.com/giampaolo/psutil>`_
     - System resource monitoring (memory, CPU)
     - ``pip install psutil``
   * - `pandas <https://pandas.pydata.org/>`_
     - Data handling utilities
     - ``pip install pandas``
   * - `joblib <https://joblib.readthedocs.io/>`_
     - Parallel execution (CC3 triples)
     - ``pip install joblib``
   * - `sympy <https://www.sympy.org/>`_
     - Symbolic algebra (ADC response functions)
     - ``pip install sympy``
   * - `tqdm <https://tqdm.github.io/>`_
     - Progress bars
     - ``pip install tqdm``
   * - `geomeTRIC <https://github.com/leeping/geomeTRIC>`_
     - Geometry optimization
     - ``pip install geometric``

Optional Python packages:

.. list-table::
   :header-rows: 1
   :widths: 25 55 20

   * - Package
     - Purpose
     - Install
   * - `pyberny <https://github.com/jhrmnn/pyberny>`_
     - Alternative geometry optimizer
     - ``pip install pyberny``
   * - `mpi4py <https://mpi4py.readthedocs.io/>`_
     - MPI parallelism (for parallel calculations)
     - ``pip install mpi4py``

You can install all required Python dependencies at once:

.. code-block:: shell

   pip install -r requirements.txt

Or manually:

.. code-block:: shell

   pip install numpy scipy pyscf cython h5py psutil pandas joblib sympy tqdm geometric

For the f2py meson build backend (required for Python >= 3.12):

.. code-block:: shell

   pip install meson meson-python ninja

For optional packages:

.. code-block:: shell

   pip install geometric pyberny mpi4py


Math Libraries
^^^^^^^^^^^^^^

BAGH uses MKL (Intel Math Kernel Library) for optimized linear algebra in the
Fortran extensions. MKL is available through:

- **Intel oneAPI MKL** (recommended): Install from https://www.intel.com/content/www/us/en/developer/tools/oneapi/onemkl.html
- **Conda:** ``conda install mkl mkl-devel``
- **System package manager:** e.g., ``apt install libmkl-dev`` on Ubuntu

.. note::

   If MKL is not available, CMake will proceed without it. You can also
   modify ``MKL_LINK`` in ``bagh_code/fortran_lib/makefile`` to link against
   an alternative BLAS/LAPACK library (e.g., OpenBLAS).
   See the :ref:`Configuring the Build <configure-makefile>` section below.


Fortran Compiler
^^^^^^^^^^^^^^^^

The build system auto-detects the compiler based on your platform:

- **macOS:** defaults to ``gfortran`` (install via ``brew install gcc``)
- **Linux:** defaults to ``ifort`` (Intel Fortran)

You can override the compiler by setting the ``FC`` environment variable
(see :ref:`Step 3 <build-step>` below).

Verify your Fortran compiler is available:

.. code-block:: shell

   ifort --version    # Intel classic
   ifx --version      # Intel oneAPI
   gfortran --version # GNU Fortran


Installation Steps
------------------

Step 1: Clone the Repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: shell

   git clone --recursive https://github.com/achintya007/bagh.git
   cd bagh

The ``--recursive`` flag is important as it also clones the ``socutils``
submodule, which is required for spin-orbit coupling and relativistic
calculations.

If you already cloned without ``--recursive``, initialize the submodule
manually:

.. code-block:: shell

   git submodule update --init --recursive


.. _configure-makefile:

Step 2: Configure the Build (Optional)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The build system (CMake) auto-detects most settings. It will automatically find:

- The correct Python interpreter (prefers conda/venv Python with NumPy)
- f2py, Cython, and NumPy
- The Fortran compiler (``gfortran`` on macOS, ``ifort`` on Linux)
- MKL at common installation paths
- OpenMP flags appropriate for your compiler
- macOS SDK paths (for ``gfortran`` and Cython C compilation)

You may need to adjust the MKL path if your installation is non-standard.
Pass it on the command line:

.. code-block:: shell

   MKLROOT=/path/to/mkl/lib bash make

Or set it in ``bagh_code/fortran_lib/makefile``:

.. code-block:: makefile

   MKLROOT ?= /opt/intel/oneapi/mkl/latest/lib/intel64

.. note::

   If MKL is not available, CMake will proceed without it. You can also
   modify ``MKL_LINK`` in the makefile to link against an alternative
   BLAS/LAPACK library (e.g., OpenBLAS):

   .. code-block:: makefile

      MKL_LINK = -lopenblas

.. tip::

   If you are unsure about the correct MKL linking flags for your system,
   use the `Intel Link Line Advisor <https://www.intel.com/content/www/us/en/developer/tools/oneapi/onemkl-link-line-advisor.html>`_.


.. _build-step:

Step 3: Build
^^^^^^^^^^^^^

Run the build script from the repository root:

.. code-block:: shell

   bash make

This will:

1. Detect your platform and set up the compiler environment
   (``gfortran`` on macOS, ``ifort`` on Linux)
2. On macOS, automatically configure SDK paths for ``gfortran`` and Cython
3. Validate that stale ``CC``/``CXX``/``FC`` environment variables are cleared
   (e.g., after a Homebrew upgrade)
4. Create a ``build/`` directory and run CMake, which auto-detects Python,
   NumPy, f2py, Cython, MKL, and OpenMP
5. Compile all 18 Fortran extensions (via ``f2py`` with the meson backend)
6. Compile all Cython extensions across the codebase (interfaces, integral
   transformations, spin-orbital modules, ADC, relativistic CC, etc.)
7. Configure the ``bagh`` launcher script with the correct ``bagh_path``
   and the exact Python interpreter used during the build

To override the Fortran compiler:

.. code-block:: shell

   FC=gfortran bash make   # Use GNU Fortran
   FC=ifx bash make        # Use Intel oneAPI Fortran

To clean and rebuild from scratch:

.. code-block:: shell

   bash make clean
   bash make

The build process compiles extensions in the following order:

- Fortran library → Interfaces → Integral transformation → Spin-orbital CCSD
  → Spin-orbital EOM → Additional Cython modules (ADC, CC3, relativistic CCSD,
  relativistic EOM)

.. note::

   The full build may take several minutes depending on your system.

.. important::

   The build pins the exact Python interpreter into the ``bagh`` launcher
   script. If you switch Python environments (e.g., activate a different
   conda env), you should rebuild with ``bash make clean && bash make``
   to ensure the launcher uses the correct Python with all dependencies.


Step 4: Set Up the Environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The ``bagh`` wrapper script (in the repository root) sets up the necessary
environment variables. You can either:

**Option A: Add BAGH to your PATH** (recommended)

Add this to your ``~/.bashrc`` or ``~/.bash_profile``:

.. code-block:: shell

   export PATH=/path/to/bagh:$PATH

Then you can run BAGH from anywhere:

.. code-block:: shell

   bagh input.inp

**Option B: Use the full path**

.. code-block:: shell

   /path/to/bagh/bagh input.inp

The wrapper script automatically sets:

- ``bagh_path`` — path to the BAGH root directory
- ``socutils_path`` — path to the SOC utilities submodule
- ``PythonProjectorEmbedding_path`` — path to the embedding module
- ``PYTHONPATH`` — includes all the above so Python can find the modules


Verifying the Installation
--------------------------

Run a quick test calculation to verify everything works. Create a file
``test_h2.inp``:

.. code-block:: none

   ! CCSD cc-pvdz

   %cc
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   H 0.0 0.0 0.74
   *

Run it:

.. code-block:: shell

   bagh test_h2.inp

You should see output showing:

1. PySCF integral generation
2. RHF SCF convergence
3. Integral transformation
4. CCSD iterations converging to the correlation energy

If the calculation completes without errors, BAGH is installed correctly.

You can also run the test suite included in the ``test/`` directory:

.. code-block:: shell

   cd test
   python3 test.py


.. _install-x2camf:

Installing X2CAMF (Relativistic Spin-Orbit Integrals)
------------------------------------------------------

`X2CAMF <https://github.com/Warlocat/x2camf>`_ is required for relativistic
calculations using the X2C-AMF (exact two-component with atomic mean-field)
framework. It provides spin-orbit integrals interfaced with PySCF.

Linux Installation
^^^^^^^^^^^^^^^^^^

On Linux, X2CAMF can be installed directly via pip:

.. code-block:: shell

   pip install git+https://github.com/warlocat/x2camf

.. note::

   The default stack size may not be sufficient for larger systems.
   Use ``ulimit -s unlimited`` before running calculations.


macOS Installation
^^^^^^^^^^^^^^^^^^

On macOS, the standard pip install does not work due to compiler compatibility
issues with AppleClang. You must build X2CAMF from source with the following
modifications.

**1. Install system dependencies via Homebrew:**

.. code-block:: shell

   brew install cmake libomp eigen

**2. Set up a Python environment:**

.. code-block:: shell

   conda create -n x2camf_env python=3.10
   conda activate x2camf_env
   pip install pybind11 numpy

**3. Clone the repository:**

.. code-block:: shell

   git clone https://github.com/Warlocat/x2camf.git
   cd x2camf

**4. Apply macOS compatibility fixes:**

The source code uses GCC-specific ``std::chrono`` types that are not compatible
with AppleClang. Edit the following two files:

In ``include/general.h`` and ``src/general.cpp``, replace all occurrences of:

.. code-block:: cpp

   std::chrono::_V2::system_clock::time_point

with:

.. code-block:: cpp

   std::chrono::system_clock::time_point

Additionally, in ``src/general.cpp``, replace:

.. code-block:: cpp

   timeWall = chrono::high_resolution_clock::now();

with:

.. code-block:: cpp

   timeWall = std::chrono::system_clock::now();

**5. Add pybind11 (if not already included):**

.. code-block:: shell

   git clone https://github.com/pybind/pybind11.git

Verify the directory structure has ``pybind11/`` inside the ``x2camf/`` root.

**6. Build with CMake using Clang and Homebrew OpenMP:**

.. code-block:: shell

   mkdir build && cd build

   cmake .. \
     -DCMAKE_C_COMPILER=clang \
     -DCMAKE_CXX_COMPILER=clang++ \
     -DOpenMP_C_FLAGS="-Xpreprocessor -fopenmp -I$(brew --prefix libomp)/include" \
     -DOpenMP_CXX_FLAGS="-Xpreprocessor -fopenmp -I$(brew --prefix libomp)/include" \
     -DOpenMP_C_LIB_NAMES="omp" \
     -DOpenMP_CXX_LIB_NAMES="omp" \
     -DOpenMP_omp_LIBRARY=$(brew --prefix libomp)/lib/libomp.dylib \
     -DPYTHON_EXECUTABLE=$(which python) \
     -DCMAKE_CXX_FLAGS="-I$(brew --prefix eigen)/include/eigen3"

   make -j8

**7. Copy the shared library into the package and verify:**

The build produces ``libx2camf.cpython-*.so`` in the ``build/`` directory,
but it must be placed inside the ``x2camf/`` package directory for Python
to find it (otherwise you get a circular import error):

.. code-block:: shell

   # From the x2camf root directory (not the build directory)
   cd ..
   cp build/libx2camf.cpython-*.so x2camf/
   export PYTHONPATH=$PWD:$PYTHONPATH
   python -c "import x2camf; print('x2camf loaded successfully')"

Add the ``PYTHONPATH`` export to your ``~/.bashrc`` or ``~/.bash_profile``
to make it persistent.

.. note::

   This procedure works on both Apple Silicon (``/opt/homebrew``) and
   Intel Mac (``/usr/local``) systems. The ``brew --prefix`` commands
   automatically resolve to the correct Homebrew paths for your architecture.


Troubleshooting
---------------

**CMake error: "Could not find the compiler specified in CC"**
   This means your ``CC`` environment variable points to a compiler that
   no longer exists (e.g., after a Homebrew upgrade). The ``make`` script
   auto-detects and clears stale ``CC``/``CXX``/``FC`` variables, but you
   can also fix it manually: ``unset CC CXX && bash make``, or update the
   path in your ``~/.bashrc``.

**CMake error: "Could NOT find Python3 (missing: NumPy)"**
   CMake found a Python that does not have NumPy installed. The build
   system prefers ``python`` over ``python3`` to pick up conda/venv Python.
   Make sure your conda environment is activated, or specify explicitly:
   ``cmake -DPython3_EXECUTABLE=$(which python) ..``

**Fortran compilation fails with "compiler not found"**
   Ensure your Fortran compiler is in your ``PATH``. The build script
   auto-detects the compiler (``gfortran`` on macOS, ``ifort`` on Linux).
   Override with ``FC=gfortran bash make`` if needed.

**"Compiler gfortran/ifort cannot compile programs" (meson error)**
   On macOS, this usually means the SDK paths are not set. The ``make``
   script handles this automatically, but if you are running the makefiles
   directly, set: ``export SDKROOT=$(xcrun --show-sdk-path)`` and
   ``export LIBRARY_PATH=$SDKROOT/usr/lib``.

**"No such file or directory: 'meson'" (Python >= 3.12)**
   NumPy's f2py requires the meson build backend for Python 3.12+.
   Install it: ``pip install meson meson-python ninja``.

**"fatal error: 'assert.h' file not found" during Cython compilation (macOS)**
   The C compiler cannot find standard headers. The ``make`` script sets
   ``CFLAGS`` and ``CPPFLAGS`` with ``-isysroot`` automatically. If you
   are building manually, set:
   ``export CFLAGS="-isysroot $(xcrun --show-sdk-path)"``

**MKL linking errors**
   Verify ``MKLROOT`` points to the correct directory. The path should
   contain ``libmkl_core.so`` (Linux) or ``libmkl_core.dylib`` (macOS).
   Try: ``ls $MKLROOT/libmkl_core*``. You can pass ``MKLROOT`` on the
   command line: ``MKLROOT=/path/to/mkl/lib bash make``.

**Cython compilation errors**
   Ensure Cython and NumPy are installed in the same Python environment:
   ``pip install cython numpy``. Also verify that the Python found by
   CMake (shown in the build output) is the correct one.

**"ModuleNotFoundError: No module named 'h5py'" (or other missing modules)**
   The ``bagh`` launcher is using a different Python than the one used
   during the build. Rebuild to update the launcher:
   ``bash make clean && bash make``. The build pins the exact Python
   executable path into the ``bagh`` script.

**PySCF import errors at runtime**
   Install PySCF: ``pip install pyscf``. For relativistic calculations,
   ensure you have a recent version (2.0+).

**"ModuleNotFoundError: No module named 'bagh_code'"**
   This means the ``PYTHONPATH`` is not set correctly. Make sure you are
   running BAGH through the ``bagh`` wrapper script, not directly calling
   ``python3 main.py``.

**socutils submodule is empty**
   Run ``git submodule update --init --recursive`` from the repository root.
   The ``socutils`` module is required for spin-orbit and relativistic
   calculations using the X2C-AMF framework.

**HDF5 / h5py errors**
   Install h5py: ``pip install h5py``. Some Fortran modules also use HDF5
   directly — ensure the HDF5 C/Fortran libraries are available on your
   system (``apt install libhdf5-dev`` on Ubuntu).

**X2CAMF circular import: "cannot import name 'libx2camf' from partially initialized module"**
   After building X2CAMF, the shared library ``libx2camf.cpython-*.so``
   is in the ``build/`` directory but must be inside the ``x2camf/``
   package directory. Copy it:
   ``cp build/libx2camf.cpython-*.so x2camf/``.
   Also ensure that the x2camf root directory is on your ``PYTHONPATH``:
   ``export PYTHONPATH=/path/to/x2camf:$PYTHONPATH``.

**macOS-specific issues**
   - Install ``gfortran`` via Homebrew: ``brew install gcc``
   - The build script auto-detects macOS and uses ``gfortran`` by default
   - For MKL on macOS, Intel oneAPI or ``conda install mkl`` are recommended
   - If you have multiple Python versions, ensure the conda env is activated
     before running ``bash make``


Uninstallation
--------------

BAGH is not installed system-wide. To remove it, simply delete the repository
directory:

.. code-block:: shell

   rm -rf /path/to/bagh

And remove any PATH additions from your shell configuration files.
