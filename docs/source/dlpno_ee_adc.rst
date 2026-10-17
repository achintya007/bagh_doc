DLPNO-EE-ADC(3)
################

``DLPNO-EE-ADC(3)`` computes singlet excitation energies of closed-shell
molecules with the third-order algebraic diagrammatic construction, with the
singles held in **projected atomic orbital (PAO) domains** around
**localized occupied orbitals** and the doubles in the **pair natural orbital
(PNO)** space of the pair that owns them -- the construction of
:doc:`DLPNO-IP-ADC(3) <dlpno_adc>`, applied to the electron-excitation
operator. ``DLPNO-EE-ADC(2)`` and ``DLPNO-EE-ADC(2)-X`` run the same local
operator at second order.

The method is **state-specific**: every root drives its own orbital domains,
its own pair screen, its own PNO spaces and its own MP2/MP3 solved inside
them, so *n* roots are *n* local calculations rather than *n* eigenvalues of
one shared operator.

The reference must be closed-shell RHF.

Input
=====

.. code-block:: shell

   ! DLPNO-EE-ADC(3) cc-pvdz cc-pvdz-ri

   %cc
   nroots 4
   dlpno_thresh NORMALPNO
   end

   *xyz 0 1
   C   0.0000   0.0000  -0.5290
   O   0.0000   0.0000   0.6750
   H   0.0000   0.9376  -1.1120
   H   0.0000  -0.9376  -1.1120
   *

The second basis on the ``!`` line is the RI (density-fitting) basis; every
integral of the method is built from it. This input is
``test/DLPNO-EE-ADC3.inp`` and its output ``test/DLPNO-EE-ADC3.out``.

The composite
=============

A single truncated ADC(3) calculation carries several truncation errors at
once -- the domain of :math:`r_1`, the pair screen, the PNO cut -- and they
are removed by a ladder of cheaper calculations in nested spaces:

* **S0** -- the untruncated localized-occupied x PAO space. CIS and CIS(D) are
  solved here exactly.
* **S1** -- the level-0 pair list (screened on both the ground and the
  excited state) with per-orbital **DNO** (domain natural orbital) virtual
  spaces, ``tcut_dno``. CIS, CIS(D) and SOS-ADC(2) are solved here.
* **S2** -- the S1 pair list **rescreened** on the SOS-ADC(2) solution in S1,
  with the PNOs diagonalized *inside* S1. SOS-ADC(2) and ADC(3) are solved
  here.

The reported excitation energy is

.. math::

   \omega = \omega_{\text{ADC(3)}}[S2]
          + \underbrace{\omega_{\text{SOS-ADC(2)}}[S1] - \omega_{\text{SOS-ADC(2)}}[S2]}_{\delta_{\text{ADC(2)}}}
          + \underbrace{\omega_{\text{CIS(D)}}[S0] - \omega_{\text{CIS(D)}}[S1]}_{\delta_{\text{CIS(D)}}}
          + \underbrace{\omega_{\text{CIS}}[S0] - \omega_{\text{CIS}}[\text{domains}]}_{\delta_{\text{CIS}}}

SOS-ADC(2) is the SOS-ADC(2)-s of Krauter, Pernpointner and Dreuw (JCP 138,
044107 (2013)): :math:`c_T` on the opposite-spin :math:`t_2` in
:math:`M_{ss}`, :math:`c_c` on each singles-doubles coupling (``sos_ct``,
``sos_cc``).

How each root is built
----------------------

1. **CIS**, untruncated, by a symmetric block Davidson for ``3 * nroots``
   roots (convergence :math:`10^{-6}`); the lowest ``nroots`` are followed,
   each as its own state. CIS is printed once, since it does not depend on the
   root.
2. **Domains**: for each localized occupied orbital, the PAOs of the atoms
   chosen by differential overlap (``tcut_do``) together with the atoms the
   root's CIS particle sits on.
3. **Pair screen**: a pair is kept if either its MP2 pair energy passes
   ``tcut_pairs`` (Eh) or its CIS(D) pair contribution passes
   ``tcut_pairs_excited`` (eV). The energy dropped in each channel is printed.
4. **S1**: DNOs, LMP2, CIS in the domains, CIS(D), SOS-ADC(2).
5. **Rescreen** on the SOS-ADC(2) weights (``tcut_pairs_2``,
   ``tcut_pairs_excited_2``), PNOs inside S1 (``tcut_pno``), SOS-ADC(2).
6. **S2, third order**: LMP2, MP3 (:math:`t_2^{(2)}`, :math:`E^{(3)}`),
   :math:`t_1^{(2)}`, the third-order singles block dM3, and the ADC(3) root
   by a non-symmetric Davidson with overlap following on the guess.

The local operator
==================

The ADC(3) trial vector is ``[r1_i in D_i] ++ [r2^P in P's PNO basis]``; no
object with a full virtual index appears in the sigma. Two rules govern it:

* **A virtual index summed between an integral and an amplitude is carried in
  the pair that owns the amplitude.** The ring terms of the doubles-doubles
  block contract in the *source* pair and project only the free index into
  the target. Projecting the source amplitude into the target pair first
  truncates the summed index in a space with no claim on it, and that error
  does not fall with ``TCutPNO``.
* **The operator is non-symmetric.** The doubles metric is not the identity
  in a PNO basis; a symmetric Davidson converges confidently to the wrong
  root. Only the non-symmetric solver with overlap following is used.

dM3, the third-order singles block
----------------------------------

:math:`M_{ss}^{(3)} - M_{ss}^{(2)}` is static, built once per state from the
amplitudes, and is a first-class part of the operator (dropping it costs
0.1-0.4 eV on a root). It has 98 classes in 13 families, all built in the
local spaces, with no dense ``ovvv`` or ``vvvv`` anywhere:

* Every orbital-energy prefactor is rewritten through the amplitudes' own
  denominators, :math:`w_1 = D\,t_2^{(1)}` and :math:`w_2 = D\,t_2^{(2)}`, as
  a localized orbital has no orbital energy.
* Coulomb-type pieces use the global density fit; exchange-type pieces use
  the local MKN fitting domains (``tcut_mkn``, :math:`10^{-4}` for this
  method).
* Each IDLA result lives in PNO(I,L), except the oooo family, which uses the
  wider PNO' space at ``tcut_pno/10``, and the integral-carried classes, which
  are formed directly in the domain block :math:`D_I \times D_L`. The
  :math:`M_{vv}` folds are accumulated in the PAO basis of each domain.
* :math:`t_1^{(2)}` is held in its own domain :math:`D_k`.
* The vvvv family reads a half-transformed integral store
  :math:`(K|\mu\, u_q)` built together with the PNOs. It is kept in memory up
  to 1 GiB and otherwise written to the job's scratch directory.

Against the dense dM3 in the same spaces, all 98 classes together move the
root by at most 0.1 meV on formaldehyde and water clusters and by +0.24 /
+0.21 meV on the two NH\ :sub:`3`\ ...F\ :sub:`2` roots tested (charge-transfer
and local). The remaining error comes from holding the IDLA results in
PNO(I,L), and :math:`t_1^{(2)}` in :math:`D_k`.

Implementation
==============

Everything after the SCF is one call of the compiled job
``bagh_ee_cxx.run_ee_composite`` (``bagh_code/dlpno_ee_cxx``). Python builds
the localized orbitals, the PAOs and the three-index factors with pyscf,
hands them over once, and prints the log the job returns. CIS, the domains,
the pair screens, DNOs and PNOs, the pair integrals, LMP2/MP3, CIS(D),
SOS-ADC(2), the dM3 packing and kernels, and both Davidsons all run in C++.

On formaldehyde / cc-pVDZ, four roots, the whole job takes 20 s on a laptop
(25 s with the earlier Python-driven composite over the same kernels, which
gives the same numbers line for line).

Building the C++
----------------

The extension is part of the top-level CMake build (``compile_all``), or
standalone:

.. code-block:: shell

   cmake -S bagh_code/dlpno_ee_cxx -B build/dlpno_ee -DCMAKE_BUILD_TYPE=Release
   cmake --build build/dlpno_ee -j

This leaves ``bagh_code/dlpno_ee_cxx/bagh_ee_cxx.cpython-*.so`` where the
Python side expects it. Without it, asking for a DLPNO-EE method stops with
the build commands rather than a traceback.

Temporary files
---------------

Every temporary file of a BAGH job -- the dM3 integral store, Davidson
subspace files, scratch HDF5 files -- is registered and removed when the job
ends, also on an error. ``keep_temp true`` in ``%cc`` keeps them for
debugging. Checkpoint files (``.chk``) are never touched.

Keywords
========

All in the ``%cc`` block. ``dlpno_thresh``, ``tcut_pno``, ``tcut_pairs``,
``tcut_do`` and ``tcut_pao`` are shared with :doc:`DLPNO-IP-ADC <dlpno_adc>`
and described in :doc:`keyword`; the ones below are specific to this method.

.. code-block:: shell

   nroots                 4         # states; 3*nroots CIS roots are solved
   dlpno_thresh   NORMALPNO         # LOOSEPNO / NORMALPNO / TIGHTPNO
   tcut_pairs_excited  1e-4         # excited-state pair cut, eV
   tcut_dno            1e-7         # S1 DNO cut
   tcut_pairs_2  (tcut_pairs)       # S2 rescreen, ground-state channel, Eh
   tcut_pairs_excited_2 (tcut_pairs_excited)   # S2 rescreen, excited channel, eV
   tcut_mkn            1e-4         # dM3 MKN fitting domains
   sos_ct              1.3          # SOS-ADC(2)-s c_T
   sos_cc              1.17         # SOS-ADC(2)-s c_c
   adc_convergence     1e-6         # ADC Davidson convergence
   keep_temp          false         # keep the job's temporary files

For development and validation, three environment variables:

* ``BAGH_EE_COMPOSITE=python`` runs the composite step by step from Python
  over the same compiled kernels, instead of the single job.
* ``BAGH_EE_JOB_LEVEL=1|2`` stops the job after level S1 or S2.
* ``BAGH_DM3_PROFILE=1`` prints timings of the dM3 kernels.

Output
======

Per state, one line per step with its time, then a summary table:

.. code-block:: shell

   state        CIS     CIS(D)   SOS2[S1]   SOS2[S2] ADC(3)[S2]    dADC(2)    dCIS(D)       dCIS     ADC(3)
       1     4.5839     4.1845     4.0746     4.0733     3.9904       1.30       0.07      -0.00     3.9918
       2     9.9177     9.6643     9.4351     9.4350     9.4161       0.03       0.03      -0.00     9.4161
       3    10.2135    10.4344     9.9053     9.9085     9.5709      -3.21      -0.06      -0.00     9.5676
       4    10.4239     8.2264     8.3079     8.3080     9.1292      -0.08      -0.07       0.00     9.1291

Energies are in eV and the three corrections in meV; the last column is the
composite :math:`\omega`. States are numbered by the CIS root that seeded
them, so after correlation they need not be in ascending order.

Validity
========

* Closed-shell RHF references, singlet states only.
* State-specific: an *n*-root spectrum is *n* independent local calculations.
* The method is new; benchmark it against canonical EE-ADC(3) on your own
  system class before relying on sub-meV differences, especially for
  charge-transfer states, where the dM3 result-space error is largest.
