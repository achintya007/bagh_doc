THC-LT-(SOS-)IP-ADC(2): Folded, Doubles-Free IP-ADC(2)
########################################################

THC-LT-IP-ADC(2) and its opposite-spin-scaled variant THC-LT-SOS-IP-ADC(2)
compute the same vertical ionization potentials as ``IP-ADC(2)``, but the
iterative eigenproblem is solved entirely in the occupied-orbital
(singles) space -- the 2h1p doubles block is never stored, and no
Davidson iterations run over it. This is not an approximation to
ADC(2) itself (see :ref:`thc-lt-adc-validity` below for the one place
where it does introduce error); it is a different, much cheaper way of
solving the same secular equation.

Theory
======

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

.. _thc-lt-adc-validity:

Validity
========

The exact folding step requires strict ADC(2) (a diagonal doubles
block) -- this is why the method has no ``-X`` or third-order
counterpart. The Laplace-transform step additionally requires the root
to sit above the 2h1p continuum threshold (``w`` minus the largest
``d_ajk``), which holds for ordinary valence ionizations but can fail
for deeply embedded or near-continuum states; each printed root reports
its gap to that threshold and prints a warning if the gap is small.
Beyond that, the only approximations are the finite Laplace quadrature
and the THC factorization of the two-electron integrals, both of which
are systematically improvable and, at the defaults below, contribute
only a few hundredths of a mHartree (see the worked example).

Keywords
========

**cos_scale** ``Float``

.. code-block:: shell

   cos_scale 1.3

Opposite-spin scaling factor for THC-LT-SOS-IP-ADC(2); unused for the
unscaled THC-LT-IP-ADC(2).

The following existing keywords are reused as-is (no THC-LT-specific
keyword needed):

- ``nroots``: number of ionization roots to converge, counting down from
  the HOMO.
- ``adc_convergence``: self-consistency convergence tolerance for each
  root's energy.
- ``Debug``: when ``True``, additionally checks each converged root
  against a dense, non-THC/non-Laplace exact-resolvent solve of the same
  folded partitioning -- this isolates the THC+Laplace approximation
  error for the run at hand (the folding algebra itself is an identity
  proven once by construction, so it is not re-checked every run).

The auxiliary basis for the underlying density fitting is taken from the
``%ribasis`` block / the auxiliary-basis word on the ``!`` line, same as
every other DF-based method; if none is given it falls back to a
default MP2-fit auxbasis.

MPI parallelism
================

Both variants run under multiple MPI ranks:

.. code-block:: shell

   bagh nprocs input.inp

See :doc:`parallel_execution` for the launcher syntax and how to combine
``nprocs`` with ``einsum_threads`` on a single node. Every major cost
center is sharded: the DF 3-center integrals over auxiliary shells, the
THC grid evaluation and interpolation-point selection over grid points,
the ``M_ij`` build over its term list, and the ``K_g`` build over
Laplace-quadrature points -- the last of these has no data dependency
between quadrature points at all, so it is the most cleanly scalable
piece of the method. Without ``nprocs`` this reduces to exactly the
serial computation.

Example
=======

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
