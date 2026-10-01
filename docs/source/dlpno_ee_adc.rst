DLPNO-EE-ADC(3)
################

``DLPNO-EE-ADC(3)`` computes singlet excited-state energies in a basis of
**pair natural orbitals** built on **projected atomic orbital domains**
around **localized occupied orbitals**, the same construction as
:doc:`DLPNO-IP-ADC(3) <dlpno_adc>` applied to the electron-excitation (EE)
operator. It is **state-specific**: each root drives its own CIS vector,
its own orbital domains, its own pair screen, its own PNO spaces, and its
own MP2 solved inside them, so *n* roots are *n* separate local
calculations rather than *n* eigenvalues of one operator.

The reference must be closed-shell RHF.

.. note::

   This is a research implementation. It is driven today through the
   Python classes below (``bagh_code/dlpno/reference_ee.py``,
   ``adc3_local.py``, ``domain_operator.py``) and the scripts under
   ``test/dlpno_ee/``, not yet through a ``!`` input line the way
   ``DLPNO-IP-ADC(3)`` is. See `What is not yet done`_.

Theory
======

Beyond CIS the matrix has the usual singles/doubles block structure,

.. math::

   \sigma_1[i,a] = \sum_e M_{vv}[a,e]\, r_1[i,e] - \sum_m M_{oo}[m,i]\, r_1[m,a]
                 + \sum_{jb} M_{ijab}[ia,jb]\, r_1[j,b]
                 + \text{(sd coupling)},

.. math::

   \sigma_2^{ij}[a,b] = (\epsilon_a+\epsilon_b)\, r_2^{ij}[a,b]
                      - \sum_m F_{mj}\, S\, r_2^{im}\, S^\mathsf{T}
                      + \tfrac12\sum_{ef}(ae|bf)\, r_2^{ij}[e,f]
                      + \tfrac12\sum_{mn}(mi|nj)\, S\, r_2^{mn}\, S^\mathsf{T}
                      + \text{ring A} + \text{ring B} + \text{(ds coupling)},

but every doubles index :math:`(i,j,a,b)` is confined to the PNO space of
the pair :math:`P=(i,j)` that owns it, of which there are typically a few
dozen functions rather than the full virtual space.

The routing rule
-----------------

The same rule that governs the IP method governs this one, and the
evidence for it is independent:

  **A virtual index summed between an integral and an amplitude must be
  carried in the pair that owns the amplitude.**

In the doubles–doubles block, the **ladder** and **oooo** terms sum no
virtual against an integral and are exact once the source amplitude is
projected into the target pair wholesale. The **ring A / ring B** terms
are different: they sum a virtual between an integral and *another
pair's* amplitude. That contraction is done in the **source** pair, where
the index is owned, and only the free index is projected into the target.
Projecting the source amplitude into the target pair first -- the obvious
way to write it -- truncates the summed index in a space with no claim on
it, and the resulting error does not fall with ``TCutPNO``. A
``route_through_target`` switch is kept in ``reference_ee`` precisely so
this cost can be measured rather than only asserted.

A second, unrelated consequence of locality: with the doubles metric no
longer the identity in a PNO basis, the operator is **non-symmetric**. A
symmetric Davidson solver converges confidently to the wrong root (observed
12.18 eV instead of 12.85 eV on a test system). The non-symmetric solver
with overlap-following on the CIS guess is therefore mandatory, not a
tuning choice.

The singles–singles block
==========================

The singles–singles sigma splits as **bare + dressing**. The bare half
carries no amplitude and is exact at any PNO truncation, since it is a CIS
sigma formed straight from the density-fitting factors with the virtual
confined to the appropriate orbital domain, :math:`D_i`. The dressing half
carries :math:`t_2`, which belongs to a pair, so :math:`M_{vv}` is built
**directly into each domain** -- no dense :math:`(n_{vir},n_{vir})` matrix
ever appears -- and :math:`M_{oo}` turns out to have no free virtual index
at all, which is why it measures as *exactly* zero PNO-rule error at every
threshold tested, structurally rather than by chance.

Term by term, moved into the PNO basis one at a time on a water dimer in
cc-pVDZ, at ``TCutPNO`` :math:`10^{-5}` (the worst case found):

.. code-block:: shell

   term     ADC(2)   ADC(3)  ratio   resid after ADC(2) correction
   MOO      -0.000   -0.000    -       0.000
   MVV      -1.248   -1.214   0.97     0.034
   B12       0.180    0.123   0.68    -0.057
   B34      -0.405   -0.394   0.97     0.012
   B56       0.180    0.123   0.69    -0.056
   B78      -0.405   -0.394   0.97     0.011
   all six  -1.797   -1.840   1.02    -0.043          (meV)

The dominant single-block error is **the domain restriction of** :math:`r_1`
itself, not the PNO rule: :math:`(1-P_{D_i})\,r_1[i,:]` enters the bare
terms too, so even the CIS limit stops being exact once domains are
incomplete. It costs nothing where the domains are complete, 13 meV on a
water trimer at ``TCutDO`` :math:`10^{-2}`, and 57 meV on the dimer at
``TCutDO`` :math:`5\times10^{-2}`.

The third order, and dM3
=========================

The complete third order (``pairwise3_all``) is pair-local and exact at
zero truncation to :math:`2\times10^{-15}`/:math:`5\times10^{-16}` in
:math:`\sigma_1`/:math:`\sigma_2`. The only two points that ever touched
the full virtual space -- :math:`r_1` coming in and :math:`\sigma_1` going
out -- are now routed through the orbital domains as well, so the whole
block runs with no object of shape :math:`(n_{vir},\ldots)` in it.

``dM3`` -- the third-order singles-singles block, :math:`M_{ss}^{(3)} -
M_{ss}^{(2)}` -- is static (built once from the amplitudes) and is a
first-class part of the operator: dropping it entirely costs 124-378 meV
on a root, far more than any PNO or domain truncation. Three results from
this part of the development are worth recording because they would
otherwise be easy to repeat:

* Of 423 statements, 88 carried bare orbital energies
  (:math:`\epsilon_{\text{core}}`/:math:`\epsilon_{\text{extern}}`), which
  is fatal in a local basis -- a localized occupied orbital has no orbital
  energy. Relabelling the summed indices merges those 88 into 16 groups
  whose energy prefactors are exactly combinations of the two amplitudes'
  own denominators, :math:`D(t_{ijab}) = \epsilon_a+\epsilon_b-\epsilon_i-
  \epsilon_j`, which are already available as :math:`w_1 = D\cdot t_2^{(1)}
  = -(ia|jb)` and :math:`w_2 = D\cdot t_2^{(2)} = R_2` (the second-order
  residual). The rewrite, 25 statements for the original 88, agrees with
  the orbital-energy form to :math:`6.98\times10^{-16}`.
* A transcription bug -- never previously exposed, since the module had
  never been run against an oracle -- made an early version 2.2x the
  correct norm. The cause: pyscf's ``eris.vvvv``, reshaped to four indices
  the way this code reshapes it, holds :math:`(ac|bd)` at index
  :math:`[a,b,c,d]`, not :math:`(ab|cd)`. Every *other* integral block is
  index-for-index, which is exactly why this one layout difference hid.
  With the correct layout, all independent transcriptions reproduce
  pyscf's ``get_imds(adc3) - get_imds(adc2)`` to :math:`3.8\times10^{-12}`.
* dM3 built entirely from BAGH's own local amplitudes and integrals (no
  pyscf call) agrees with the two-canonical-call route to
  :math:`3.9\times10^{-12}` at zero truncation, and projecting it onto the
  PAO domains -- rather than dropping it or routing it pair by pair, which
  buys nothing further -- costs only 1.0-1.1 meV even at a leak of 61% of
  the virtual space. dM3 needs roughly 1% accuracy, and a domain-blocked
  form delivers it at the cost of one matrix build, done once because dM3
  is static.

The wired operator
===================

``domain_operator.DomainEEADC`` assembles every piece above into one
operator whose trial vector is ``[r1_i in D_i] ++ [r2^P in P's PNO
basis]`` -- no object carrying a full virtual index appears anywhere in
the sigma. The order of operations follows ``reference_ee.matvec`` and
``LocalADC3.matvec`` exactly, which matters: the input doubles are
symmetrized onto the physical sector *first*, the order :math:`\le 2.5`
doubles result is symmetrized as :math:`\sigma_2[p] + \sigma_2[\text{twin}]^\mathsf{T}`,
and the third-order doubles are added *after* that.

Against the full-space operator, formaldehyde / cc-pVDZ:

.. code-block:: shell

   TCutPNO   TCutDO    dom      dim |  full-space   domain | shift   +bagh dM3
   1.0e-06  1.0e-02   30.0    20010 |     3.97425  3.97443 | +0.18     +1.17
   1.0e-06  5.0e-02   28.1    19809 |     3.97348  3.97388 | +0.41     +0.96
   3.3e-07  1.0e-02   30.0    24764 |     3.97182  3.97189 | +0.07     +1.07   (meV)

The shift tightens with ``TCutPNO``, as a genuine truncation error should.
The last column, the extra cost of the self-contained dM3 over the older
pyscf-built one, is nearly threshold-independent -- it comes from the pair
list rather than the PNO spaces -- and vanishes entirely at zero
truncation.

The compiled kernel
=====================

``bagh_code/dlpno_ee_cxx/`` carries the ADC(2) sigma, the full
doubles–doubles block (ladder, oooo, both rings), all eleven third-order
groups, and an out-of-core non-symmetric Davidson (subspace vectors spilled
to a temporary file via ``pread``/``pwrite`` rather than held densely).
Verified against ``LocalADC3.matvec`` to :math:`2.4\times10^{-13}`; the
compiled Davidson reproduces the Python root exactly. Measured speed-up on
an idle box: ADC(2) 4.0x, third order 5.4x.

The gap: ``Context`` still takes ``M_oo``, ``M_vv``, ``M_ijab`` and
``dM3`` as dense arrays handed in from Python, so none of the
domain/PNO singles-singles work described above is in the compiled kernel
yet -- it runs today with the singles-singles block full-space in C++ and
everything else domain-restricted in Python.

Reference numbers
===================

Formaldehyde / aug-cc-pVDZ, state-specific, four roots, ``TCutPNO``
:math:`3.33\times10^{-7}` (about 26% of the full doubles space):

.. code-block:: shell

   root   canonical   local      err
      0     3.9642    3.9653   +1.05 meV
      1     7.5396    7.5403   +0.72
      2     8.4204    8.4229   +2.53
      3     8.5681    8.5711   +3.00

Example
=========

There is no ``!`` input keyword yet; the method is used directly from
Python, following ``test/dlpno_ee/study_domain_operator.py``:

.. code-block:: python

   from pyscf import gto, scf
   import reference_ee
   from domain_operator import DomainEEADC

   mol = gto.M(atom="C 0 0 0; O 0 0 1.208; H 0 0.943 -0.588; H 0 -0.943 -0.588",
               basis="aug-cc-pvdz", verbose=0)
   mf = scf.RHF(mol).run()

   ref = reference_ee.DLPNOEEADCReference(
       mol, order=3, root=0, tcut_do=1e-2, tcut_do_particle=1e-2,
       f_keep_cisd=1e-3, tcut_pno=3.33e-7)
   ref.build_orbitals(mf); ref.build_integrals_3c(); ref.solve_cis()
   ref.build_domains(); ref.screen_pairs(); ref.build_pnos()
   ref.build_pair_integrals(); ref.iterate_lmp2(); ref.build_t2_2()
   ref.build_imds()

   omega = DomainEEADC(ref, mf, order=3).solve()

``root`` selects which CIS vector seeds the domains/pairs/PNOs for that
calculation; repeat the block once per root for a spectrum.

What is not yet done
=======================

* **The last dense step**: ``dm3_local.dense`` still forms the ``vvvv``
  contribution to dM3 densely from the DF factors; routing it through the
  factors is the one remaining full-space step in the whole method.
* **The C++ kernel's domain gap** above -- ``EEPairs``, ``sigma2``,
  ``sigma3``, ``Context`` and ``davidson`` all still need the
  domain-sized singles.
* **No input-file dispatch.** Unlike ``DLPNO-IP-ADC(3)``, there is no ``!``
  keyword or ``run_rhf`` entry point yet; every run goes through the
  Python classes directly.
* **The composite ADC(2) bracket is unresolved.** As an estimator of the
  *PNO rule* it removes about 98% of the error (ratio 0.94-1.02); as an
  estimator of the *domain restriction* it is partial (ratio 0.80). As a
  correction on the bare truncated root it has carried the wrong sign in
  every test so far and made the result worse -- the decisive test
  (rerunning the comparison with the pair-local third order switched off)
  has not yet been run. Treat ``dlpno_sos``-style corrections for this
  method as experimental until that is settled.

Validity
==========

* Closed-shell RHF references only.
* State-specific: a four-root spectrum is four independent local
  calculations, each with its own domains, pairs and PNOs, not four
  eigenvalues of a shared operator.
* The doubles metric is not the identity in a PNO basis, so the operator
  is non-symmetric; a symmetric Davidson will converge to the wrong root
  with no warning. Only the non-symmetric solver with overlap-following
  is supported.
