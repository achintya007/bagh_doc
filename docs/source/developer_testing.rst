Testing Infrastructure
======================

This guide describes the BAGH testing infrastructure, explains how to run
and write tests, and provides best practices for contributors. It is aimed
at quantum chemistry developers who may not be deeply familiar with Python
testing tooling.

.. contents:: On this page
   :local:
   :depth: 2


Overview
--------

BAGH uses `pytest <https://docs.pytest.org/>`_ as its test framework. The
test suite is organised into two broad categories:

* **Fast (unit) tests** -- verify that the code base is structurally sound,
  that modules import correctly, that the input parser behaves as expected,
  and that utility functions return correct results. These tests run in
  seconds and require no quantum-chemistry computation.

* **Slow (regression) tests** -- execute BAGH on small input files and
  compare computed energies, ionisation potentials, and electron affinities
  against stored reference values. These tests exercise the full
  computational pipeline.

The test files live in the ``tests/`` directory of the main BAGH repository:

.. list-table::
   :header-rows: 1
   :widths: 30 10 60

   * - File
     - Tests
     - Purpose
   * - ``tests/conftest.py``
     - --
     - Shared pytest fixtures available to all test modules
   * - ``tests/test_build.py``
     - 30
     - Smoke tests: project structure, imports, dependency checks
   * - ``tests/test_input_parser.py``
     - 56
     - Input parser and ``cc_input`` class validation
   * - ``tests/test_utilities.py``
     - 31
     - ``check.py``, ``printer.py``, ``methodinfo``, ``math_util``
   * - ``tests/test_regression.py``
     - varies
     - Regression tests driven by ``reference_data.yaml``
   * - ``tests/reference_data.yaml``
     - --
     - Reference energies, tolerances, and metadata


Quick Start
-----------

All commands below assume you are in the root of the BAGH repository and
that BAGH and its dependencies (listed in ``requirements.txt``) are
installed in your active Python environment.

Run all fast tests (recommended first step)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   python3 -m pytest tests/ -m fast

This finishes in a few seconds and catches import errors, typos, and
parser regressions without running any electronic-structure calculation.

Run the full test suite
^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   python3 -m pytest tests/ -v

The ``-v`` flag produces verbose output so you can see each individual test
and its result.

Skip slow tests
^^^^^^^^^^^^^^^

.. code-block:: bash

   python3 -m pytest tests/ -m "not slow"

Run only regression tests
^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   python3 -m pytest tests/ -m regression

Run tests for a specific method
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

.. code-block:: bash

   # By marker
   python3 -m pytest tests/ -m method_ccsd

   # By keyword expression
   python3 -m pytest tests/test_regression.py -k "CCSD"


Test Categories and Markers
---------------------------

pytest markers are defined in ``pyproject.toml`` and allow you to select
subsets of the test suite. The following markers are available:

.. list-table::
   :header-rows: 1
   :widths: 25 75

   * - Marker
     - Description
   * - ``fast``
     - Unit tests that require no BAGH computation
   * - ``slow``
     - Regression tests that execute BAGH on input files
   * - ``regression``
     - Full regression tests (overlaps with ``slow``)
   * - ``parser``
     - Input-parsing tests (``test_input_parser.py``)
   * - ``utilities``
     - Utility-function tests (``test_utilities.py``)
   * - ``method_ccsd``
     - CCSD-specific regression tests
   * - ``method_adc``
     - ADC-specific regression tests
   * - ``method_eom``
     - EOM-CC-specific regression tests
   * - ``method_so``
     - Spin--orbit-specific regression tests
   * - ``method_rel``
     - Relativistic-method regression tests
   * - ``method_fno``
     - Frozen natural orbital regression tests

Markers can be combined with boolean logic:

.. code-block:: bash

   # Run fast tests that are also parser tests
   python3 -m pytest tests/ -m "fast and parser"

   # Run CCSD or ADC regression tests
   python3 -m pytest tests/ -m "method_ccsd or method_adc"

   # Exclude regression tests
   python3 -m pytest tests/ -m "not regression"


Understanding Test Files
------------------------

test_build.py -- Build and import smoke tests
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

These 30 tests verify that:

* Essential source files and directories exist.
* Core Python modules can be imported without error.
* Required third-party packages listed in ``requirements.txt`` are
  installed.

These tests catch packaging and environment problems early and run with
no computational overhead.

test_input_parser.py -- Parser tests
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The 56 tests in this module exercise the BAGH input parser and the
``cc_input`` class. They cover:

* Keyword recognition and default values.
* Correct handling of method-specification blocks.
* Edge cases and malformed input.

test_utilities.py -- Utility function tests
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

31 tests covering the helper modules:

* ``check.py`` -- validation utilities.
* ``printer.py`` -- formatted output helpers.
* ``methodinfo`` -- method metadata lookups.
* ``math_util`` -- mathematical helper functions.

test_regression.py -- Regression tests
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Regression tests are **parametrised** from ``tests/reference_data.yaml``.
Each entry in the YAML file becomes a separate test case. The test runner:

1. Locates the ``.inp`` file specified in the YAML entry.
2. Executes BAGH on that input.
3. Parses the output for the expected ``search_pattern``.
4. Compares every value listed under ``values`` against the computed
   result, using the specified ``tolerance``.

Because these tests invoke full BAGH calculations they are marked ``slow``
and ``regression``.

conftest.py -- Shared fixtures
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

``conftest.py`` defines fixtures that are automatically available to every
test module. If you need a fixture that is shared across multiple test
files, place it here rather than duplicating it.


Adding New Tests
----------------

Adding a unit test
^^^^^^^^^^^^^^^^^^

1. Identify which test file is appropriate (``test_build.py``,
   ``test_input_parser.py``, ``test_utilities.py``, or create a new
   module).
2. Write a function whose name starts with ``test_``.
3. Apply the relevant marker(s) with ``@pytest.mark.<marker>``.
4. Run the new test in isolation to confirm it passes:

   .. code-block:: bash

      python3 -m pytest tests/test_utilities.py -k "test_my_new_test" -v

Example:

.. code-block:: python

   import pytest

   @pytest.mark.fast
   @pytest.mark.utilities
   def test_square_root_helper():
       from bagh_code.math_util import my_sqrt
       assert abs(my_sqrt(4.0) - 2.0) < 1e-12

Adding a regression test
^^^^^^^^^^^^^^^^^^^^^^^^

Regression tests are data-driven. You do **not** write a new Python test
function; instead you add an entry to ``tests/reference_data.yaml`` and the
parametrised test runner picks it up automatically.

**Step 1 -- Prepare the input file.**
Create or verify a ``.inp`` file in the ``test/`` directory. Keep it as
small as possible (minimal basis, small molecule) so the test finishes
quickly.

**Step 2 -- Obtain reference values.**
Run BAGH manually on the input file and record the quantities of interest
(energies, IPs, EAs, etc.) to full precision.

**Step 3 -- Add an entry to** ``reference_data.yaml``.

.. code-block:: yaml

   MY_NEW_TEST:
     input_file: my_new_test.inp
     search_pattern: "CCSD correlation energy"
     values:
       E_CCSD: -0.07068008830844916
     tolerance: 1.0e-8
     category: fast

Fields:

* ``input_file`` -- path to the ``.inp`` file (relative to ``test/``).
* ``search_pattern`` -- string that the test runner searches for in the
  BAGH output to locate the result line.
* ``values`` -- dictionary of quantity names to expected numerical values.
* ``tolerance`` -- maximum allowed absolute difference between computed
  and reference values.
* ``category`` -- ``fast`` or ``slow``; controls which marker is applied.

**Step 4 -- Verify.**

.. code-block:: bash

   python3 -m pytest tests/test_regression.py -k "MY_NEW_TEST" -v


Reference Data Management
--------------------------

All reference data lives in ``tests/reference_data.yaml``. When updating
reference values, follow these guidelines:

* **Never round reference values.** Store them at the full precision
  produced by BAGH.
* **Choose tolerances carefully.** A tolerance of ``1.0e-8`` Hartree is
  typical for total and correlation energies. Looser tolerances may be
  appropriate for properties that are inherently less precise (e.g.,
  numerical gradients).
* **Document the provenance.** If a reference value is updated, note the
  BAGH version or commit hash that produced it in a comment.
* **Keep the file sorted.** Group entries by method (CCSD, ADC, EOM, etc.)
  to make the file easy to navigate.

Example entry with a comment:

.. code-block:: yaml

   CCSD:
     input_file: CCSD.inp
     search_pattern: "CCSD correlation energy"
     values:
       E_CCSD: -0.07068008830844916   # from commit abc1234
     tolerance: 1.0e-8
     category: fast


Troubleshooting
---------------

Tests fail on import
^^^^^^^^^^^^^^^^^^^^

Ensure BAGH is installed in the active environment:

.. code-block:: bash

   pip install -e .

and that all dependencies are satisfied:

.. code-block:: bash

   pip install -r requirements.txt

Regression test fails with a small numerical difference
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Check whether the tolerance in ``reference_data.yaml`` is appropriate
  for the quantity being tested.
* Confirm that the same basis set files and integral library are being
  used.
* If a code change intentionally alters the result, update the reference
  value and record the reason.

A test is not discovered
^^^^^^^^^^^^^^^^^^^^^^^^

* The function name must start with ``test_``.
* The file name must start with ``test_`` or end with ``_test.py``.
* If the test is in ``reference_data.yaml``, ensure the YAML is valid
  (no indentation errors, no duplicate keys).

``pytest`` reports "no tests ran"
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You may have specified a marker or keyword expression that matches nothing.
Double-check the marker names (see the table above) and keyword spelling.

Known issues discovered by the test suite
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The following bugs were identified when the test suite was first run and
may serve as useful reference:

* **``methodinfo.py`` line 25** -- a typo where ``self.inlist`` should be
  ``self.intlist``.
* **``cc_input_read``** -- the ``fc`` keyword sets ``self.fc = True`` but
  never initialises ``self.fc_no``, which can cause ``AttributeError`` in
  downstream code.


Code Coverage
-------------

Generate an HTML coverage report with:

.. code-block:: bash

   python3 -m pytest tests/ --cov=bagh_code --cov-report=html

This creates an ``htmlcov/`` directory. Open ``htmlcov/index.html`` in a
browser to see line-by-line coverage.

To print a summary to the terminal instead:

.. code-block:: bash

   python3 -m pytest tests/ --cov=bagh_code --cov-report=term-missing

The ``--cov-report=term-missing`` flag highlights which lines are not
exercised by any test.

.. note::

   Coverage requires the ``pytest-cov`` package. Install it with
   ``pip install pytest-cov`` if it is not already available.


Best Practices for Developers
-----------------------------

1. **Run fast tests before every commit.** A quick
   ``python3 -m pytest tests/ -m fast`` takes only seconds and catches
   the most common mistakes.

2. **Run the full suite before opening a pull request.** Regression
   failures caught early save review cycles.

3. **Keep unit tests deterministic.** Avoid random data unless you seed
   the random number generator. Tests should produce the same result on
   every run.

4. **Test one thing per function.** Small, focused tests are easier to
   debug when they fail.

5. **Use markers consistently.** Every new test should carry at least one
   marker (``fast`` or ``slow``) so that selective test runs work
   correctly.

6. **Prefer absolute tolerances for energies.** Relative tolerances can
   mask large errors when the reference value is close to zero.

7. **Do not commit large input files.** Regression inputs should use
   minimal basis sets and small molecules to keep test times manageable.

8. **Add regression tests for bug fixes.** When you fix a bug, add a
   test that would have caught it. This prevents regressions.

9. **Keep** ``reference_data.yaml`` **under version control.** Any change
   to reference values should be reviewed in the same pull request as
   the code change that motivated it.


Continuous Integration (CI/CD)
------------------------------

BAGH uses GitHub Actions for automated testing. Three workflows are configured:

CI — Fast Tests (on every push)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

**File:** ``.github/workflows/ci.yml``

Triggered on every push and pull request to ``master``. Runs fast unit tests
across Python 3.9–3.12 in about 2 minutes. No Fortran or Cython compilation
is needed.

.. code-block:: text

   Trigger:  push to master, PR to master
   Runner:   GitHub-hosted (ubuntu-latest)
   Duration: ~2 minutes
   Tests:    148 fast tests (parser, utilities, build checks)
   Coverage: Generated for Python 3.11, uploaded as artifact

PR — Quality Gate
^^^^^^^^^^^^^^^^^^

**File:** ``.github/workflows/pr-check.yml``

Required check before merging any pull request. Verifies fast tests pass,
core imports work, and minimum code coverage threshold is met.

Nightly — Full Regression
^^^^^^^^^^^^^^^^^^^^^^^^^^

**File:** ``.github/workflows/nightly.yml``

Runs at 02:00 UTC every night. Two jobs:

1. **regression-fast** — Runs on GitHub-hosted runners. Executes regression
   tests that need only Python (no compiled extensions).
2. **regression-full** — Runs on a **self-hosted runner** that has BAGH fully
   built with Fortran/Cython extensions. Generates coverage report.

The nightly workflow can also be triggered manually from the GitHub Actions tab
with optional method and category filters.

Local Test Runner Script
^^^^^^^^^^^^^^^^^^^^^^^^^

A convenience script ``run_tests.sh`` is provided at the project root:

.. code-block:: bash

   ./run_tests.sh              # Fast tests only (default)
   ./run_tests.sh fast         # Fast tests only
   ./run_tests.sh all          # All tests including regression
   ./run_tests.sh regression   # Regression tests only
   ./run_tests.sh coverage     # Fast tests with HTML coverage report
   ./run_tests.sh method CCSD  # Specific method regression test

Setting Up a Self-Hosted Runner
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

For the nightly full regression suite, you need a self-hosted GitHub Actions
runner on a machine where BAGH is fully compiled:

1. Go to **Settings > Actions > Runners** in your GitHub repository
2. Click **New self-hosted runner** and follow the setup instructions
3. Ensure the runner machine has:

   - Intel Fortran compiler (``ifort``/``ifx``) or ``gfortran``
   - MKL or OpenBLAS
   - PySCF, NumPy, SciPy, h5py, Numba
   - BAGH built: ``mkdir build && cd build && cmake .. && cmake --build . --target compile_all``

4. Start the runner: ``./run.sh`` (or configure as a system service)
