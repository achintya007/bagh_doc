DLPNO-IP-ADC(2) / DLPNO-IP-ADC(3)
###################################

``DLPNO-IP-ADC(2)`` and ``DLPNO-IP-ADC(3)`` compute the same vertical
ionization potentials as ``IP-ADC(2)`` and ``IP-ADC(3)``, in a basis of
**pair natural orbitals** built on **projected atomic orbital domains** around
**localized occupied orbitals**. Nothing is transformed into the canonical
virtual space: the occupied orbitals are localized, each pair of them is given
its own small virtual space, and every integral is generated once, directly in
the PNO basis of the pair that owns it.

The reference must be closed-shell RHF.

Theory
======

The 1h and 2h1p spaces are the usual ones,

.. math::

   \mathbf{M} =
   \begin{pmatrix} M_{ij} & C_{sd} \\ C_{ds} & D \end{pmatrix},

but the doubles index :math:`(j,k,a)` no longer runs over all virtuals. For
each strong pair :math:`(j,k)` the virtual label :math:`a` runs only over that
pair's PNOs, of which there are typically twenty to sixty rather than several
hundred. Everything else follows from having to move quantities between
different pairs' spaces, which is done with the cross-pair overlap

.. math::

   S^{PQ}_{ab} = \langle a_P | b_Q \rangle .

The routing rule
----------------

One rule decides how each term of the ADC matrix is evaluated, and getting it
wrong is the single largest source of error in a local ADC(3):

  **A virtual index summed between an integral and an amplitude must be
  carried in a pair that owns it.**

Every virtual belongs to an occupied label: in :math:`t^{ij}_{ab}` the index
:math:`a` belongs to :math:`i` and :math:`b` to :math:`j`, and the same is
true of :math:`(i\,a\,|\,j\,b)`. A pair's PNO space spans the virtuals of its
own two occupieds and nothing else. Projecting an amplitude wholesale into
some third pair -- the obvious thing to do -- therefore truncates the summed
index in a space that has no reason to span it, and the error then **does not
fall when TCutPNO falls**. On water this was worth about 300 meV at every
threshold, spread over five terms of :math:`M_{ij}^{(3)}`, the third-order
:math:`M_{ds}` and the first-order 2h1p--2h1p block.

Two kinds of contraction are exempt: amplitude times amplitude with no
integral between them, since both factors decay; and an integral whose two
virtuals sit on the same electron, :math:`(x\,y\,|\,a\,b)`, which has no
per-electron ownership to respect.

Which pair owns it
------------------

The rule says the summed index must be carried in a pair that owns it; the
sharpest answer to *which* is the pair the amplitude itself belongs to. In the
eight ring terms of the second-order amplitude,

.. math::

   R^{ij}_{ab} \;+\!= \; \sum_c g_{\ldots c \ldots}\, t^{Q}_{\ldots c \ldots},

the amplitude :math:`t^{Q}` is identically zero outside :math:`\mathrm{PNO}(Q)`,
so summing :math:`c` over :math:`\mathrm{PNO}(Q)` is not an approximation to
the full-space sum -- it *is* that sum, to the accuracy of the amplitude
itself. The price is a mixed integral with one virtual in :math:`Q` and the
other in the target pair, and it is a small one: nothing new is stored, since
the mixed block is the two pairs' own three-index blocks contracted over a
common fitting domain inside the pair loop.

Measured as the *domain* error of MP3 -- the same code at ``TCutDO = 1e-2``
minus the same code with the domains switched off, so that the PNO truncation
divides out -- on a chain of four waters 3.2 A apart in cc-pVDZ:

+-------------+-----------------+-------------------+--------------------+
| ``TCutPNO`` | PNO(*Q*)        | target pair's PAO | orbital domain of  |
|             |                 | domain            | the summed occupied|
+=============+=================+===================+====================+
| 1e-5        | -0.15           | -4.88             | +5.51              |
+-------------+-----------------+-------------------+--------------------+
| 1e-6        | -0.43           | -50.66            | +43.63             |
+-------------+-----------------+-------------------+--------------------+

(micro-:math:`E_h`). The two domain answers bracket the right one from
opposite sides, which is the signature of an amplitude being projected rather
than an integral being screened; routing through the amplitude's own pair is
two orders of magnitude closer, and is also the cheapest of the three, a PNO
pair being a handful of functions where a PAO domain is tens. Water shows none
of this -- its domains are complete -- which is why ``tests/test_ring.py``
runs on a chain.

Approximations
--------------

* **PNO truncation** (``TCutPNO``) -- the only approximation in the virtual
  space, and the one that dominates.
* **Pair screening** (``TCutPairs``) -- weak pairs are dropped after a dipole
  prescreen; their MP2 energy is reported but they contribute nothing to the
  ADC matrix.
* **PAO domains** (``TCutDO``) -- each pair's virtual space is restricted to
  the projected atomic orbitals on nearby atoms, chosen by differential
  overlap.
* **Local density fitting** (``TCutMKN``) -- each pair is fitted in the
  auxiliary functions of its own fitting domain.

The pair prescreen
------------------

Three steps, in the paper's order, because no single criterion does the job:

**Step 1 -- a cheap estimate, over every pair.** Two channels, because no
single cheap quantity sees both sources of a pair energy.

The *overlap channel* is the one that decides on ordinary molecules. When two
localized orbitals share space the pair energy is driven by their exchange
integral, which involves the product density :math:`i(r)j(r)`, so it tracks
the square of their differential overlap:

.. math::

   e_{ij} \;\approx\; -\,c_{\mathrm{DOI}}\; \mathrm{DOI}(i,j)^2 .

This is not a guess. Measured over every pair of a four-water chain and a
C\ :sub:`8`\ H\ :sub:`16` chain in cc-pVDZ, :math:`|e_{ij}|/\mathrm{DOI}^2`
lies between 0.3 and 10\ :sup:`2` across all pairs worth more than
10\ :sup:`-6` Eh -- two orders of magnitude of scatter for pair energies
spanning five. The smallest prefactor that would keep every pair worth more
than ``TCutPairs`` is 0.38 on those systems; the default ``c_doi = 25`` is
therefore about a sixty-fold safety factor, and on C\ :sub:`6`\ H\ :sub:`12`
the discarded total comes out within 10% of the true one.

The *dispersion channel* is the multipole estimate: two orbitals that do not
overlap at all still have an :math:`R^{-6}` pair energy, and DOI is
exponentially blind to it. It is formed only for pairs with
:math:`\mathrm{DOI}(i,j)` below ``TCutDO_ij``, where a multipole expansion
means something.

A pair is kept if **either** channel says it matters. Neither is a veto. In
practice the overlap channel does nearly all the work -- on the test systems
above the dispersion channel changes one decision out of several hundred --
but it is cheap and the case it covers is real.

.. note::

   An earlier version had this backwards. The dipole estimate was the only
   criterion and DOI was used as a *guard* that kept overlapping pairs without
   estimating them; on any compact system that guard fired for essentially
   every pair, so step 1 decided nothing. What was actually removing pairs was
   an upstream topological test -- "do these two domains share an atom" --
   which has no energy attached, so everything it discarded disappeared from
   the weak-pair sum without a trace. Step 1 now runs over every unordered
   pair and accounts for everything it drops.

**Step 2 -- the semicanonical LMP2 pair energy.** For every survivor,

.. math::

   e_{ij} = (2 - \delta_{ij}) \sum_{ab} K^{ij}_{ab}
            \left( 2 t^{ij}_{ab} - t^{ij}_{ba} \right), \qquad
   t^{ij}_{ab} = \frac{-K^{ij}_{ab}}
                      {\epsilon_a + \epsilon_b - F_{ii} - F_{jj}}

computed in the pair's own PAO domain with the local fit. **This** is what
``TCutPairs`` is applied to. It costs one domain orthogonalization, one local
fit and one division per candidate -- the same work as the first four steps of
the PNO construction, and no PNOs.

**Step 3 -- the weak-pair correction.** Everything discarded is summed at the
best estimate available for it (the step-2 pair energy for what step 2 dropped,
the dipole estimate for what step 1 dropped without ever computing one) and
reported as ``E_weak``. ``E(MP2) + E_weak`` is the method's estimate of the
unscreened MP2 energy.

On a chain of four water molecules at 3.2 Å in cc-pVDZ, at NORMALPNO, of the
136 unordered pairs step 1 keeps 94 and step 2 keeps 40; the discarded totals
are 6.9e-6 Eh and 1.2e-3 Eh respectively. Tightening to TIGHTPNO moves step 1
to 104 kept and 4.6e-7 Eh discarded, and step 2 to 73 kept.

The fitting domain
------------------

The auxiliary functions a pair is fitted in have to represent the products
:math:`i(r)\,\tilde\mu(r)`, and how far those reach is a question about the
*occupied* orbital -- the PAO domain answers a different question. ``TCutMKN``
asks the orbital directly: sort the atoms by that orbital's Löwdin population,
take them in that order until the population left behind falls below the
threshold. The pair's fitting domain is the union of the two orbitals'.

Smaller ``TCutMKN`` leaves less behind and therefore keeps *more* atoms, in the
same direction as every other ``TCut*``. On the same water chain, with every
other threshold switched off, measured against a global fit:

+--------------+------------------+-------------------+
| ``TCutMKN``  | aux per pair     | error in E(LMP2)  |
+==============+==================+===================+
| 1e-1         | 185              | 3.0e-4 Eh         |
+--------------+------------------+-------------------+
| 1e-2         | 203              | 2.1e-5 Eh         |
+--------------+------------------+-------------------+
| **1e-3**     | **227**          | **1.3e-5 Eh**     |
+--------------+------------------+-------------------+
| 1e-4         | 267              | 5.2e-6 Eh         |
+--------------+------------------+-------------------+

The default is 1e-3, as in ORCA, and is not one of the numbers the composite
levels vary. ``tcut_mkn 0`` disables the criterion and falls back to fitting
each pair in the auxiliary functions on its PAO-domain atoms, which is what
the code did before ``TCutMKN`` existed and is what the exactness gates use.

Thresholds
==========

``dlpno_thresh`` selects a whole row of Table I of Dutta, Saitow, Riplinger,
Neese and Izsák, *J. Chem. Phys.* **148**, 244101 (2018):

+-------------------+-------------+--------------+-------------+
| keyword           | LOOSEPNO    | NORMALPNO    | TIGHTPNO    |
+===================+=============+==============+=============+
| ``tcut_pno``      | 1e-6        | 3.33e-7      | 1e-7        |
+-------------------+-------------+--------------+-------------+
| ``tcut_pairs``    | 1e-3        | 1e-4         | 1e-5        |
+-------------------+-------------+--------------+-------------+
| ``tcut_do``       | 2e-2        | 1e-2         | 5e-3        |
+-------------------+-------------+--------------+-------------+
| ``tcut_doi_occ``  | 0           | 0            | 0           |
+-------------------+-------------+--------------+-------------+
| ``tcut_pao``      | 1e-8        | 1e-8         | 1e-8        |
+-------------------+-------------+--------------+-------------+
| ``tcut_mkn``      | 1e-3        | 1e-3         | 1e-3        |
+-------------------+-------------+--------------+-------------+
| ``c_doi``         | 25          | 25           | 25          |
+-------------------+-------------+--------------+-------------+

``TCutDO`` runs the other way from the rest: TIGHT has the *smallest* value
and therefore the *largest* domains. Two further thresholds are derived from
``tcut_pairs`` unless given explicitly: ``tcut_pre = tcut_pairs/100`` (the
dipole prescreen) and ``tcut_doi_pair = tcut_pairs/10`` (the overlap guard on
it). ``tcut_doi_occ``, which screens the occupied lists of the integral store,
is not from the paper and is now zero at every level -- see below.

Any single entry can be overridden after the composite is applied, for
example::

   dlpno_thresh NORMALPNO
   tcut_pno     1e-7

Two implementations
===================

The method exists twice in the tree, and both are maintained:

``bagh_code/dlpno_cxx``
   The production implementation: C++ behind pybind11, OpenMP-parallel, with
   an explicit memory plan that decides from ``maxcore`` which integral
   classes stay in RAM and which are memory-mapped from disk.

``bagh_code/dlpno/reference.py``
   The same method in plain NumPy -- no C++, no sparse maps, no memory
   manager and no Davidson. Every quantity is a dense array, every
   contraction is one ``einsum``, and the eigenproblem is formed in full and
   handed to ``numpy.linalg.eig``. It is there to be read, and to check the
   C++ against something that shares no code with it.

They agree to **0.000 meV** on water at every threshold tested, and reproduce
the same MP2 and MP3 energies to :math:`10^{-12}` Eh. Select the readable one
with a single keyword::

   dlpno_python true

It is much slower and stores everything densely; use it up to about a dozen
heavy atoms. Above 250 basis functions it prints a warning and carries on.

Placement in ``RHF.py``
-----------------------

``DLPNO-IP-ADC(2)`` and ``DLPNO-IP-ADC(3)`` are dispatched by method name in
``run_rhf``, before the canonical integral transformation rather than after it
with the other IP-ADC blocks. The module reads nothing out of ``eris``: it
builds its own localized orbitals, PAOs, PNOs and density-fitted integrals
directly from ``mol``/``mf``, and every integral it forms already carries the
PNO indices of one pair. Letting ``int_tranf`` run first would perform exactly
the transformation the method exists to avoid -- at the default incore level
that materializes the dense ``(vv|vv)`` block, which on the systems DLPNO is
for is the entire cost.

Building the C++
----------------

The extension is built in place, next to its sources, by the top-level CMake:

.. code-block:: shell

   cmake -S . -B build -DCMAKE_BUILD_TYPE=Release
   cmake --build build --target bagh_dlpno -j

This leaves ``bagh_code/dlpno_cxx/bagh_dlpno.cpython-*.so`` where the Python
side expects it. The target finds its own pybind11 and OpenMP and falls back
to serial plus system BLAS when either is missing, so a stock macOS box
without ``libomp`` still builds. If it has not been built, asking for a
DLPNO method prints the two commands above rather than a missing-module
traceback.

Keywords
========

- ``nroots``: number of roots to converge.
- ``adc_convergence``: Davidson convergence tolerance.
- ``dlpno_thresh``: ``LOOSEPNO`` | ``NORMALPNO`` (default) | ``TIGHTPNO``.
- ``dlpno_python``: ``true`` runs the readable NumPy implementation.
- ``dlpno_sos``: ``true`` adds the folded SOS-ADC(2) truncation correction
  (see below).
- ``cos_scale``: :math:`c_{os}` for that correction, default 1.3.
- ``tcut_pno``, ``tcut_pairs``, ``tcut_do``, ``tcut_pao``, ``tcut_mkn``,
  ``tcut_doi_occ``, ``tcut_pre``, ``tcut_doi_pair``, ``c_doi``: individual
  overrides.
- ``maxcore``: in MB, as everywhere in BAGH. The memory plan uses it to
  decide what to spill; if it is too small for the data that cannot be
  spilled, the run stops with a report rather than swapping.
- ``fc`` / ``fc_no``: frozen core, the usual BAGH convention. The frozen
  orbitals are neither localized nor correlated.

The auxiliary basis comes from the ``%ribasis`` block or from the third field
of the ``!`` line, exactly as for the other density-fitted methods.

The SOS-ADC(2) truncation correction
====================================

At strict ADC(2) the 2h1p block is exactly :math:`D`, so Löwdin partitioning
is an identity rather than an approximation:

.. math::

   [\,M + F(\omega)\,] c = \omega c, \qquad
   F(\omega) = C_{sd} (\omega - D)^{-1} C_{ds}, \qquad \mathrm{IP} = -\omega .

Running that fold canonically (before localization) and again in the PNO
basis gives the ADC(2) truncation error *measured* rather than estimated, and
``dlpno_sos true`` adds the difference to the ADC(3) result.

It is off by default, and the honest reason is that on the systems tested it
does not help. The correction removes exactly the doubles-space channel --
verified to the microvolt -- but the third-order channel it cannot see is of
comparable size and does not have the same sign on every root, so correcting
one leaves the other exposed. Section III B of the DLPNO-IP paper reaches the
same conclusion for the analogous weak-pair correction. It is provided
because the number itself is informative: it tells you how much of the error
is doubles-space truncation.

Accuracy
========

Formaldehyde, aug-cc-pVDZ, frozen C 1s and O 1s, six roots. The reference
column is the same code with every threshold switched off, which isolates the
locality error from every other difference -- same equations, same integrals,
same solver:

.. code-block:: shell

                    canonical      LOOSEPNO     NORMALPNO      TIGHTPNO
     IP 1       11.1156  93%   11.0791  93%   11.0977  93%   11.1093  93%
     IP 2       14.5958  91%   14.5746  92%   14.5940  92%   14.6029  91%
     IP 3       16.3944   1%   18.5883  13%   18.4768  15%   17.0777   0%
     IP 4       16.6595  91%   16.6607  92%   16.6620  91%   16.6745  91%
     IP 5       17.1594  70%   17.2246  75%   17.2058  73%   17.2035  73%
     IP 6       18.3570  17%   21.1991   0%   21.0702   0%   18.4900  15%
     err 1h            0.0          65.2          46.4          44.1  meV
     mean n_PNO       56.0          24.7          30.1          35.1
     time/s           73.1          11.9          17.1          23.1

The percentage after each root is its singles character, and the rows are
matched on that rather than on sorted order -- which matters here, because
IP 3 is a **shake-up state with 1% singles character** sitting between two
ordinary one-hole states. Truncating the PNO space moves it by hundreds of
meV and it changes places in the sorted list; comparing root 3 with root 3
would then subtract two different states from each other.

Read the table in two parts:

* the **one-hole states** are converged to a few tens of meV, which is what a
  DLPNO method is expected to deliver: 6, 7, 15 and 44 meV at TIGHTPNO.
* the **shake-up states** are not, and cannot be. They live entirely in the
  2h1p space, which is exactly what the PNO basis truncates, and the PNOs are
  built from MP2 pair densities that know nothing about them. Treat
  satellite states from a DLPNO run as qualitative.

The canonical limit of this code agrees with PySCF's own IP-ADC(3) to 3.8 meV
-- a code-versus-code difference in how the third-order :math:`M_{ij}` is
written, not a DLPNO error.

Example input
=============

.. code-block:: shell

   ! DLPNO-IP-ADC(3) aug-cc-pvdz aug-cc-pvdz-ri

   %cc
   nroots 4
   fc true
   dlpno_thresh TIGHTPNO
   maxcore 8000
   end

   *xyz 0 1
   C   0.0000   0.0000  -0.5290
   O   0.0000   0.0000   0.6750
   H   0.0000   0.9376  -1.1120
   H   0.0000  -0.9376  -1.1120
   *

which prints:

.. code-block:: shell

   ========================================================================
     DLPNO-IP-ADC(3)   TIGHTPNO
   ========================================================================
     prescreen: TCutPre = 1.0e-07, TCutDO_ij = 1.0e-06, TCutMKN = 1.0e-03
     step 1 (dipole):        21 candidates -> 21 (21 kept by the DOI guard),
                             E_weak = 0.000000000 Eh
     step 2 (semicanonical): 21 -> 21 strong, 0 weak, E_weak = 0.000000000 Eh
     step 3 (correction):    E_weak(total) = 0.000000000 Eh
     fitting domain: 320 of 320 auxiliary functions on average
     mean n_PNO = 35.1, PNO truncation error 0.000068 Eh
     E(LMP2) = -0.3328052
     E(MP3)  = -0.0076891

      root      IP / eV        IP / Eh
         1      11.1093     0.40826...
         2      14.6029     0.53664...
         3      17.0777     0.62759...
         4      16.6745     0.61277...

To read the algorithm instead of running it, add ``dlpno_python true`` and
open ``bagh_code/dlpno/reference.py`` alongside the output.

Still not implemented
=====================

The pure-Python implementation fits globally and does not screen occupied
lists, so ``tcut_mkn``, ``tcut_pre``, ``tcut_doi_pair`` and ``tcut_doi_occ``
have no meaning there; asking for them with ``dlpno_python true`` prints a
note rather than accepting them silently.

``tcut_doi_occ`` -- which decides, for each pair, the occupied labels its
integral blocks are generated for -- is not from the paper, and is switched
off at every level. It drops an occupied label ``x`` from pair ``(i,j)`` when
``x`` has no differential overlap with the pair's PAO domain, and when it was
finally tested on a system where it does anything, it turned out not to
converge. On compact molecules every label survives and the threshold is
inert: H2CO / aug-cc-pVDZ gives IPs identical to five decimals with it at its
old value and at zero, at all three levels. On a chain of four waters 3.2 A
apart, where it does cut, it costs 140 meV at ``1e-2`` *and* at ``1e-3`` --
both of its old values -- and still 15-47 meV at ``1e-4``. Inert where it is
safe and unconverged where it bites is not a threshold. The replacement is
not a new number: ``x`` can be dropped from ``(i,j)`` only when neither
``(x,i)`` nor ``(x,j)`` survived the pair screen, since those are the
amplitudes the terms it would enter carry. Until that is implemented, every
occupied label is kept.

Validity
========

* Closed-shell RHF references only.
* One-hole (quasiparticle) states are the intended target. Satellite states
  are computed and reported but are not converged with respect to the PNO
  truncation.
* The third-order :math:`M_{ij}` of ``calcimds_adc.py`` is not symmetric,
  although the self-energy is Hermitian. It is symmetrized here by default:
  the roots move by well under a meV, but leaving it unsymmetrized breaks the
  self-adjointness the symmetric Davidson rests on and the residual then
  floors out short of its tolerance.
