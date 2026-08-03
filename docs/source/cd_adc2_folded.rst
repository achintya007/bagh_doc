CD-EE-ADC(2)-FOLDED: Folded, Doubles-Free Relativistic EE-ADC(2)
###################################################################

``CD-EE-ADC(2)-FOLDED`` computes the same vertical excitation energies as
``EE-ADC(2)`` for SOC-X2CAMF (X2CAMF/X2CMP two-component spinor) references
under Cholesky decomposition, but the iterative eigenproblem is solved
entirely in the particle-hole (singles) space -- the 2p2h doubles block is
never stored, and no Davidson iterations run over it. This is not an
approximation to ADC(2) itself (see :ref:`cd-adc2-folded-validity` below for
the one place a limitation shows up); it is a different, much cheaper way of
solving the same secular equation, implemented in
``bagh_code/adc_rel/CD_adc2_folded_rel.py``.

Theory
======

At strict ADC(2) the 2p2h/2p2h block of the ADC secular matrix is diagonal
in the canonical spinor basis,

.. code-block:: text

   M_DD = D_{ijab} = e_a + e_b - e_i - e_j ,

so the full eigenvalue problem

.. code-block:: text

   [ M_SS  M_SD ] [ R1 ]        [ R1 ]
   [ M_DS  D    ] [ R2 ]  = w   [ R2 ]

can be folded *exactly* onto the singles space:

.. code-block:: text

   R2(w) = (w - D)^-1 M_DS R1
   [ M_SS + M_SD (w - D)^-1 M_DS ] R1 = w R1

Solving this reproduces the eigenvalues of the *full* ADC(2) matrix exactly
-- a re-partitioning, not an approximation (verified in
``tests/test_adc2_folded.py`` against an explicit dense singles+doubles
build). The doubles vector ``R2`` is never part of the trial space: it is
generated inside the sigma build, divided by ``(w - D)``, immediately
contracted back down to the singles space, and discarded, batched over the
first occupied index (``%cc occ_batch``) so its peak memory footprint is
``O(n_batch * n_o * n_v^2)`` rather than ``O(n_o^2 n_v^2)``.

Only the 0-, 1- and 2-external antisymmetrised integral blocks
(``<oo||oo>`` unused, ``<oo||ov>``, ``<oo||vv>``, ``<ov||ov>``) are ever
built, directly from the same Cholesky vectors (``eris.LOO`` /
``eris.LOV`` / ``eris.LVV``) that every other CD method in BAGH already
uses. The 3-external block is contracted on the fly from those Cholesky
vectors inside the sigma build; the 4-external block never appears in
ADC(2) at all.

Because the calculation is over complex spinors rather than real orbitals,
a contraction is only basis-invariant when each summed index appears once
conjugated and once not -- three places in the working equations (the
virtual intermediate ``I1``, the occupied intermediate ``I2``, and the
coupling blocks ``M_DS``/``M_SD``) need the conjugate on a specific side to
get this right; see the module docstring and the docstrings of
``FoldedADC2._make_intermediates`` / ``FoldedADC2._sigma_folded`` in
``CD_adc2_folded_rel.py`` for exactly which. Getting one of these wrong
leaves the MP2 correlation energy and the CIS excitation energies exactly
right while making the ADC(2) excitation energies depend on the spinor
basis -- e.g. differing between a GHF-derived basis and its j-adapted
two-component counterpart, or splitting an exactly degenerate Kramers pair.

.. _cd-adc2-folded-validity:

Validity
========

The exact folding step requires strict ADC(2) (a diagonal 2p2h/2p2h block)
-- this is why the method has no ``-X`` (extended) or third-order
counterpart; ``ADC(2)-X`` has a first-order, non-diagonal 2p2h/2p2h block,
so the closed-form elimination above does not apply. (The non-relativistic
THC-LT-EE-ADC(2) module, :doc:`thc_lt_adc`, makes the same point for its
own doubles-free fold.) A root above the lowest (CVS-restricted) 2p2h
energy sits near a pole of the resolvent; the driver prints a warning
rather than failing in that case.

Transition dipole moments are supported (see
:ref:`cd-adc2-folded-tdm` below); excited-state dipole moments (EXDM) are
not. Only two-component references are supported; 4c-DC needs BAGH's own 4c
CD path. IP/EA are not covered by this module (EE only).

Keywords
========

Reused as-is (no new keyword needed beyond ``occ_batch``): ``nroots``,
``adc_convergence``, ``fc``/``fc_no`` (frozen core), ``CD_Threshold``,
``DoCVS``/``CVSMIN``/``CVSMAX`` (core-valence separation, same convention as
``DIP-ADC(3)``; in the folded scheme CVS is a projector applied to the
singles vector and to the transient doubles intermediate).

**occ_batch** ``Integer``

Occupied indices handled per pass when building the transient doubles
intermediate; the memory/speed dial for the folded sigma build. Default is
all occupied indices in a single pass.

.. code-block:: shell

   occ_batch 4

Example
=======

.. code-block:: shell

   ! CD-EE-ADC(2)-FOLDED soc-x2camf spinor unc-ccpvdz

   %cc
   cd True
   nroots 4
   fc True
   fc_no 2
   cd_threshold 1e-4
   occ_batch 4
   adc_convergence 1e-06
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

| ``CD-EE-ADC(2)-FOLDED``: name of the method.
| ``soc-x2camf``: two-component X2CAMF interface.
| ``spinor``: spinor basis.
| ``unc-ccpvdz``: name of the basis set (uncontracted cc-pVDZ).
| ``cd True``: enable Cholesky decomposition of the two-electron integrals.
| ``fc True`` / ``fc_no 2``: freeze the 2 lowest occupied spinors.
| ``occ_batch 4``: build the doubles intermediate 4 occupied indices at a
  time.
| ``nroots 4``: 4 excitation energies will be calculated.

which gives output in the same house style as ``EE-ADC(2)``
(see :doc:`adc`):

.. code-block:: shell

     system: o=8 v=98 naux=142  method=CD-EE-ADC(2)-FOLDED
     E(MP2 correlation) =      -0.2481930517 a.u.

                ----------------------------------------------------
          ------------- CD-EE-ADC(2)-FOLDED values -----------------
                ----------------------------------------------------

     Root: 1	 EE-ADC value: 0.376378 a.u.	  10.241999 eV
     Dominant Transition
     8 --> 10 0.47
     8 --> 11 0.48

     memory: Davidson subspace holds ph vectors (size 784); 2p2h vector
     (size 614656) never stored -- only a transient per matvec

     **** CD-EE-ADC(2)-FOLDED Calculation Completed ****

(The numeric values above are illustrative of the print format only; see
``test/cd_ee_adc2_folded.inp`` in the BAGH repository for a runnable
example.) ``system:`` reports the problem size (``o``/``v`` occupied/virtual
spinors, ``naux`` Cholesky auxiliary functions). Each root line gives the
excitation energy in both Hartree and eV, followed by the dominant orbital
transitions (weight above 10% of the singles-vector norm by default). The
final line reports that the ``o*v``-sized singles vector is what the
Davidson subspace actually holds, in contrast to the ``(o*v)^2``-sized 2p2h
vector that ``EE-ADC(2)`` would otherwise store per trial vector.

.. _cd-adc2-folded-tdm:

Transition Dipole Moments
==========================

Gated on the ``tdm`` keyword (default ``True``, same as ``EE-ADC(2)``; see
:doc:`adc`). ADC(2) is Hermitian, so the transition-moment vector only needs
the doubles part of each converged excited-state vector -- which the folded
scheme discards by construction (see Theory above). It is recovered on the
fly, once per converged root, instead of being kept resident:
``R2(w) = (w-D)^-1 M_DS R1``, reusing the same doubles-building code the
sigma routine already runs transiently every Davidson iteration. The
resulting transition density is contracted with the dipole integrals the
same way ``EE-ADC(2)`` does, and printed in the same house style.

.. code-block:: shell

   ! CD-EE-ADC(2)-FOLDED soc-x2camf spinor unc-ccpvdz

   %cc
   cd True
   nroots 4
   fc True
   fc_no 2
   DoCVS True
   CVSMIN 0
   CVSMAX 2
   cd_threshold 1e-3
   occ_batch 2
   adc_convergence 1e-4
   tdm True
   end

   *xyz 0 1
   Si 0.000000 0.000000 0.000000 dyall.v2z
   Cl 1.192288 1.192288 1.192288 augccpvdz
   Cl -1.192288 -1.192288 1.192288 augccpvdz
   Cl 1.192288 -1.192288 -1.192288 augccpvdz
   Cl -1.192288 1.192288 -1.192288 augccpvdz

which, after the usual ``Root:``/``Dominant Transition`` block, gives:

.. code-block:: shell

   ************************ Transition Dipole Moment *****************************

   state         TX             TY           TZ
                 (au)           (au)         (au)
     1    0.01177+0.00961j   0.00342+0.00279j -0.03836-0.03131j
     2    0.00098-0.00669j   -0.00749+0.05093j -0.00037+0.00249j
     3    -0.04924-0.00120j   -0.00570-0.00014j -0.01562-0.00038j
     4    -0.02504+0.04550j   0.04594-0.08349j 0.00031-0.00057j

                    ABSORPTION SPECTRUM VIA TRANSITION ELECTRIC DIPOLE MOMENTS

                         State     Energy      Wavelength      fosc         T2          TX        TY         TZ
                                   (cm-1)        (nm)                    (au**2)       (au)      (au)       (au)
                            1    889691.7        11.240      0.00730     0.00270     0.01519     0.00441     0.04952
                            2    889691.7        11.240      0.00730     0.00270     0.00676     0.05148     0.00251
                            3    889691.7        11.240      0.00730     0.00270     0.04925     0.00570     0.01562
                            4    896267.5        11.157      0.03207     0.01178     0.05194     0.09529     0.00065

(This is real output from a Si core-hole (K-edge, CVS) SiCl4 calculation on
the input above -- see ``test/sicl4_stage1_cd_ee_adc2_folded.inp`` in the
BAGH repository.)

State-Averaged Frozen Natural Spinors (SA-FNS)
================================================

Method name on the ``!`` line: ``SA-FNS-CD-EE-ADC(2)-FOLDED``. Truncates the
virtual space before the (expensive) folded ADC(2) production solve --
useful when the canonical virtual space is large enough that the folded
solve above is itself the bottleneck. Mirrors the existing, non-folded
relativistic driver (``SS-FNO-A-EE-ADC(2)``; see :doc:`methods_relativistic`)
stage for stage, but built entirely from Cholesky vectors instead of that
driver's dense integral blocks:

1. A cheap, full-virtual-space *bare* CIS (Hartree-Fock-level, no MP2
   self-energy correction) solve finds the lowest ``nroots`` states.
2. For each, the ADC(2) doubles amplitude is reconstructed at the CIS
   energy (the same reconstruction used for TDM above), standing in for a
   perturbative CIS(D) correction. The virtual-block density
   ``0.5*<R2*.R2> + <R1.R1*>``, averaged over these states plus the
   ground-state MP2 density, gives one common natural-spinor virtual basis
   for all of them.
3. That density is diagonalized and re-canonicalized (the same construction
   ``FNO``/``SS-FNS`` truncation uses elsewhere in BAGH) to select the
   active virtual spinors; the Cholesky vectors are rotated into that
   truncated basis.
4. The **expensive** MP2/ADC(2) intermediates are built exactly once,
   afterwards, in the truncated (small) virtual space -- never in the full
   canonical space -- and the real folded ADC(2) production solve (and, if
   ``tdm``, the transition dipole moments) runs there.

Because step 1 is a genuinely cheap bare-CIS solve rather than a full
diagonalization, ``rootno`` selects indices out of the lowest
``max(rootno)+1`` states found this way, not an arbitrary index into an
unbounded full spectrum; ``rootno_s``, if given, further restricts which of
those states actually get the (expensive) truncated production solve, while
the density is still averaged over the full ``rootno`` set -- matching
``SS-FNO-A-EE-ADC(2)``'s own two-stage semantics.

Reuses ``fnothresh_ex``, ``nvir_act``, ``povo_ex``, ``pct_occ_ex``,
``plotnat``, ``rootno``, ``rootno_s`` (same keywords as
``SS-FNO-A-EE-ADC(2)``; see :doc:`keyword`) to control the truncation.

.. code-block:: shell

   ! SOC-X2CAMF SA-FNS-CD-EE-ADC(2)-FOLDED spinor

   %cc
   CD True
   fc True
   fc_no 44
   adc_convergence 1e-4
   DoCVS True
   CVSMIN 0
   CVSMAX 2
   occ_batch 2
   cd_threshold 1e-3
   nroots 4
   fnothresh_ex 1e-3
   x2c_type x2cmp
   end

   *xyz 0 1
   Si 0.000000 0.000000 0.000000 dyall.v2z
   Cl 1.192288 1.192288 1.192288 augccpvdz
   Cl -1.192288 -1.192288 1.192288 augccpvdz
   Cl 1.192288 -1.192288 -1.192288 augccpvdz
   Cl -1.192288 1.192288 -1.192288 augccpvdz

On the same SiCl4 system as above, this reduces the canonical 216-virtual-spinor
space to 92 active natural spinors (``fnothresh_ex 1e-3``), roughly halving
the total run time relative to the untruncated ``CD-EE-ADC(2)-FOLDED``
calculation, at the cost of a small (~0.03 a.u.) shift in the excitation
energies from the truncation itself.

Tests
=====

``tests/test_adc2_folded.py`` in the BAGH repository (``pytest -m fast``) is
a dependency-free correctness suite -- no SCF, no pyscf/socutils -- checking
integral antisymmetry, exact folding against an explicit dense
singles+doubles build, CVS against a CVS-projected dense build,
batched-vs-unbatched sigma, and the bagh-``eris`` adapter against direct
construction. Transition-moment reconstruction (``build_r2``) is checked
against an explicit dense eigenvector, and the ground-state/transition
density formulas against the non-folded ``EE-ADC(2)`` driver's own formulas,
transcribed. SA-FNS's natural-spinor truncation is checked via an exactness
identity (no actual truncation must reproduce the untruncated eigenvalues to
machine precision) plus a bit-for-bit check that its ``build_intermediates=
False`` optimization changes nothing about the result. None of this
exercises the full X2CAMF/SCF/CD pipeline end to end;
``test/cd_ee_adc2_folded.inp``, ``test/sicl4_stage1_cd_ee_adc2_folded.inp``
and ``test/sicl4_sa_fns_cd_ee_adc2_folded.inp`` are provided for that.
