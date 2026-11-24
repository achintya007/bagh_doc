Fock-space coupled cluster and STEOM-CCSD for EE, DIP, DEA and TEA
####################################################################

Intermediate-Hamiltonian Fock-space coupled-cluster methods at the singles and
doubles level (IHFSCCSD) on a closed-shell CCSD reference, in the spinor basis
with Cholesky-decomposed integrals (``SOC-X2CAMF`` interface, ``CD True``).
Four sectors of the Fock space are available:

``EE-IHFSCCSD``
   (1,1) sector: excitation energies (1h1p).
``DIP-IHFSCCSD``
   (0,2) sector: double ionization potentials (2h).
``DEA-IHFSCCSD``
   (2,0) sector: double electron attachment (2p).
``TEA-IHFSCCSD``
   (3,0) sector: triple electron attachment (3p), e.g. p\ :sup:`3` atoms and
   triradicals from the closed-shell system with three electrons fewer.
``TEA-EOM-CCSD``
   Hbar in the 3p space alone (no dressing), for comparison.
``EE-STEOM-CCSD``, ``DIP-STEOM-CCSD``, ``DEA-STEOM-CCSD``, ``TEA-STEOM-CCSD``
   Similarity-transformed EOM-CCSD in the same four sectors, built from the
   same EA/IP-EOM states of the active spinors (see below).

The (1,1), (2,0) and (0,2) sectors follow Meissner's scheme as implemented with
spin-orbit coupling by Lu, Wang and Wang; the (3,0) sector follows the new
scheme of Hu, Wang, Wang and Meissner. In all of them only the (1,0) and
(0,1) amplitudes are needed. They are taken from the EA- and IP-EOM-CCSD
vectors of the states of the active spinors, because FSCC(1,0) = EOM-EA-CCSD
and FSCC(0,1) = EOM-IP-CCSD. The higher-sector amplitudes S(1,1), S(2,0),
S(0,2) and S(3,0) never appear. The code is in ``bagh_code/fscc/``
(``ihfscc_sd.py``, ``tea_ihfscc.py``).

Theory
======

With :math:`\bar H = e^{-T}He^{T} - E_\mathrm{CC}`, the energies are the
eigenvalues of an intermediate Hamiltonian in the whole single-excitation
space :math:`P_1` of the sector (all 1h1p, 2h, 2p or 3p determinants). The
model space :math:`P` holds the determinants whose quasi-particles are all
active, and :math:`Q = 1 - P_1`.

**EE, DIP, DEA** (Meissner):

.. math::

   H_\mathrm{int} = P_1 \bar H P_1 + P_1 \bar H\, Q\, \{e^{S^{(1,0)} + S^{(0,1)}}\} P .

The dressing :math:`\bar H Y` acts on the model space only, so it is an
explicit :math:`P_1 \times P` matrix.

**TEA** (new (3,0) scheme):

.. math::

   H_\mathrm{int} = P_1 \bar H \{e^{S_2^{(1,0)}}\} P_1 ,

where only the two-body (1,0) amplitudes enter and the dressing acts on all
3p determinants. This intermediate Hamiltonian is the same as that of
STEOM-CCSD.

**STEOM-CCSD**: the dressing is built differently. There are no one-body
:math:`S` amplitudes, only :math:`S_2^{(1,0)}` and :math:`S_2^{(0,1)}`, and
the dressing acts on the full :math:`P_1` vector rather than on its
model-space part:

.. math::

   G = P_1 \bar H \{e^{S_2^{(1,0)} + S_2^{(0,1)}}\} P_1 .

Because :math:`S` raises the excitation rank, :math:`P_1\{e^{-S}\} = P_1`,
so :math:`G` is the full second similarity transformation projected on
:math:`P_1`. :math:`S_2` contracts only with active labels; on a determinant
with one active quasi-particle the other is a spectator. For EE, DIP and DEA
the dressing is the IHFSCC one with the genuine one-body amplitudes set to
zero and the spectator labels running over all spinors. For TEA the (3,0)
scheme above already has this form, so ``TEA-STEOM-CCSD`` and
``TEA-IHFSCCSD`` are the same calculation.

The (1,0) and (0,1) amplitudes come from the EOM right vectors of the states
assigned to the active spinors (:math:`(R_P)_{\alpha N} = r_1^N[\alpha]`):

.. math::

   s^c_A = \sum_N r_1^N[c]\,(R_P^{-1})_{NA}, \qquad
   s^{cd}_{Ak} = \sum_N r_2^N[k,c,d]\,(R_P^{-1})_{NA},

   s^I_k = -\sum_N r_1^N[k]\,(R_P^{-1})_{NI}, \qquad
   s^{Ic}_{kl} = \sum_N r_2^N[k,l,c]\,(R_P^{-1})_{NI},

with bagh's EOM conventions (EA :math:`r_2[k,a,b] \leftrightarrow a^+b^+k|0\rangle`,
IP :math:`r_2[i,j,a] \leftrightarrow -a^+ji|0\rangle`). The extensions
:math:`s^B_A = \delta_{AB}` and :math:`s^J_I = -\delta_{IJ}` come out of these
formulas automatically. ``r1%`` (single-excitation weight of each EOM state),
the weight of the active spinors and the condition number of :math:`R_P` are
printed, because states with sizeable doubles character spoil the results.

Working equations
=================

The dressings follow the appendices of the two papers, re-derived for spinors
and checked term by term against a Fock-space brute force. Exact Hbar
elements are built from t1-dressed Cholesky vectors, e.g.

.. code-block:: text

   L~_ae = L_ae - t_ma L_me
   W[ab,ef] = sum_L (L~_ae L~_bf - L~_af L~_be) + 1/2 t_mnab <mn||ef>
   W[cm,ef] = sum_L (L~_ce L_mf - L~_cf L_me)

Note that the vvvv element of Hbar has :math:`\tfrac12\tau\langle mn||ef\rangle`,
not the :math:`\tfrac14\tau` of the CCSD-equation intermediate.

Three corrections to the printed equations were needed:

* (3,0), Eq. A.5 of Hu et al.: the first term of :math:`I_{abEf}` carries no
  :math:`P(EF)`, because :math:`f` is not an active index.
* (1,1), Eq. A11 of Lu et al.: the dressing enters with an overall minus sign
  when the model determinants are :math:`a_A^+a_I|0\rangle`, like the rows. The
  equation as printed corresponds to the order :math:`a_I a_A^+|0\rangle`.
* (1,1), Eq. A11: :math:`S^{Ac}_{mn}` should read :math:`S^{Ic}_{mn}`.

Algorithm and cost
==================

#. CCSD (``ccsd``, Cholesky).
#. EA-EOM-CCSD for the active virtuals (EE, DEA, TEA) and/or IP-EOM-CCSD for
   the active occupied spinors (EE, DIP), with ``eom_SO``. One Koopmans guess
   is followed per active spinor by overlap, and the residuals are checked.
   If an active state is fragmented over 2p1h/2h1p satellites, the lowest
   roots are solved instead and assigned by r1 weight.
#. Dressing intermediates, computed once.

   * EE: an :math:`(o\,v) \times (A_o A_v)` matrix.
   * DIP / DEA: :math:`O(A^2 o v^3)` at most.
   * TEA: the :math:`I_{abAf}` intermediate, :math:`O(A\,o\,v^4)`, stored as
     :math:`A v^3` numbers.

#. Davidson in :math:`P_1`. The guesses are the leading Schur vectors of the
   exact model block :math:`P H_\mathrm{int} P`. They are orthonormal and span
   the invariant subspace of its lowest eigenvalues, so no member of a
   degenerate multiplet is lost (eigenvectors of a non-Hermitian matrix can
   come out nearly parallel for a degenerate eigenvalue). A degenerate cluster
   cut by the requested number of roots is taken whole. In each iteration the
   lowest Ritz values with a model-space weight of at least ``fs_pick_weight``
   are followed; overlap with the guesses only fills the rest. This keeps
   roots of the wrong character out of the result.
   ``nroots + fs_extra_roots`` roots are solved and the lowest ``nroots``
   reported. Cost per matvec:

   * DEA: :math:`O(L v^3)`.
   * DIP: :math:`O(o^4)`.
   * EE: :math:`O(L o v^2)`.
   * TEA: :math:`O(L v^4)` (particle-particle ladder with dressed Cholesky
     vectors, in blocks); TEA vectors are packed :math:`a<b<c`.
   * STEOM: :math:`G` is built once from dressed intermediates, each the bare
     :math:`\bar H` block plus the :math:`S_2` dressing on its active rows or
     columns, e.g. :math:`G_{ik} = \bar F_{ik} + \delta_{i\in\mathrm{act}}
     I_{ik}`. EE stores :math:`G_{oo}`, :math:`G_{vv}` and :math:`G_{ovvo}`
     (:math:`o^2v^2`), DIP :math:`G_{oo}` and :math:`G_{oooo}`; their matvecs
     are plain contractions. DEA stores the dressed :math:`G_{vv}` and applies
     the two-body dressing to the vector. Nothing larger than :math:`o^2v^2`
     is stored.

For each root the output gives the energy (a.u., eV), the energy relative to
the lowest root (DIP, DEA, TEA), for TEA also :math:`\mathrm{TIP} = -E`, the
weight of the model space (``active%``, the paper's measure of an adequate
active space) and the dominant determinants. Energies and the active lists
are written to ``<inp>.fscc11/20/02.hdf5`` and ``<inp>.tea.hdf5``.

Input
=====

The orbital energies of the active spinors are printed, and a warning is
given when an inactive spinor lies within 10\ :sup:`-3` a.u. of an active
one: such an active space cuts through a shell and splits the multiplets
artificially. The ordering of the virtual shells depends on the basis (in
cc-pVDZ the 3p spinors of N\ :sup:`3+` lie below 3s), so check it, and use
``fs_act`` / ``fs_acto`` to pick complete shells. Choose complete shells and
keep Kramers partners together, e.g.
np\ :sub:`1/2` + np\ :sub:`3/2` = 6 spinors. An active space that cuts through
a degenerate set breaks the symmetry of the results. Spin-orbit splittings can
need a larger active space than scalar-relativistic results do.

Double ionization potentials of Kr (4p\ :sup:`-2`):

.. code-block:: shell

   ! SOC-X2CAMF DIP-IHFSCCSD spinor aug-cc-pvtz

   %cc
   CD True
   cd_threshold 1e-5
   fc True
   nroots 15
   fs_nacto 6
   end

   *xyz 0 1
   Kr 0.0 0.0 0.0

Excitation energies of Zn (4s → 4p): 2 active occupied and 6 active virtual
spinors:

.. code-block:: shell

   ! SOC-X2CAMF EE-IHFSCCSD spinor aug-cc-pvtz

   %cc
   CD True
   fc True
   nroots 12
   fs_nacto 2
   fs_nactv 6
   end

   *xyz 0 1
   Zn 0.0 0.0 0.0

Double electron attachment, the C atom from C\ :sup:`2+`
(\ :sup:`3`\ P, :sup:`1`\ D, :sup:`1`\ S):

.. code-block:: shell

   ! SOC-X2CAMF DEA-IHFSCCSD spinor aug-cc-pvtz

   %cc
   CD True
   nroots 15
   fs_nactv 6
   end

   *xyz 2 1
   C 0.0 0.0 0.0

Triple electron attachment, the N atom (2s\ :sup:`2`\ 2p\ :sup:`3`) from the
closed-shell N\ :sup:`3+` reference:

.. code-block:: shell

   ! SOC-X2CAMF TEA-IHFSCCSD spinor aug-cc-pvtz

   %cc
   CD True
   cd_threshold 1e-5
   nroots 8
   fs_nactv 6
   eom_convergence 1e-7
   end

   *xyz 3 1
   N 0.0 0.0 0.0

The usual ``nroots``, ``eom_convergence``, ``eomconv_r``, ``max_cycle``,
``max_space`` and ``max_memory`` keywords apply. The Fock-space keywords are
listed below and in the keyword reference.

.. list-table::
   :header-rows: 1
   :widths: 20 60 20

   * - keyword
     - meaning
     - default
   * - ``fs_nactv``
     - number of active virtual spinors (the lowest ones)
     - 6
   * - ``fs_act``
     - explicit active virtual spinors: comma list of MO indices as printed
       by EA-EOM-CCSD (``nocc + a``, 0-based)
     - --
   * - ``fs_nacto``
     - number of active occupied spinors (the highest ones)
     - 2
   * - ``fs_acto``
     - explicit active occupied spinors: comma list of occupied MO indices
       (0-based, correlated numbering)
     - --
   * - ``fs_ea_extra``
     - extra EOM roots in the fallback for fragmented EA/IP states
     - 2·(number of active spinors)
   * - ``fs_extra_roots``
     - extra roots solved for, of which the lowest ``nroots`` are reported
     - 4
   * - ``fs_pick_weight``
     - smallest model-space weight of a root followed in the Davidson
     - 0.2

Validation
==========

``test/tea_ihfscc`` and ``test/ihfscc_sd`` in the bagh repository hold
stand-alone checks based on pyscf generalised-HF spin orbitals:

* **Fock-space brute force.** Hbar is built from creation and annihilation
  matrices, the EA/IP-EOM states from it, and the normal-ordered exponential
  on the model determinants. The intermediate Hamiltonians of all four sectors
  agree with it to :math:`10^{-15}`, with real and with random complex orbital
  phases.
* **Amplitudes and roots.** The amplitudes obtained from the EOM vectors in
  bagh's conventions agree with the brute-force Bloch amplitudes to
  :math:`10^{-8}`. The Davidson roots agree with the exact eigenvalues of
  :math:`H_\mathrm{int}` to :math:`10^{-10}` and come out as complete
  multiplets.
* **Comparison with the paper.** N atom from N\ :sup:`3+` (aug-cc-pVTZ,
  non-relativistic, reference orbitals):

  * TEA-IHFSCCSD: TIP 90.95 eV, :sup:`2`\ D at +2.31 eV (paper, aug-cc-pVQZ:
    91.24 and 2.27 eV; experiment 91.58 and 2.38 eV).
  * TEA-EOM-CCSD: TIP 85.48 eV (paper: 85.46 eV).

* **STEOM.** :math:`G` of all three sectors (EE, DIP, DEA) agrees with
  :math:`P_1 \bar H \{e^{S_2}\} P_1` from the Fock-space brute force to
  :math:`10^{-14}`, real and with complex phases (``test/ihfscc_sd/test_steom.py``).
  With every spinor active, STEOM and IHFSCC coincide (to :math:`10^{-15}`),
  as they must.
* **Unitary invariance.** The (3,0) energies are unchanged under unitary
  rotations inside degenerate shells (``test/tea_ihfscc/test_unitary.py``),
  as returned by a spinor SCF.
* **Inside bagh** (``SOC-X2CAMF``, aug-cc-pVQZ, all electrons correlated,
  active space np + (n+1)s): TEA-EOM-CCSD and TEA-IHFSCCSD for N, O\ :sup:`+`,
  F\ :sup:`2+`, Ne\ :sup:`3+`, P, S\ :sup:`+`, Cl\ :sup:`2+` and
  Ar\ :sup:`3+` reproduce Tables 1 and 2 of Hu et al. All :sup:`2`\ D and
  :sup:`2`\ P excitation energies (J-averaged) agree within 0.04 eV. The TIPs
  agree within 0.02 eV for N and O\ :sup:`+`, and the difference grows with the
  nuclear charge to 0.2 eV for Ar\ :sup:`3+` for both methods, the scalar
  relativistic effect that the non-relativistic reference results do not have.
  The TEA-IHFSCCSD TIP of P is 0.26 eV higher than the paper's, which used
  d-aug-cc-pVQZ for N and P.

STEOM-CCSD versus IHFSCCSD
--------------------------

Both methods use the same EA/IP-EOM states of the active spinors and the same
:math:`S_2` amplitudes; they differ in the dressing. IHFSCCSD dresses only the
model determinants (all quasi-particles active) and keeps the one-body
:math:`S` amplitudes; STEOM-CCSD has no one-body :math:`S` and dresses every
:math:`P_1` determinant with at least one active label. With every spinor
active the two are the same method. Results inside bagh (``SOC-X2CAMF``,
cc-pVDZ, Cholesky; the inputs are in ``test/steom_vs_ihfscc`` of the bagh
repository), energies in eV, degeneracies in parentheses:

.. list-table::
   :header-rows: 1
   :widths: 22 39 39

   * - system (active spinors)
     - IHFSCCSD
     - STEOM-CCSD
   * - H\ :sub:`2`\ O, EE (4 occupied, 4 virtual)
     - 7.4037 (3), 8.0941, 9.7169 (3), 9.8213
     - 7.4974 (3), 8.1477, 9.8416 (3), 9.9128
   * - C from C\ :sup:`2+`, DEA, :math:`E(N{+}2)-E_\mathrm{CC}` (2p)
     - -35.1313, -35.1293 (3), -35.1254 (5), -33.7346 (5), -32.2878
     - -35.1356, -35.1337 (3), -35.1298 (5), -33.7581 (5), -32.3534
   * - Ne\ :sup:`2+` from Ne, DIP (2p)
     - 61.3352 (5), 61.4085 (3), 61.4433, 64.9315 (5), 69.0485
     - identical

* **EE (H**\ :sub:`2`\ **O):** STEOM lies 0.05--0.12 eV above IHFSCC. The
  excitations from the active occupied spinors into inactive virtuals (and
  from inactive occupieds into active virtuals) are dressed in STEOM and not
  in IHFSCC.
* **DEA (C):** STEOM is lower by 4--65 meV; the one-body amplitudes, which mix
  the active 2p with inactive p spinors, are absent in STEOM.
* **DIP (Ne):** identical, by symmetry. The determinants STEOM dresses beyond
  the model space (2s\ :sup:`-1`\ 2p\ :sup:`-1`, 1s\ :sup:`-1`\ 2p\ :sup:`-1`)
  have the opposite parity to the 2p\ :sup:`-2` states, and the IHFSCC
  one-body amplitudes vanish because 2p does not mix with 2s or 1s.

For TEA the (3,0) scheme already has the STEOM form, so TEA-IHFSCCSD and
TEA-STEOM-CCSD give the same energies.

References
==========

* Y. Lu, Z. Wang, F. Wang, *J. Chem. Phys.* **156**, 114111 (2022) --
  IHFSCCSD for EE, DIP and DEA with spin-orbit coupling.
* Y. Hu, Z. Wang, F. Wang, L. Meissner, *J. Phys. Chem. A* **128**, 8279
  (2024) -- the new (3,0) IHFSCC scheme for triple electron attachment.
* L. Meissner, *J. Chem. Phys.* **108**, 9227 (1998) -- the intermediate
  Hamiltonian Fock-space CC scheme.
* M. Nooijen, R. J. Bartlett, *J. Chem. Phys.* **106**, 6441 (1997) --
  similarity-transformed equation-of-motion coupled cluster (STEOM-CCSD).
