.. _parallel-execution:

===================
Parallel Execution
===================

BAGH has two independent parallelism knobs: the number of MPI ranks a
calculation runs across, and the number of BLAS threads each rank uses
internally for tensor contractions (einsum). They are set separately and,
when running multiple ranks on one node, usually need to be tuned
together.

.. _running-mpi:

MPI ranks
---------

An optional first argument to the ``bagh`` launcher selects the number of
MPI ranks:

.. code-block:: shell

   bagh nprocs input.inp

   # e.g.
   bagh 4 input.inp

``nprocs`` currently only benefits ``thc_scf`` jobs (see
:doc:`methods_non-Relativistic`) -- it is the only method with an
MPI-parallel code path so far. Every other input, and ``thc_scf`` itself
when ``nprocs`` is omitted, runs exactly as plain ``bagh input.inp``. This
requires ``mpi4py`` and an MPI runtime (e.g. OpenMPI) in the active
environment; if either is unavailable, omit ``nprocs`` and BAGH falls back
to the ordinary serial driver.

For ``thc_scf``, each rank shards the ``naux`` (interpolation-point) index
of the Fock build's O(naux^2 * nbasis) contractions, and shards the
one-time DF-integral projection used to build the THC factors -- the DF
integrals themselves are generated once, on rank 0, and broadcast rather
than regenerated on every rank.

BLAS/einsum thread count
-------------------------

Set ``einsum_threads`` in the ``%cc`` block to cap the number of threads
the underlying BLAS backend (MKL, OpenBLAS, ...) uses for every
einsum/matrix contraction for the rest of the run:

.. code-block:: shell

   %cc
   einsum_threads 4
   end

The default, ``0``, leaves BLAS threading untouched. This requires
``threadpoolctl``; if it isn't installed, ``einsum_threads`` is ignored
with a warning rather than erroring, and every other input is unaffected
either way.

Combining both on a single node
--------------------------------

When multiple MPI ranks share a node, each rank's BLAS calls default to
using every core on the node -- with ``nprocs`` ranks all doing that, the
node is oversubscribed by roughly a factor of ``nprocs``. Set
``einsum_threads`` to about ``(cores on the node) / nprocs`` to avoid
this. For example, on a 16-core node running 4 ranks:

.. code-block:: shell

   ! RHF ccpvdz

   %cc
   thc_scf True
   thc_naux_mult 15
   einsum_threads 4
   end

.. code-block:: shell

   bagh 4 input.inp

.. note::

   Changing ``einsum_threads`` changes the floating-point summation order
   inside every BLAS call. This is negligible for most methods, but for
   an iterative nonlinear solve like SCF the tiny per-iteration rounding
   difference can compound over cycles. For ``thc_scf`` specifically,
   this can shift the converged energy by roughly the same few-mHartree
   amount as the THC fit's own approximation error (see
   :doc:`methods_non-Relativistic`), which is already that method's
   accuracy floor -- not a new source of error, but worth knowing if
   you're comparing runs made with different ``einsum_threads`` values.
