X2CAMF Double Hybrids: RI-DH and THC-LT-DH
##########################################

``RI-DH`` and ``THC-LT-DH`` are two-component double-hybrid density
functionals on the ``SOC-X2CAMF`` interface. A spinor Kohn--Sham calculation
on the X2CAMF Hamiltonian (spin--orbit coupling included variationally) is
followed by a second-order perturbative correction evaluated on the KS
spinors. The PT2 term is spinor MP2, computed either exactly in a
resolution-of-the-identity (RI) auxiliary basis (``RI-DH``) or through the
low-memory THC + Laplace-transform machinery of :doc:`thc_lt_mp2`
(``THC-LT-DH``).

Theory
======

A double hybrid mixes Hartree--Fock exchange and a scaled semilocal
correlation functional in the self-consistent step, then adds a fraction of
MP2 correlation computed on the resulting orbitals:

.. math::

   E_\mathrm{DH}
     = E_\mathrm{KS}\big[a_x E_x^\mathrm{HF} + (1-a_x) E_x^\mathrm{DFA}
                        + (1-c)\, E_c^\mathrm{DFA}\big]
     + c\, E_\mathrm{MP2}[\{\phi^\mathrm{KS}\}, \{\varepsilon^\mathrm{KS}\}].

Here the KS step is the j-adapted two-component spinor DFT of socutils
(``socutils.dft.dft.SpinorDFT``). Its exchange--correlation potential is
evaluated in the cheaper scalar-AO :math:`\times` spin representation, and
it uses the same X2CAMF/X2CMP one-electron Hamiltonian, with optional Gaunt
and Breit picture-change corrections, as the ``SOC-X2CAMF`` Hartree--Fock
driver. The PT2 term is the antisymmetrized spinor MP2,

.. math::

   E_\mathrm{MP2} = E_\mathrm{dir} + E_\mathrm{exc},
   \qquad
   E_\mathrm{dir}
     = -\tfrac12 \sum_{ijab} \frac{|(ia|jb)|^2}{D_{ijab}},
   \qquad
   E_\mathrm{exc}
     = +\tfrac12 \sum_{ijab}
       \frac{\operatorname{Re}\big[(ia|jb)(ib|ja)^\ast\big]}{D_{ijab}},

with :math:`D_{ijab}=\varepsilon_a+\varepsilon_b-\varepsilon_i-\varepsilon_j`
built from KS eigenvalues. As in the standard double-hybrid definition, the
non-Brillouin singles contribution is neglected.

Evaluating the PT2 term
-----------------------

**RI-DH.** The three-index integrals
:math:`(ia|L)` in the spinor MO basis are built by streaming over blocks of
the auxiliary basis and are stored on disk. Then

.. math::

   (ia|jb) = \sum_L L^L_{ia} L^L_{jb}

is formed one occupied block pair at a time, and both diagrams are
contracted with the exact denominators. Both diagrams are symmetric under
:math:`i\leftrightarrow j`, so only :math:`j`-blocks :math:`\ge` the
:math:`i`-block are formed. The block size is set by the memory budget.
The cost is :math:`O(o^2 v^2 n_\mathrm{aux})` (conventional RI-MP2), the
peak memory is :math:`O(n_b^2 v^2)`, and no :math:`o^2v^2` array is held.

**THC-LT-DH.** The direct diagram is evaluated with THC + Laplace at
:math:`O(K^3)` cost. The ISDF THC factors are fitted to the same RI
integrals, over the active (non-frozen) spinors only. The exchange diagram
uses RI with exact denominators. This is exactly the
:doc:`thc_lt_mp2` kernel, applied to the KS spinors.

Spin-component scaling
----------------------

A spinor basis has no exact :math:`S_z`, so no opposite-spin/same-spin
split exists. Following :doc:`thc_lt_mp2`, the optional ``scs_os`` /
``scs_ss`` keywords scale :math:`E_\mathrm{dir}` / :math:`E_\mathrm{exc}`
instead:

.. math::

   E_\mathrm{DH} = E_\mathrm{KS}
     + c\,\big(c_\mathrm{os} E_\mathrm{dir} + c_\mathrm{ss} E_\mathrm{exc}\big).

All built-in functionals are non-SCS double hybrids, so both factors
default to 1. Spin-component-scaled (DSD-type) functionals are not
provided, because their parameters refer to a spin separation that does not
exist here.

Built-in functionals
====================

Select a functional with ``dh_functional``:

.. list-table::
   :header-rows: 1
   :widths: 16 46 12 26

   * - name
     - KS functional (``xc`` string)
     - :math:`c`
     - reference
   * - ``B2PLYP`` (default)
     - ``0.53*HF + 0.47*B88, 0.73*LYP``
     - 0.27
     - Grimme 2006
   * - ``B2GPPLYP``
     - ``0.65*HF + 0.35*B88, 0.64*LYP``
     - 0.36
     - Karton *et al.* 2008
   * - ``MPW2PLYP``
     - ``0.55*HF + 0.45*MPW91, 0.75*LYP``
     - 0.25
     - Schwabe & Grimme 2006
   * - ``PBE0-DH``
     - ``0.5*HF + 0.5*PBE, 0.875*PBE``
     - 1/8
     - Brémond & Adamo 2011
   * - ``PBE-QIDH``
     - :math:`3^{-1/3}` HF + PBE, :math:`2/3` PBE
     - 1/3
     - Brémond *et al.* 2014
   * - ``PBE0-2``
     - :math:`2^{-1/3}` HF + PBE, :math:`1/2` PBE
     - 1/2
     - Chai & Mao 2012

The parameters are stored as explicit ``xc`` strings, because the libxc
bundled with PySCF has no aliases for these double hybrids (and
``PBE0-2`` would silently be parsed as PBE0 :math:`-\,2\times` PBE).
To use any other functional, set ``dh_functional custom`` together with
``dh_xc`` and ``dh_cmp2``. Because the ``%cc`` reader takes only
two-token lines, the ``dh_xc`` string must be written **without blanks**.
``dh_xc`` and ``dh_cmp2`` also override the corresponding parameters of a
named functional.

Examples
========

RI-B2PLYP with Gaunt, frozen core, and the default mp2fit RI basis (or set
an ``%ribasis`` block):

.. code-block:: shell

   !  SOC-X2CAMF RI-DH spinor cc-pvdz Angstrom

   %cc
   dh_functional B2PLYP
   Gaunt True
   fc True
   end

   *xyz 0 1
   H  0.000000  0.000000  0.000000
   Br 0.000000  0.000000  1.414000
   *

The same calculation with the THC + Laplace PT2 term:

.. code-block:: shell

   !  SOC-X2CAMF THC-LT-DH spinor cc-pvdz Angstrom

   %cc
   dh_functional B2PLYP
   Gaunt True
   fc True
   lt_spacing 0.3
   thc_c 10
   thc_mp2_grid_level 3
   end

A custom double hybrid, with the KS step using RI-JK (``df True``; the JK
auxiliary basis is taken from ``%jkbasis``):

.. code-block:: shell

   %cc
   dh_functional custom
   dh_xc 0.53*HF+0.47*B88,0.73*LYP
   dh_cmp2 0.27
   df True
   end

For HBr/cc-pVDZ (Gaunt, frozen core) the two inputs above give
:math:`E_\mathrm{DH} = -2596.1578795` a.u.; ``RI-DH`` and ``THC-LT-DH``
agree to :math:`2\times10^{-8}` a.u.

Validity and remarks
====================

* Validated (``bagh_code/double_hybrid/tests/test_x2camf_dh.py``):

  - In the non-relativistic limit (spinor KS without X2C), ``RI-DH``
    reproduces PySCF RKS + DF-MP2 with the same auxiliary basis, to
    :math:`10^{-13}` a.u. in :math:`E_\mathrm{KS}` and :math:`10^{-15}`
    a.u. in :math:`E_\mathrm{MP2}`, for B2PLYP and PBE0-DH.
  - The blocked RI-MP2 matches a dense antisymmetrized MP2 to
    :math:`10^{-16}`.
  - On an X2CAMF reference, ``THC-LT-DH`` matches ``RI-DH`` to THC +
    Laplace accuracy.

* The method is self-contained. It runs its own spinor-KS SCF in place of
  the X2CAMF Dirac--Hartree--Fock step, and it does not need ``cd True``.
  ``ext_e`` and ``plasma`` embedding are not supported.
* ``fc True`` freezes the default core (``fc_no -1``) or ``fc_no`` spinors,
  in the PT2 term only.
* The scratch files ``dh.LOV.tmp.hdf5`` (and, for THC, ``dh.cderi.tmp.hdf5``)
  are written to the working directory and removed at the end of the run,
  unless ``dh_keep_ints True`` is set.
* Keywords: ``dh_functional``, ``dh_xc``, ``dh_cmp2``, ``dh_grid_level``,
  ``dh_keep_ints``, plus ``scs_os`` / ``scs_ss``, ``df``, ``fc`` /
  ``fc_no``, ``Gaunt`` / ``Breit``, ``x2c_type``, and for ``THC-LT-DH`` the
  THC-LT-MP2 keywords ``lt_spacing``, ``thc_c``, ``thc_mp2_grid_level``,
  ``thc_mp2_mode``, ``thc_mp2_exchange``. See :doc:`keyword`.
