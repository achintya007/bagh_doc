THC-LT-MP2: Low-Memory Relativistic MP2
#######################################

``THC-LT-MP2`` computes the second-order Møller--Plesset
correlation energy on the ``SOC-X2CAMF`` spinor interface **without ever
forming a four-index quantity**. Every other MP2 route in BAGH materializes
either the antisymmetrized :math:`\langle ij||ab\rangle` / :math:`t_2`
tensor (:math:`O(o^2v^2)` complex) or an in-core Cholesky tensor
(:math:`O(n_\mathrm{aux}\,n_\mathrm{mo}^2)`); neither fits once the spinor
basis reaches a few thousand functions. Here the antisymmetrized energy is
split into its two Goldstone diagrams and each is evaluated matrix-free --
the direct (Coulomb) diagram through tensor hypercontraction (THC) and a
Laplace transform (LT), the exchange diagram through the resolution of the
identity (RI) with exact orbital-energy denominators.

Theory
======

In the spinor (two-component X2CAMF) basis the MP2 correlation energy is

.. math::

   E_\mathrm{MP2}
     = -\tfrac14 \sum_{ijab}
       \frac{|\langle ij||ab\rangle|^2}{\varepsilon_a+\varepsilon_b
       -\varepsilon_i-\varepsilon_j},
   \qquad
   \langle ij||ab\rangle = (ia|jb) - (ib|ja).

Expanding the antisymmetrizer and using the :math:`i\leftrightarrow j`,
:math:`a\leftrightarrow b` symmetry of the sum gives two separately
convergent pieces,

.. math::

   E_\mathrm{MP2} = E_\mathrm{dir} + E_\mathrm{exc},

.. math::

   E_\mathrm{dir}
     = -\tfrac12 \sum_{ijab}
       \frac{|(ia|jb)|^2}{D_{ijab}},
   \qquad
   E_\mathrm{exc}
     = +\tfrac12 \sum_{ijab}
       \frac{\operatorname{Re}\big[(ia|jb)(ib|ja)^\ast\big]}{D_{ijab}},

with :math:`D_{ijab} = \varepsilon_a+\varepsilon_b-\varepsilon_i
-\varepsilon_j > 0`.

Direct diagram (THC + Laplace)
------------------------------

The Coulomb integral is written in the grid THC form used throughout BAGH's
X2CAMF THC code (:math:`k` runs over the two spinor components),

.. math::

   (pq|rs) = \sum_{PQ} \rho_{pq}(P)\, Z_{PQ}\, \rho_{rs}(Q),
   \qquad
   \rho_{pq}(P) = \sum_k X^\ast_{k P p}\, X_{k P q},

where :math:`X` is the weighted collocation of the spinor MOs on a pruned
molecular grid of :math:`K` points and :math:`Z` is the least-squares THC
core fitted against the density-fitted ERIs. The denominator is removed by
the Laplace identity

.. math::

   \frac{1}{D_{ijab}}
     = \sum_g w_g\, e^{-D_{ijab}\,t_g},

which factorizes across the four orbital labels. Folding half of each
Boltzmann weight into the collocation factors,

.. math::

   Y^o_{kPi}(t) = X^o_{kPi}\, e^{+\varepsilon_i t/2},
   \qquad
   Y^v_{kPa}(t) = X^v_{kPa}\, e^{-\varepsilon_a t/2},

the direct energy collapses onto :math:`K\times K` grid matrices,

.. math::

   E_\mathrm{dir}
     = -\tfrac12 \sum_g w_g
       \sum_{PR} M_g[P,R]\,\big(Z M_g Z^\dagger\big)[P,R],

.. math::

   M_g[P,R]
     = \sum_{ia}\tilde\rho_{ia}(P)\,\tilde\rho_{ia}(R)^\ast
     = \sum_{k k'}
       \Big(Y^{o\ast}_{k}\, Y^{o\,\mathsf T}_{k'}\Big)[P,R]
       \odot
       \Big(Y^v_{k}\, Y^{v\dagger}_{k'}\Big)[P,R].

:math:`M_g` is Hermitian and is built from four :math:`(K\times o)(o\times
K)` and four :math:`(K\times v)(v\times K)` GEMMs and a Hadamard product;
:math:`N_g = Z M_g Z^\dagger` is two more :math:`K\times K` GEMMs. The
formal cost is :math:`O(K^3\,n_\mathrm{grid})` and **no tensor larger than
:math:`K\times K` is ever allocated**. The only approximations are the THC
fit of the ERIs (reported in the output) and the Laplace quadrature
(:math:`\sim 10^{-7}` relative at the default spacing).

Exchange diagram (RI, exact denominators)
-----------------------------------------

The exchange index pattern :math:`(ia|jb)(ib|ja)` couples :math:`i` to both
:math:`a` and :math:`b` across the two factors, so the Laplace transform
does **not** decouple it -- under THC it stays :math:`O(K^4)`, which is not
viable at the target size. Instead the exchange diagram keeps the occupied
labels explicit and uses the density-fitted three-index integral
:math:`(ia|jb)=\sum_L L^L_{ia}L^L_{jb}` that BAGH already carries. Because
:math:`i` and :math:`j` are explicit, **no Laplace grid is needed** -- the
exact denominator is used directly:

.. math::

   E_\mathrm{exc}
     = \tfrac12 \sum_{i\le j} m_{ij}
       \sum_{ab}
       \frac{\operatorname{Re}\big[T^{ij}_{ab}\,(T^{ij}_{ba})^\ast\big]}
            {\varepsilon_a+\varepsilon_b-\varepsilon_i-\varepsilon_j},
   \qquad
   T^{ij}_{ab} = \sum_L L^L_{ia}\, L^L_{jb},

with :math:`m_{ij}=1` for :math:`i=j` and :math:`2` otherwise. Each pair
costs one :math:`v\times v` GEMM (contracting the auxiliary index) plus a
weighted Hermitian-cross trace; the formal cost is
:math:`O\!\big(o^2 (n_\mathrm{aux}+v)\,v^2\big)`, the same as conventional
RI-MP2 exchange, and the memory is :math:`O(v^2)` per pair. The exchange
term is therefore **numerically exact given the RI fit** -- it carries no
Laplace error at all.

With localized occupied orbitals (input ``DoLoc``) a Schwarz bound
:math:`s_{ij}=\sum_L\lVert L^L_{i\cdot}\rVert\,\lVert L^L_{j\cdot}\rVert`
screens the occupied pair list (``thc_mp2_pair_thresh``), reducing the pair
count from :math:`O(o^2)` towards :math:`O(o)` for extended systems.

Spin-component scaling
----------------------

Under spin--orbit coupling there is no exact :math:`S_z`, so a rigorous
opposite-/same-spin separation does not exist. The two diagrams above are
the Coulomb and exchange antisymmetrized contributions; scaling them
independently,

.. math::

   E_\mathrm{MP2} = c_\mathrm{os}\,E_\mathrm{dir}
                  + c_\mathrm{ss}\,E_\mathrm{exc},

reproduces the standard relativistic SCS-/SOS-MP2 approximations.
``scs_os`` and ``scs_ss`` set :math:`c_\mathrm{os}` and
:math:`c_\mathrm{ss}` (both ``1.0`` by default = plain MP2).
``scs_ss 0`` is SOS-MP2: the exchange term is skipped entirely and only
the :math:`O(K^3)` direct term is evaluated -- the choice for the largest
systems.

Implementation
==============

The driver is ``bagh_code.relccsd.thc_lt_mp2`` and the rate-limiting
contractions run in a compiled pybind11 extension,
``bagh_code/thc_mp2_cxx`` (module ``bagh_thc_mp2``), built off the top-level
``compile_all`` target exactly like the DLPNO C++ layer. Pure-NumPy
references of the identical equations are kept for validation and are used
automatically if the extension is not built.

THC factors
-----------

When the reference is run with ``THC True`` the X2CAMF driver builds the
collocation :math:`X^o`, :math:`X^v` and the core :math:`Z` and hands them
to the MP2 kernel. Otherwise ``build_isdf_factors_lowmem`` constructs them
from ``cderi.hdf5``: a grid-blocked evaluation of the two-component
collocation, interpolation-point selection by pivoted Cholesky with
on-demand metric columns (never an :math:`n_\mathrm{grid}\times
n_\mathrm{grid}` array), and a ridge-stabilized Cholesky least-squares
solve for :math:`Z`. For large :math:`K` the core is written to
``thc_mp2_Z.h5`` and streamed.

In-core and out-of-core direct term
-----------------------------------

``thc_mp2_mode`` selects how the direct term treats the :math:`K\times K`
matrices:

* ``incore`` -- :math:`M`, :math:`N`, and :math:`Z` resident; the C++
  kernel does the whole node loop.
* ``ooc`` -- only :math:`M` and :math:`W=MZ^\dagger` are resident and
  :math:`Z` is streamed in row panels from HDF5; peak RAM
  :math:`\approx 2K^2 + O(K\,n_\mathrm{mo})`.
* ``auto`` (default) -- ``incore`` when the resident set fits 80 % of free
  memory (or an explicit budget), else ``ooc``; a disk-backed :math:`Z`
  forces ``ooc``.

Frozen core
-----------

The standard ``fc`` / ``fc_no`` keywords apply. The occupied collocation
:math:`X^o`, the occupied energies, the RI integral :math:`L_{ia}`, and the
Laplace denominator window are all sliced to the active occupied space;
the virtual space is untouched. Frozen core acts identically on both
diagrams.

Formal scaling and memory
-------------------------

.. list-table::
   :header-rows: 1
   :widths: 24 40 36

   * - term
     - cost
     - peak memory
   * - direct (Coulomb)
     - :math:`O(K^3\,n_\mathrm{grid})`
     - :math:`O(K^2)` in-core; :math:`2K^2+O(K n_\mathrm{mo})` out-of-core
   * - exchange (RI)
     - :math:`O\!\big(o^2(n_\mathrm{aux}+v)\,v^2\big)`
     - :math:`O(v^2)` per pair
   * - ISDF factor build
     - :math:`O(K\,n_\mathrm{aux}\,n_\mathrm{mo}^2)`
     - :math:`O(K^2 + p\,n_\mathrm{mo}^2)`

Nothing scales as :math:`o^2v^2` or :math:`n_\mathrm{aux}n_\mathrm{mo}^2`.

Examples
========

Plain THC-LT-MP2 (both diagrams), X2CAMF spinor reference:

.. code-block:: shell

   ! THC-LT-MP2 SOC-X2CAMF spinor aug-cc-pvdz

   %cc
   cd True
   thc True
   lt_spacing 0.35
   end

   *xyz 0 1
   O  0.000000000000  -0.143225816552   0.000000000000
   H  1.638036840407   1.136548822547  -0.000000000000
   H -1.638036840407   1.136548822547  -0.000000000000

Frozen-core SOS-MP2 (direct term only -- the scalable large-system route),
with the direct term forced out-of-core:

.. code-block:: shell

   ! THC-LT-MP2 SOC-X2CAMF spinor aug-cc-pvtz

   %cc
   cd True
   thc True
   fc True
   fc_no -1
   scs_ss 0.0
   thc_mp2_mode ooc
   end

   *xyz 0 1
   Xe 0.0 0.0 0.0

SCS-style scaling with localized-orbital pair screening on the exchange:

.. code-block:: shell

   ! THC-LT-MP2 SOC-X2CAMF spinor cc-pvdz

   %cc
   cd True
   thc True
   DoLoc True
   scs_os 1.2
   scs_ss 0.33
   thc_mp2_pair_thresh 1e-8
   end

   *xyz 0 1
   ...

Validity and remarks
====================

* Requires ``cd True``. With ``thc True`` the THC factors come from the
  X2CAMF driver; without it they are built from the density-fitted ERIs by
  the internal ISDF routine.
* The direct term's error is the THC ERI fit plus the Laplace quadrature
  (:math:`\sim 10^{-6}`--:math:`10^{-7}` in the correlation energy at the
  default grid and ``lt_spacing``). The exchange term is exact given the
  RI fit -- no Laplace error.
* Validated against a dense density-fitted MP2 in the same X2CAMF spinor
  MO basis: the exchange diagram agrees to :math:`\sim 10^{-15}`, the
  direct diagram and the total to THC + LT accuracy; the in-core and
  out-of-core direct paths agree to :math:`\sim 10^{-14}`.
* Current ceilings: the exchange :math:`v\times v` work is not yet
  virtual-domain truncated, so full MP2 is practical to roughly
  :math:`v\sim 3000`--:math:`4000`; beyond that use ``scs_ss 0``
  (SOS-MP2), whose direct term scales to the full target. The fully
  out-of-core ISDF :math:`Z` solve is not wired -- ``build_isdf_factors_lowmem``
  raises a clear error when :math:`K\,n_\mathrm{mo}^2` exceeds the in-core
  limit; supply THC factors from ``THC True`` in that regime.
* Keywords: see :doc:`keyword` -- ``lt_spacing``, ``scs_os``, ``scs_ss``,
  ``thc_mp2_exchange``, ``thc_mp2_mode``, ``thc_mp2_pair_thresh`` plus the
  shared ``cd_threshold``, ``thc_c``, ``fc`` / ``fc_no``.
