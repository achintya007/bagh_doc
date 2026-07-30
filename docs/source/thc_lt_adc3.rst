THC-LT-IP/EA-ADC(3): Full Third Order, Memory-Disciplined
############################################################

THC-LT-IP-ADC(3) and THC-LT-EA-ADC(3) compute the same vertical
ionization potentials / electron affinities as conventional
``IP-ADC(3)`` / ``EA-ADC(3)``, with every two-electron integral
contraction tensor-hypercontracted (THC) and every 4-index orbital-
energy denominator Laplace-transformed (LT). Unlike the folded
THC-LT-(SOS-)IP/EA-ADC(2) methods (see :doc:`thc_lt_adc`), the doubles
block here is **not** eliminated -- exact folding only holds at strict
ADC(2) -- so the full ``(n_occ + n_occ^2*n_virt)`` (IP) or
``(n_virt + n_occ*n_virt^2)`` (EA) secular problem is solved by Davidson
iteration. What THC+LT still buys is memory discipline: no ``o^2v^2``,
``v^3``, or dense 4-index ERI buffer is ever formed, at any order.

Theory
======

Every contraction is built from a small set of fused THC-chain
primitives (``feri``, ``ft2``, ``ft2_feri``, ``ft2e``, ``fq2``, ``fq2e``,
and a doubly-Laplace-batched ``t2_2``-times-``ovvo`` chain), each of
which keeps its largest intermediate at ``O(v * N_thc^2)`` or smaller
regardless of perturbation order. The static singles-singles block
(``M_ij`` for IP, ``M_ab`` for EA) accumulates the second-order
2h1p/2p1h self-energy plus, at third order, the ``t1`` first-order
amplitude correction and a set of quadratic (``t2 . t2``) terms fused
directly through the coupling matrices -- including, for EA, a
``t2 . (vvvv-ladder t2)`` quadratic that never forms a ``vvvv``-sized
object. The matvec (``sigma``) similarly stays THC-fused at every term,
including the third-order 2h1p/2p1h-2h1p/2p1h coupling blocks.

The same engine, selected internally by an order parameter, also
evaluates ``adc(2)`` and ``adc(2)-x`` (the second-order self-energy with
and without the 2h1p/2p1h ladder/ring terms); only ``adc(3)`` is
currently reachable from the ``.inp`` interface (``! THC-LT-IP-ADC(3)`` /
``! THC-LT-EA-ADC(3)``) -- the lower orders are exercised by
THC-LT-SM-IP-ADC / THC-LT-SM-EA-ADC's blend (see :doc:`thc_lt_sm_adc`)
and by the module's own internal cross-checks, but are not yet exposed
as a separate method name of their own.

.. _thc-lt-adc3-fno:

FNO truncation
================

Both methods support an optional frozen-natural-orbital (FNO) virtual-
space truncation, computed *without* ever forming the MP2 ``t2``
amplitude (the virtual-virtual density is built via the same
double-Laplace-batched THC chains used elsewhere):

.. code-block:: shell

   thc_fno True
   thc_fno_thresh 1e-4

Natural orbitals with occupation below ``thc_fno_thresh`` are dropped,
the retained space is semicanonicalized, and the rest of the THC+LT+ADC
machinery runs in the truncated space unchanged. Because EA attachment
states live in the virtual space that FNO truncates directly (unlike
IP, where the truncation is one step removed), a tighter
``thc_fno_thresh`` is generally needed for EA than for IP to reach the
same accuracy target -- this is not defaulted differently between the
two methods (to avoid a silent-override surprise), so set it explicitly
for EA if you enable FNO there.

Validity
========

Exact for the orders it evaluates -- there is no approximation beyond
the finite Laplace quadrature and the THC factorization of the
two-electron integrals (plus, if enabled, the FNO virtual truncation).
EA-ADC(3) is first-order sensitive to the density-fitting (RI) error in
the ``vvvv`` block in a way IP-ADC(3) is not (IP only feels ``vvvv`` at
third order); this only matters when comparing against an external
reference -- it does not affect the THC-LT calculation itself, which is
internally DF-consistent throughout.

Keywords
========

- ``nroots``: number of roots to converge.
- ``adc_convergence``: Davidson convergence tolerance for each root's
  energy.
- ``thc_fno`` / ``thc_fno_thresh``: see :ref:`thc-lt-adc3-fno` above.
- ``Debug``: when ``True``, cross-checks the converged roots against
  PySCF ADC(3) run in the *same* (possibly FNO-truncated) orbital space,
  isolating the THC+LT approximation error from any FNO truncation
  error.

The auxiliary basis is taken from the ``%ribasis`` block / the
auxiliary-basis word on the ``!`` line, as for every other DF-based
method.

MPI parallelism
================

Not yet available for either method (both stay on a single rank if
launched under ``mpirun`` -- see :doc:`parallel_execution`).

Examples
========

IP
---

.. code-block:: shell

   ! THC-LT-IP-ADC(3) cc-pvdz cc-pvdz-ri

   %cc
   nroots 3
   end

   *xyz 0 1
   O 0 0 0
   H 0 0 1
   H 0 1 0
   *

which gives:

.. code-block:: shell

   system: o=5 v=19 naux=84  N_thc=278  n_LT=18  method=adc(3)

   THC+LT IP-adc(3)   conv=True
   root 1  IP = 0.447585 a.u.  12.1794 eV
   root 2  IP = 0.555821 a.u.  15.1247 eV
   root 3  IP = 0.637402 a.u.  17.3446 eV

   memory audit: largest einsum intermediate = 1806444 elements (grid-class cap 5873584);  for reference o^2v^2 = 9025, N^2 = 77284

The memory audit line reports the largest intermediate actually formed
anywhere in the run, next to the cap it was kept under and, for scale,
what a naive ``o^2v^2`` (or ``N_thc^2``) buffer would have cost -- here
the largest intermediate (1.8M elements) stays well under the cap and
is a small multiple of ``o^2v^2``, never anywhere near a dense
``ovvv``/``vvvv``-sized object.

EA
---

Same input, method name swapped:

.. code-block:: shell

   ! THC-LT-EA-ADC(3) cc-pvdz cc-pvdz-ri

   %cc
   nroots 3
   end

   *xyz 0 1
   O 0 0 0
   H 0 0 1
   H 0 1 0
   *

which gives:

.. code-block:: shell

   system: o=5 v=19 naux=84  N_thc=278  n_LT=18  method=adc(3)

   THC+LT EA-adc(3)   conv=True
   root 1  EA = 0.154743 a.u.  4.2108 eV
   root 2  EA = 0.232480 a.u.  6.3261 eV
   root 3  EA = 0.410261 a.u.  11.1638 eV

   memory audit: largest einsum intermediate = 1806444 elements (grid-class cap 5873584);  for reference o^2v^2 = 9025, N^2 = 77284
