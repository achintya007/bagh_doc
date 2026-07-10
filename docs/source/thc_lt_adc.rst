THC-LT-(SOS-)ADC(2): Folded, Doubles-Free ADC(2)
##################################################

Three related methods -- THC-LT-(SOS-)IP-ADC(2), THC-LT-(SOS-)EA-ADC(2),
and THC-LT-EE-ADC(2) -- compute the same ionization potentials, electron
affinities, and excitation energies as the corresponding ``IP-ADC(2)``/
``EA-ADC(2)``/``EE-ADC(2)`` secular equations, but with the doubles block
eliminated exactly. This is not an approximation to ADC(2) itself (see
:ref:`thc-lt-adc-validity` below for the one place where it does
introduce error); it is a different, much cheaper way of solving the
same secular equation.

Theory: IP/EA (fully folded)
==============================

At strict ADC(2) the 2h1p-2h1p block of the ADC secular matrix is
diagonal (``d_ajk = e_j + e_k - e_a``), so Lowdin partitioning eliminates
it *exactly*, folding its effect into an energy-dependent correction to
the singles-singles block:

.. code-block:: text

   [ M     C_sd ] [ c  ]        [ c  ]
   [ C_ds  D    ] [ r2 ]  = w   [ r2 ]      ,   D = diag(d_ajk)

   =>  [M + F(w)] c = w c ,     F(w) = C_sd (w - D)^-1 C_ds

with ``C_sd[i,ajk] = 2(ja|ki) - (ka|ji)`` and ``C_ds[ajk,i] = (ja|ki)``.
This is the approach of Mester & Kallay (*J. Chem. Theory Comput.* **19**,
3982 (2023), Sect. 2.3), who rebuild ``F(w)`` at O(N^4) every
self-consistency cycle.

This implementation goes one step further: below the 2h1p continuum the
resolvent argument ``w - d_ajk`` is positive, so a Laplace transform
separates the ``w``-dependence from the rest of the sum into a scalar,

.. code-block:: text

   1/(w - d_ajk) = sum_g  w_g e^{-w t_g} [tau_j tau_k sig_a]_g

   =>  F(w) = sum_g  w_g e^{-w t_g}  K_g

where the ``K_g`` are ``w``-independent, occupied x occupied matrices,
precomputed *once* as dressed double-``ovoo`` tensor-hypercontraction
(THC) chains. After that, every self-consistency cycle costs only
O(n_LT * n_occ^2 + n_occ^3): scale and sum the ``K_g``, diagonalize an
``n_occ`` x ``n_occ`` matrix, and follow the desired root by overlap with
the previous cycle's vector ("root homing"). Quasiparticle pole
strengths (printed alongside each root) come for free from the
Laplace-transformed resolvent's analytic ``w``-derivative.

Relative to conventional ``IP-ADC(2)``, this eliminates the
``n_occ^2 * n_virt``-sized doubles vector, the Davidson subspace built
over it, and every per-iteration ``ovoo`` coupling contraction.

THC-LT-(SOS-)EA-ADC(2) is the particle-hole mirror: the fold happens in
the 2p1h (occupied x virtual x virtual) doubles block instead
(``d_ibc = -e_i + e_b + e_c``, diagonal at strict ADC(2)), so the
iterative eigenproblem lives in the **virtual**-orbital space (dimension
``n_virt``) rather than the occupied one, and the ``K_g`` factors are
dressed double-``ovvv`` chains (``v x v`` matrices) instead of
``ovoo``/``o x o``. Attachment energies are the eigenvalues directly --
there is no overall sign flip the way there is for IP. Since strict
EA-ADC(2) contains no ``vvvv`` integrals, it is not sensitive to the
density-fitting error in the way EA-ADC(3) is (see :doc:`thc_lt_adc3`);
plain (non-DF-consistent) reference comparisons are fine here.

Theory: EE (particle-conserving)
===================================

THC-LT-EE-ADC(2) computes neutral excitation energies. Unlike IP/EA, EE
is particle-conserving, so the singles space *is* the particle-hole (ph)
space itself (dimension ``n_occ * n_virt``) -- there is no smaller (``o``
or ``v``) space to fold onto, and no SOS variant. What the fold still
buys: the 2p2h doubles vector (dimension ``n_occ^2 * n_virt^2``) is never
stored in the Davidson subspace, and the frequency dependence is still
Laplace-separated, so there is no explicit 2p2h energy-denominator array
either. A single 2p2h-shaped object is nonetheless reconstructed as a
transient inside each matvec (this is intrinsic to any ADC(2)/CIS(D):
it is the first-order double-excitation amplitude of the current trial
vector) and immediately consumed.

Because the eigenproblem does not collapse onto a smaller space, it is
instead solved by explicitly building and diagonalizing the full
``(n_occ*n_virt) x (n_occ*n_virt)`` matrix ``A + F(w)`` at each
self-consistency cycle and following the desired root by overlap --
the same root-homing idea as IP/EA, just without the dimension
reduction. The static ph-ph block ``A`` is built once (per calculation,
not per matvec) from the same THC factors, transcribed verbatim from
PySCF's ``radc_ee.get_imds``.

This method needs a substantially tighter THC grid/tolerance than IP/EA
to keep ``A`` accurate (it reconstructs dense integral blocks directly
from the THC factors, rather than staying inside fused THC chains the
way IP/EA do), which in turn means the retained THC rank comes out large
relative to ``n_virt`` for typical systems. A further, fully-fused
(THC-only, ``O(N^2)``-per-term) form of the per-matvec resolvent exists
and is implemented internally, but is *not* used by default: at the
THC rank this method's accuracy needs, that fused form is empirically
slower (not faster) than the direct dense contraction against the
already-precomputed integral blocks, since the THC rank ends up larger
than the space it would be compressing. It is kept available (and
cross-checked against the dense path whenever ``Debug True`` is set) for
a possible future two-grid scheme, not as something to switch to today.

.. _thc-lt-adc-validity:

Validity
========

The exact folding step requires strict ADC(2) (a diagonal doubles
block) -- this is why neither method has a ``-X`` or third-order
counterpart (see :doc:`thc_lt_adc3` for those, solved without folding).
The Laplace-transform step additionally requires the root to sit on the
correct side of its continuum threshold: above the 2h1p threshold for
IP, below the 2p1h threshold for EA. This holds for ordinary valence
ionizations/attachments but can fail for deeply embedded or
near-continuum states; each printed root reports its gap to that
threshold and prints a warning if the gap is small. Beyond that, the
only approximations are the finite Laplace quadrature and the THC
factorization of the two-electron integrals, both of which are
systematically improvable and, at the defaults below, contribute only a
few hundredths of a mHartree (see the worked examples).

Keywords
========

**cos_scale** ``Float``

.. code-block:: shell

   cos_scale 1.3

Opposite-spin scaling factor for THC-LT-SOS-IP-ADC(2) and
THC-LT-SOS-EA-ADC(2); unused for the unscaled variants and for
THC-LT-EE-ADC(2) (no SOS variant).

The following existing keywords are reused as-is (no THC-LT-specific
keyword needed):

- ``nroots``: number of roots to converge (ionization potentials
  counting down from the HOMO for IP, attachment energies counting up
  from the LUMO for EA, excitation energies for EE).
- ``adc_convergence``: self-consistency convergence tolerance for each
  root's energy.
- ``Debug``: when ``True``, additionally checks each converged root
  against a dense, non-THC/non-Laplace exact-resolvent solve of the same
  folded partitioning -- this isolates the THC+Laplace approximation
  error for the run at hand (the folding algebra itself is an identity
  proven once by construction, so it is not re-checked every run). For
  THC-LT-EE-ADC(2) this also cross-checks the fused matvec against the
  dense one on a random trial vector (see the EE theory note above).

The auxiliary basis for the underlying density fitting is taken from the
``%ribasis`` block / the auxiliary-basis word on the ``!`` line, same as
every other DF-based method; if none is given it falls back to a
default MP2-fit auxbasis.

MPI parallelism
================

THC-LT-(SOS-)IP-ADC(2) and THC-LT-(SOS-)EA-ADC(2) run under multiple MPI
ranks:

.. code-block:: shell

   bagh nprocs input.inp

THC-LT-EE-ADC(2) does **not** have an MPI-parallel path (it also stays
on a single rank if launched under ``mpirun`` for a different method --
see :doc:`parallel_execution`).

See :doc:`parallel_execution` for the launcher syntax and how to combine
``nprocs`` with ``einsum_threads`` on a single node. Every major cost
center is sharded: the DF 3-center integrals over auxiliary shells, the
THC grid evaluation and interpolation-point selection over grid points,
the static block build (``M_ij`` for IP, ``M_ab`` for EA) over its term
list, and the ``K_g`` build over Laplace-quadrature points -- the last
of these has no data dependency between quadrature points at all, so it
is the most cleanly scalable piece of the method. Without ``nprocs``
this reduces to exactly the serial computation.

Examples
========

IP
---

.. code-block:: shell

   ! THC-LT-SOS-IP-ADC(2) cc-pvdz cc-pvdz-ri

   %cc
   nroots 3
   end

   *xyz 0 1
   O 0 0 0
   H 0 0 1
   H 0 1 0
   *

| ``THC-LT-SOS-IP-ADC(2)``: name of the method (drop ``SOS-`` for the
  unscaled variant).
| ``cc-pvdz`` / ``cc-pvdz-ri``: orbital and density-fitting auxiliary
  basis sets.
| ``nroots 3``: converge the 3 outermost ionization roots.

which gives:

.. code-block:: shell

   system: o=5 v=19 naux=84  N_thc=110  n_LT=18  method=SOS-IP-ADC(2)  c_os=1.3

              ----------------------------------------------------
        --------------- THC-LT-SOS-IP-ADC(2) values -------------------
              ----------------------------------------------------

   root 1  IP = 0.394938 a.u.  10.7468 eV   pole strength 0.9115   (7 scf its, gap to continuum 0.771 a.u.)

   root 2  IP = 0.511623 a.u.  13.9220 eV   pole strength 0.9219   (6 scf its, gap to continuum 0.654 a.u.)

   root 3  IP = 0.608964 a.u.  16.5708 eV   pole strength 0.9416   (5 scf its, gap to continuum 0.557 a.u.)

   **** THC-LT-SOS-IP-ADC(2) Calculation Completed ****

   storage: K_g = 18x5x5 = 450 elements; doubles vector of size 475 never formed

``system:`` reports the problem size (``o``/``v`` occupied/virtual
orbitals, ``naux`` DF auxiliary functions, ``N_thc`` THC interpolation
points, ``n_LT`` Laplace quadrature points). Each root line gives the IP
in both Hartree and eV, the pole strength (spectroscopic factor), the
number of self-consistency cycles taken, and the gap to the 2h1p
continuum threshold discussed above. With ``debug True``, an additional
block reports each root's deviation from the dense exact-resolvent
solve, e.g. ``dIP = +0.014 mHa`` -- the actual THC+Laplace error for
this system, comfortably below chemical accuracy at the defaults used
here.

EA
---

Same input, method name swapped to the EA family:

.. code-block:: shell

   ! THC-LT-SOS-EA-ADC(2) cc-pvdz cc-pvdz-ri

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

   system: o=5 v=19 naux=84  N_thc=184  n_LT=18  method=SOS-EA-ADC(2)  c_os=1.3

              ----------------------------------------------------
        --------------- THC-LT-SOS-EA-ADC(2) values -------------------
              ----------------------------------------------------

   root 1  EA = 0.153273 a.u.  4.1708 eV   pole strength 0.9842   (4 scf its, gap to continuum 0.691 a.u.)

   root 2  EA = 0.230751 a.u.  6.2791 eV   pole strength 0.9827   (4 scf its, gap to continuum 0.614 a.u.)

   root 3  EA = 0.676390 a.u.  18.4055 eV   pole strength 0.9650   (5 scf its, gap to continuum 0.168 a.u.)

   **** THC-LT-SOS-EA-ADC(2) Calculation Completed ****

   storage: K_g = 18x19x19 = 6498 elements; doubles vector of size 1805 never formed

Same fields as IP, mirrored: the iterative space here is the 19
virtuals rather than the 5 occupied orbitals, so ``N_thc`` and the
``K_g`` storage are correspondingly bigger, and each root reports its
gap to the 2p1h continuum from below rather than the 2h1p continuum
from above.

EE
---

.. code-block:: shell

   ! THC-LT-EE-ADC(2) cc-pvdz cc-pvdz-ri

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

   system: o=5 v=19 naux=84  N_thc=278  n_LT=18  method=EE-ADC(2)
   ----------------------------------------------------
   --------------- THC-LT-EE-ADC(2) values -------------------
   ----------------------------------------------------

   root 1  EE = 0.278896 a.u.  7.5892 eV   (8 scf its, gap to 2p2h continuum 1.061 a.u.)

   root 2  EE = 0.356655 a.u.  9.7051 eV   (8 scf its, gap to 2p2h continuum 0.984 a.u.)

   root 3  EE = 0.405995 a.u.  11.0477 eV   (8 scf its, gap to 2p2h continuum 0.934 a.u.)

   **** THC-LT-EE-ADC(2) Calculation Completed ****

   memory: Davidson subspace holds ph vectors (size 95); 2p2h vector (size 9025) never stored -- only a transient per matvec

No SOS variant and no pole strength (that concept is IP/EA-specific).
``N_thc`` is noticeably larger here than for IP/EA on the same system
(278 vs 110/184) -- the tighter THC grid this method needs for accuracy
(see the EE theory note above), not a different molecule or basis.
