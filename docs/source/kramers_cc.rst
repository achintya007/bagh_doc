Kramers-Restricted and Kramers-Unrestricted CD-CCSD(T)
#######################################################

BAGH provides a Kramers-symmetry-adapted coupled-cluster family on the
``SOC-X2CAMF`` interface, built on Cholesky-decomposed (CD) two-electron
integrals: **KR-CCSD(T)** for closed-shell (Kramers-restricted)
references, **KU-CCSD(T)** for general open-shell or time-reversal-broken
references, and their frozen-natural-spinor variants **KR-FNS-** /
**KU-FNS-CCSD(T)**.  Optional tensor-hypercontraction (THC) and
Laplace-transform (LT) acceleration reduce the formal scaling of the
rate-limiting steps.  The implementation lives in ``bagh_code/kramers``
and reuses the vvvv-free CD spinor CCSD of socutils.

Kramers adaptation
===================

For a closed-shell reference the converged spinors are rotated into
exact Kramers pairs (:math:`\bar p = T p`, robust to higher than
two-fold degeneracies) and blocked as [occ_A, occ_B, vir_A, vir_B].
In this ordering every CCSD tensor obeys a single time-reversal
relation, so each rate-limiting contraction (amplitude update,
integral transformation, particle-particle ladder, (T) triples loop)
is evaluated only on the unbarred half and the barred half follows
from time reversal.  This halves the cost of every heavy step and the
converged amplitudes are exactly Kramers symmetric (time-reversal
residual :math:`\sim 10^{-16}`, printed in the output).

For Kramers-unrestricted references (odd electron count, or an
even-electron SCF that breaks time reversal) the same machinery runs
without the symmetry halving; everything else -- CD integrals, THC
ladder, LT-(T), FNS truncation -- carries over unchanged.

Frozen natural spinors (FNS)
=============================

The MP2 virtual-virtual density is diagonalized (for KR pair-wise, so
the truncated space is exactly Kramers closed), natural spinors above
an occupation threshold (``fnothresh``, default 1e-4) or a fixed even
count (``nvir_act``) are kept and semicanonicalized, and the standard
MP2 truncation correction dMP2 = E_MP2(full) - E_MP2(FNS) is printed
and added to the reported total energy.  Frozen cores are frozen as
whole Kramers pairs for KR (``fc_no`` must be even); no parity
restriction applies to KU.

THC and Laplace-transform acceleration
=======================================

Two keyword-controlled accelerations, both limited only by the ERI THC
fit (typically :math:`10^{-6}`--:math:`10^{-8}`, reported in the
output) and the Laplace quadrature (:math:`\sim 10^{-7}` relative at
the default spacing):

* ``kr_thc``: the particle-particle ladder of every CCSD iteration is
  contracted through grid least-squares THC factors of the ERIs
  (fitted against the Cholesky vectors, pivoted-Cholesky grid
  selection), replacing the :math:`O(n_{aux} o^2 v^3)` CD ladder by an
  :math:`O(K^2 o^2 v + K o^2 v^2)` chain with :math:`K =`
  ``kr_thc_rank`` :math:`\times n_{mo}` grid points.
* ``kr_t_alg lt``: the (T) energy is evaluated from the Laplace
  transform of the triples denominator through factorized contraction
  topologies -- no six-index intermediate is ever built and the formal
  scaling drops from :math:`N^7` to :math:`N^6` (audited per topology;
  contraction paths with a guaranteed scaling bound).
* ``kr_t_alg lt-thc``: additionally the ovvv-type integral
  :math:`\langle bc||ei\rangle` is THC-expanded inside every topology
  (never stored: :math:`O(Kv+K^2)` instead of :math:`O(o v^3)`); the
  dominant topology classes then run at formal :math:`N^5`, the
  remaining amplitude-pair cross terms at :math:`N^6`.  The t2
  amplitudes are kept exact, so no amplitude-compression error enters.

Formal scalings (audited)
--------------------------

===============================  =============  ==========================
step                             conventional   accelerated
===============================  =============  ==========================
CCSD pp-ladder                   N^6            N^5   (``kr_thc``)
CCSD iteration (overall)         N^6            N^6, ladder prefactor gone
(T) triples                      N^7            N^6   (``kr_t_alg lt``)
(T) triples, THC integrals       --             N^5-dominant (``lt-thc``)
===============================  =============  ==========================

Kramers restriction halves each step in addition.  Reducing the
remaining :math:`N^6` terms requires a THC representation of the t2
amplitudes themselves; for antisymmetrized spinor amplitudes this is
an open problem (a Kramers-block-adapted factorization is planned) and
is therefore not enabled.

Examples
=========

Closed-shell KR-FNS-CCSD(T) with all accelerations:

.. code-block:: shell

   ! KR-FNS-CCSD(T) SOC-X2CAMF spinor ccpvdz

   %cc
   CD True
   cd_threshold 1e-6
   x2c_type x2camf
   fc True
   fnothresh 1e-4
   kr_thc True
   kr_t_alg lt-thc
   cc_convergence 1e-8
   end

   *xyz 0 1
   Ar 0.0 0.0 0.0

Open-shell KU-CCSD(T) (sodium doublet):

.. code-block:: shell

   ! KU-CCSD(T) SOC-X2CAMF spinor ccpvdz

   %cc
   CD True
   cd_threshold 1e-6
   x2c_type x2camf
   fc True
   kr_t_alg lt
   cc_convergence 1e-8
   end

   *xyz 0 2
   Na 0.0 0.0 0.0

The plasma models combine transparently: with ``plasma debye`` the
screened Cholesky vectors are passed to the KR/KU treatment, so SCF
and correlation see the same screened interaction.

Validity and remarks
=====================

* KR methods require an even-electron aufbau (Kramers-restricted)
  reference; KU methods accept any single-determinant spinor
  reference (the usual caveat applies that an open-shell Kramers-
  unrestricted determinant need not be a spin eigenstate).
* Validated against pyscf GCCSD(T) and the plain CD spinor CCSD(T):
  closed-shell agreement to the CD threshold, SOC cases (x2camf) to
  :math:`10^{-16}`; LT-(T) agrees with the exact (T) to the quadrature
  accuracy (:math:`10^{-13}` at ``laplace_h 0.4``).
* Keywords: see :doc:`keyword` -- ``kr_thc``, ``kr_thc_rank``,
  ``kr_thc_grid``, ``kr_t_alg``, ``laplace_h`` plus the shared
  ``cd_threshold``, ``fc``/``fc_no``, ``fnothresh``, ``nvir_act``.
