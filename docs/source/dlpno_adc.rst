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
* **Local density fitting** -- each pair is fitted in the auxiliary functions
  of its own domain.

Not implemented: ``TCutMKN`` (a separate criterion for the fitting domain;
here the fitting domain is simply the auxiliary functions on the pair's domain
atoms), and steps 2--3 of the paper's three-step prescreen (the semicanonical
LMP2 pair energies that make the final strong/weak call, and the weak-pair
correction). What is implemented is step 1, a dipole estimate applied a
hundred times more loosely than ``TCutPairs``, with a differential-overlap
guard so that no pair close enough to invalidate the multipole expansion is
ever discarded on the strength of it.

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
| ``tcut_doi_occ``  | 1e-2        | 1e-2         | 1e-3        |
+-------------------+-------------+--------------+-------------+
| ``tcut_pao``      | 1e-8        | 1e-8         | 1e-8        |
+-------------------+-------------+--------------+-------------+

``TCutDO`` runs the other way from the rest: TIGHT has the *smallest* value
and therefore the *largest* domains. Two further thresholds are derived from
``tcut_pairs`` unless given explicitly: ``tcut_pre = tcut_pairs/100`` (the
dipole prescreen) and ``tcut_doi_pair = tcut_pairs/10`` (the overlap guard on
it). ``tcut_doi_occ``, which screens the occupied lists of the integral store,
is not from the paper and is not calibrated against anything.

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
``run_rhf``, alongside ``IP-ADC(2)`` and ``IP-ADC(3)``. They read nothing out
of ``eris``: the module builds its own localized orbitals, PAOs, PNOs and
density-fitted integrals directly from ``mol``/``mf``, and every integral it
forms already carries the PNO indices of one pair. The shared ``int_tranf``
setup that runs before the dispatch is therefore not used by these methods --
worth knowing on a large molecule, where that transformation is the cost the
DLPNO treatment exists to avoid.

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
- ``tcut_pno``, ``tcut_pairs``, ``tcut_do``, ``tcut_pao``, ``tcut_doi_occ``,
  ``tcut_pre``, ``tcut_doi_pair``: individual overrides.
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
     pairs: 21 strong, 0 weak of 21 candidates
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
