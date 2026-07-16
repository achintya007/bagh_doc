Plasma-Embedded Systems: Debye Screening and the Ion-Sphere Model
##################################################################

BAGH can describe atoms and molecules embedded in a plasma environment
within the relativistic (spinor X2C) framework, using two standard
plasma models: **Debye-Hueckel screening** for weakly coupled plasmas
and the **ion-sphere model (ISM)** for dense, strongly coupled plasmas.
Both models are available for the ``SOC-X2CAMF`` interface and work
with every Cholesky-decomposition based correlation method
(MP2, CCSD, CCSD(T), EOM-CCSD, ADC, ...).

The one-electron model potential enters the four-component Dirac
Hamiltonian *before* the X2C decoupling, i.e. both the large-large
potential block ``V`` and the small-small block
``W = (sigma.p) V (sigma.p)`` are modified, so the screening is
picture-change consistent. The recommended Hamiltonian is the
one-electron X2C variant, requested with ``x2c_type 1e``.

Debye-Hueckel screening
========================

In a weakly coupled plasma every Coulomb interaction is screened
exponentially with the Debye length :math:`\lambda_D`
(:math:`\mu = 1/\lambda_D`):

.. code-block:: text

   V_en(r)  = - sum_A  Z_A exp(-mu r_A)/r_A
   g(r_12)  =   exp(-mu r_12)/r_12
   E_nn     =   sum_{A<B}  Z_A Z_B exp(-mu R_AB)/R_AB

All three interactions (electron-nucleus, electron-electron and
nucleus-nucleus) are screened consistently.

The screened (Yukawa) two-electron integrals are generated in the
scalar AO basis and **Cholesky decomposed** once, exactly as in the
standard CD-based X2CAMF implementation. The same Cholesky vectors are
used to build the Coulomb and exchange matrices during the spinor SCF
and are then passed on to the correlation modules, so the SCF and all
post-SCF steps see the same screened interaction. For this reason the
Debye model requires ``CD True``.

If the underlying libcint library was compiled with F12 support, the
Yukawa integrals are evaluated analytically (``int2e_yp``). Otherwise
BAGH uses the exact identity

.. math::

   \frac{e^{-\mu r}}{r} \;=\; \frac{1}{r}
   \;-\; \int_0^\infty du\, e^{-u}\,
   \frac{\mathrm{erf}\!\left(\mu r/2\sqrt{u}\right)}{r}

discretized with a geometrically convergent quadrature, so the Yukawa
integrals become a linear combination of standard long-range
(erf-attenuated) Coulomb integrals that every PySCF build supports.
The default quadrature spacing (``debye_quad_h 0.25``, about 55 nodes)
keeps the kernel error below :math:`2\times 10^{-7}`; ``debye_quad_h
0.2`` tightens it to :math:`10^{-9}`.

Example: Debye-screened CCSD for a magnesium atom
--------------------------------------------------

.. code-block:: shell

   ! CCSD SOC-X2CAMF spinor ccpvdz

   %cc
   CD True
   cd_threshold 1e-6
   x2c_type 1e
   plasma debye
   debye_length 10.0
   end

   *xyz 0 1
   Mg 0.0 0.0 0.0

The Debye length is given in bohr; alternatively the screening
parameter :math:`\mu` can be given directly with ``debye_mu``.
Excited-state and ionized-state methods work unchanged, e.g.
``! IP-EOM-CCSD SOC-X2CAMF spinor ccpvdz`` with ``nroots``.

Ion-sphere model
=================

For an atomic ion of nuclear charge :math:`Z` with :math:`N` bound
electrons embedded in a dense plasma, the ISM confines the ion and the
:math:`n_f = Z - N` neutralizing free electrons (spread uniformly) in
a sphere of radius :math:`R`:

.. math::

   V(r) \;=\; -\frac{Z}{r} \;+\;
   \frac{n_f}{2R}\left[\,3 - \left(\frac{r}{R}\right)^{2}\right],
   \qquad r \le R

(outside the sphere the background potential continues as
:math:`n_f/r`). The electron-electron interaction remains pure
Coulomb, so the ISM works with and without ``CD``. The ion-sphere
radius is related to the free-electron density by
:math:`\tfrac{4}{3}\pi R^3 n_e = Z - N`; BAGH prints the density
corresponding to the given radius.

The constant (orbital-independent) ISM energy terms, the
nucleus-background interaction :math:`-3 Z n_f/2R` and the background
self-energy :math:`+3 n_f^2/5R`, are printed in the output but **not**
added to the total electronic energy; add them by hand when comparing
total energies between different plasma conditions.

Example: ion-sphere CCSD for Ne-like Al\ :sup:`3+`
---------------------------------------------------

.. code-block:: shell

   ! CCSD SOC-X2CAMF spinor ccpvdz

   %cc
   CD True
   cd_threshold 1e-6
   x2c_type 1e
   plasma ionsphere
   is_radius 4.0
   end

   *xyz 3 1
   Al 0.0 0.0 0.0

Numerical details
==================

The one-electron model potentials are evaluated as an analytic
long-range part (:math:`\mathrm{erf}(\sqrt{\zeta}r)/r`, computed with
Gaussian-charge ``rinv`` integrals) plus a smooth, exponentially
compact residual integrated on a DFT grid. The screening parameter
:math:`\zeta` is chosen such that the residual vanishes at every
nucleus, so the grid never sees a Coulomb singularity or a long-range
tail; the resulting matrix elements are insensitive to the grid at the
:math:`10^{-11}` level even for heavy atoms in fully decontracted
basis sets. The defaults rarely need to be changed; if desired the
grid can be controlled with ``plasma_grid_level`` (molecules) or
``plasma_atom_grid`` (atoms, default ``250,590``).

Validity and remarks
=====================

* Implemented for the ``SOC-X2CAMF`` interface. With ``x2c_type``
  other than ``1e`` the plasma potential still enters the one-electron
  4c Hamiltonian, but any atomic-mean-field two-electron/spin-orbit
  picture-change correction remains unscreened; ``x2c_type 1e`` is the
  fully consistent choice.
* Point nuclei are assumed for the screened nuclear attraction.
* The Debye model requires ``CD True`` (the screened two-electron
  integrals are handled through the Cholesky vectors). The ISM has no
  such restriction.
* The ion-sphere model is defined for a single atomic ion with
  :math:`Z > N` (positive ions).

Keywords
=========

See the :doc:`keyword` page: ``plasma``, ``debye_length``,
``debye_mu``, ``is_radius``, ``plasma_grid_level``,
``plasma_atom_grid``, ``debye_quad_h``.
