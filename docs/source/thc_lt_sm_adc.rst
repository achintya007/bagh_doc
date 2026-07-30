THC-LT-SM-IP/EA-ADC: Semi-Empirical Mixed-Order ADC[(2)+x(3)]
################################################################

THC-LT-SM-IP-ADC and THC-LT-SM-EA-ADC blend the ADC(2) and ADC(3)
secular matrices of :doc:`thc_lt_adc3` (Bauer, Dempwolff, Rehn, Dreuw,
*J. Chem. Phys.* **156**, 144101 (2022)):

.. code-block:: text

   M_sm(x) = M^(2) + x (M^(3) - M^(2)) = (1-x) M^(2) + x M^(3)

with ``x=0`` reproducing IP-ADC(2)/EA-ADC(2) exactly and ``x=1``
reproducing IP-ADC(3)/EA-ADC(3) exactly. Intermediate ``x`` gives a
mixed-order method that is Hermitian, size-consistent, and yields
genuine wavefunctions/properties (not just an eigenvalue estimate) --
unlike naively averaging the ADC(2) and ADC(3) eigenvalues themselves
("ADC(2.5)"), which is neither Hermitian nor guaranteed to correspond to
any single well-defined Hamiltonian.

Theory
======

Because ``M^(2)`` and ``M^(3)`` share every lower-order block, the
matrix blend is realized exactly at the matvec level:

.. code-block:: text

   sigma_sm(x; r) = (1-x) sigma_ADC(2)(r) + x sigma_ADC(3)(r)

so no new algebra is needed beyond what :doc:`thc_lt_adc3` already
implements -- THC-LT-SM-IP-ADC and THC-LT-SM-EA-ADC build both the
ADC(2) and the ADC(3) static block and matvec from that same engine and
blend them at solve time. This means the cost is dominated by the
ADC(3) side (both matvecs are formed every iteration), so there is no
speed advantage over plain THC-LT-IP/EA-ADC(3) at a given ``x`` -- the
point of the method is the mixed-order *physics* (tunable between
ADC(2) and ADC(3) with a single Hermitian, size-consistent operator),
not reduced cost.

Validity
========

Exact at the endpoints (``x=0``/``x=1`` reproduce ADC(2)/ADC(3)
respectively to the same THC+LT accuracy as :doc:`thc_lt_adc3`).
Away from the endpoints, ``M_sm(x)`` is a legitimate Hermitian operator
for any ``x``, but it is a *semi-empirical* interpolation, not a
first-principles perturbation-theory result at fractional order --
choosing ``x`` (typically by comparison to a trusted reference for a
similar system) is part of using the method, the same way choosing a
DFT functional's mixing parameter is.

Keywords
========

**thc_x_sm** ``Float``

.. code-block:: shell

   thc_x_sm 0.5

The blend parameter ``x`` (``0`` = ADC(2), ``1`` = ADC(3)). Shared by
both THC-LT-SM-IP-ADC and THC-LT-SM-EA-ADC.

All :doc:`thc_lt_adc3` keywords (``nroots``, ``adc_convergence``,
``thc_fno``/``thc_fno_thresh``, ``Debug``) apply unchanged; with
``Debug True`` the endpoint solves (``x=0``, ``x=1``) are additionally
checked against PySCF ADC(2)/ADC(3) in the same orbital space, and the
``x=0.5`` matrix blend is compared against the naive eigenvalue-averaged
"ADC(2.5)" (expect a genuine few-mHa difference between the two --
that difference is the point of the method, not an error; see
:doc:`thc_lt_adc3` for the EA-specific caveat about root reordering
across very different orders making that particular comparison noisy
for individual roots).

MPI parallelism
================

Not yet available for either method (both stay on a single rank if
launched under ``mpirun`` -- see :doc:`parallel_execution`).

Examples
========

IP
---

.. code-block:: shell

   ! THC-LT-SM-IP-ADC cc-pvdz cc-pvdz-ri

   %cc
   nroots 3
   thc_x_sm 0.5
   end

   *xyz 0 1
   O 0 0 0
   H 0 0 1
   H 0 1 0
   *

which gives:

.. code-block:: shell

   system: o=5 v=19 naux=84  N_thc=278  n_LT=18  method=sm-ADC[(2)+x(3)]

   THC+LT sm-IP-ADC[(2)+0.5(3)]   conv=True
   root 1  IP = 0.424281 a.u.  11.5453 eV
   root 2  IP = 0.536189 a.u.  14.5904 eV
   root 3  IP = 0.624978 a.u.  17.0065 eV

Compare against :doc:`thc_lt_adc3`'s IP-ADC(3) example on the same
system (0.447585/0.555821/0.637402 a.u.) and THC-LT-IP-ADC(2)'s implicit
``x=0`` endpoint -- the ``x=0.5`` roots sit between the two, as expected.

EA
---

.. code-block:: shell

   ! THC-LT-SM-EA-ADC cc-pvdz cc-pvdz-ri

   %cc
   nroots 3
   thc_x_sm 0.5
   end

   *xyz 0 1
   O 0 0 0
   H 0 0 1
   H 0 1 0
   *

which gives:

.. code-block:: shell

   system: o=5 v=19 naux=84  N_thc=278  n_LT=18  method=sm-ADC[(2)+x(3)]

   THC+LT sm-EA-ADC[(2)+0.5(3)]   conv=True
   root 1  EA = 0.154290 a.u.  4.1984 eV
   root 2  EA = 0.232511 a.u.  6.3269 eV
   root 3  EA = 0.639928 a.u.  17.4133 eV
