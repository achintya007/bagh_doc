Folded QED-EE-ADC(2): FOLDED-, LT-SOS- and THC-LT-QED-EE-ADC(2)
#################################################################

Three methods compute polaritonic excitation energies of a closed-shell
molecule in an optical cavity (one cavity mode, single-photon space) from the
QED-EE-ADC(2) secular equations of ``QED-EE-ADC(2)``, with the doubles
eliminated exactly:

``FOLDED-QED-EE-ADC(2)``
   exact QED-EE-ADC(2), density-fitted integrals, exact denominators.
   O(o\ :sup:`2` v\ :sup:`2` X) per sigma, no o\ :sup:`2` v\ :sup:`2` array kept.
``LT-SOS-QED-EE-ADC(2)``
   scaled-opposite-spin variant with Laplace-transformed denominators,
   O(N\ :sup:`4`).
``THC-LT-QED-EE-ADC(2)``
   exact QED-EE-ADC(2) with Laplace-transformed denominators and the exchange
   terms by tensor hypercontraction (THC), formally O(N\ :sup:`4`).

All three run their own coherent-state QED-RHF and include the two-body dipole
self-energy (DSE) in the integrals.

Theory
======

The singles space is ``s = (x, p, y)``: electronic singles ``x = re1``
(o x v), the one-photon amplitude ``p = rp1`` and the electron-photon singles
``y = rep1`` (o x v). The doubles are ``re2`` and ``rep2`` (o x o x v x v).
With canonical QED-HF orbitals the doubles-doubles block is diagonal,
``D_ijab = e_a + e_b - e_i - e_j`` on ``re2`` and ``D_ijab + omega`` on ``rep2``,
so Löwdin partitioning removes the doubles exactly:

.. code-block:: text

   [ H11  C12 ] [ s  ]       [ s  ]
   [ C21  D   ] [ x2 ]  = E  [ x2 ]   =>   [ H11 + C12 (E - D)^-1 C21 ] s = E s

``H11`` is the static singles block (MP2-dressed one-body terms, the ``m11``
block, every cavity coupling). ``C21`` sends ``x`` and ``y`` to the doubles,
including the dipole term ``-sqrt(omega/2) (d_jb y_ia + d_ia y_jb)``. ``C12``
projects the doubles back. Each root ``k`` is the fixed point
``E = lambda_k(E)`` of the singles problem. Below the doubles poles the
eigenvalue count identifies the root, so root ``k`` is the ``k``-th
eigenvalue at ``E = E_k``. ``E`` is converged by secant steps, each with an
inner non-symmetric Davidson solve.

The fold is exact. For water/6-31G it reproduces the eigenvalues of the
explicitly built unfolded QED-EE-ADC(2) matrix to 1e-11 Eh.

Integrals
---------

Every two-electron integral is used as ``(pq|rs) = sum_P L^P_pq L^P_rs``.
``L`` is an RI factor in the ``%ribasis`` (JK-fit by default; the MP2-fit
sets leave 10-20 meV errors from the CIS-like part of ``H11``). The DSE
two-body term ``(lambda.d)_pq (lambda.d)_rs`` is one more column of ``L``,
which costs nothing. The MP1 amplitude and the doubles of the trial vector
are only ever built one block of occupied orbitals at a time.

QED-HF reference
----------------

The reference is coherent-state QED-RHF. The one-electron Hamiltonian gets
``1/2 lambda_x lambda_y <r_x r_y>``, the Fock matrix gets
``-1/2 (lambda.r) D (lambda.r)``, and the DSE Coulomb mean field cancels
against the coherent-state shift. It is converged with DIIS, starting from
the RHF density. The ADC two-electron integrals carry the matching DSE term.

LT-SOS
------

``LT-SOS-QED-EE-ADC(2)`` replaces the spin-adapted combinations
``2t - t^(ab)`` (MP1 amplitude) and ``2 x2 - x2^(ab)`` (trial-vector
doubles) by ``c_os t`` and ``c_os x2``. Same-spin terms are dropped and
opposite-spin terms are scaled by ``c_os`` (keyword ``cos_scale``, default
1.3), as in SOS-CC2/SOS-ADC(2). What remains are Coulomb-type contractions
only. With the Laplace transform

.. code-block:: text

   1/(D_ijab - E) = sum_g w_g exp(-(D_ia - E/2) t_g) exp(-(D_jb - E/2) t_g)

each of them separates into DF three-index steps, O(n_LT o v X^2) per sigma
with no four-index quantity at all. The quadrature points and weights are
least-squares fitted on the actual denominator range, to a maximum relative
error ``qed_lt_tol``. The Laplace error in the roots is about 1e-4 meV at the
default.

SOS is a different model from QED-EE-ADC(2): for water/cc-pVDZ in a cavity it
shifts the roots by +80 to +150 meV.

Exact THC-LT
------------

``THC-LT-QED-EE-ADC(2)`` keeps the exchange halves. Contractions such as
``sum_mfe (mf|ae) x2_mief`` pair integral indices with indices of two
different doubles factors, which DF cannot separate below O(N^5). These terms
use least-squares THC integrals,
``(pq|rs) = sum_ab X_pa X_qa V_ab X_rb X_sb``, fitted to the same ``L``. The
interpolation points are chosen by pivoted QR of a sketch of the orbital-pair
products on a DFT grid, and ``N_thc = qed_thc_rank * nmo``. Cost is
O(n_LT X (o+v) N_thc^2) per sigma.

At ``qed_thc_rank 8`` the roots equal those of ``FOLDED-QED-EE-ADC(2)``
(1e-5 meV, water/cc-pVDZ). At 4 they are 50-95 meV off.

Transition moments
------------------

Transition dipoles and oscillator strengths are printed for every root.
They are at the level of ``QED-EE-ADC(2)``: zeroth-order transition density
``sqrt(2) re1``, from the eigenvector normalized over the full unfolded space.
There are no second-order (ISR) or photonic contributions.

Cost and choice of method
=========================

Per-sigma timings, water clusters, cc-pVDZ, 8 cores:

+-------------+----------+------------------+------------------+------------------+
| system      | nbf      | FOLDED (DF)      | LT-SOS           | THC-LT           |
+=============+==========+==================+==================+==================+
| (H2O)1      | 24       | 0.002 s          | 0.036 s          | 0.70 s           |
+-------------+----------+------------------+------------------+------------------+
| (H2O)2      | 48       | 0.014 s          | 0.081 s          | 7.0 s            |
+-------------+----------+------------------+------------------+------------------+
| (H2O)4      | 96       | 0.16 s           | 0.85 s           | --               |
+-------------+----------+------------------+------------------+------------------+
| (H2O)8      | 192      | 2.9 s            | 6.8 s            | --               |
+-------------+----------+------------------+------------------+------------------+

- ``FOLDED-QED-EE-ADC(2)`` is the default choice. It is exact, and already
  faster than the unfolded ``QED-EE-ADC(2)`` from about 100 basis functions:
  33 s against 60 s for (H2O)4.
- ``LT-SOS-QED-EE-ADC(2)`` grows about x8 per doubling of the system against
  about x18 for the DF fold. It should pay off above roughly 400 basis
  functions.
- ``THC-LT-QED-EE-ADC(2)`` is formally O(N^4), but the THC rank needed for
  meV accuracy makes it far slower than the DF fold at every size tested.

Input
=====

.. code-block:: shell

   ! FOLDED-QED-EE-ADC(2) cc-pvdz cc-pvdz-jkfit

   %cc
   nroots 3
   qed True
   cav_frequency 0.4
   cav_lambda_x 0.02
   cav_lambda_y 0.03
   cav_lambda_z 0.05
   adc_convergence 1e-8
   end

   *xyz 0 1
   O 0 0 0.1173
   H 0 0.7572 -0.4692
   H 0 -0.7572 -0.4692
   *

Replace the method with ``LT-SOS-QED-EE-ADC(2)`` (optionally with
``cos_scale``, ``qed_lt_tol``) or ``THC-LT-QED-EE-ADC(2)`` (optionally with
``qed_thc_rank``, ``qed_lt_tol``). The third basis on the method line is the
auxiliary basis.

Output
======

.. code-block:: text

   root 1  QED-EE = 0.29890234 a.u.  8.133547 eV
   root 2  QED-EE = 0.37459865 a.u.  10.193349 eV
   root 3  QED-EE = 0.38380960 a.u.  10.443991 eV

     root   E (a.u.)        E (eV)      w_re1   w_rp1   w_rep1  w_re2   w_rep2
        3  0.3838095953   10.443991  0.4903  0.4716  0.0031  0.0348  0.0002

     root   E (eV)      fosc        TX          TY          TZ
        3   10.443991   0.059155    0.000093   -0.081701   -0.473830

The weights are those of the unfolded eigenvector, with the doubles rebuilt
from the converged root. Root 3 above is a polariton: about half electronic,
half photon.

Limitations
===========

- Closed-shell reference, one cavity mode, single-photon space
  (``nphotons 1``). Frozen core is not supported.
- Strict ADC(2) only: at ADC(2)-x the doubles block is no longer diagonal.
- LT is valid only below the doubles poles. A root at or above ``min D_ijab``
  falls back to exact denominators for that sigma.
- Not MPI-parallel; under ``mpirun`` only rank 0 runs.

Tests
=====

``tests/test_qed_adc2_folded.py`` covers the fold against the unfolded matrix,
occupied blocking, LT-SOS against exact denominators, THC-LT against DF, the
DSE factor and the transition moments. The regression entries
``FOLDED-QED-EE-ADC2``, ``LT-SOS-QED-EE-ADC2`` (fast) and
``THC-LT-QED-EE-ADC2`` (slow) are in ``tests/reference_data.yaml``.
