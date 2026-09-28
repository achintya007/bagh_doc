Algebraic Diagrammatic Construction (ADC)
########################################

The Algebraic Diagrammatic Construction (ADC) is a post-Hartree Fock ab-initio Hermitian method to calculate vertical ionization potential (IP), electron-attachment energy (EA), excitation energy (EE), and vertical double ionization potential (DIP) in a direct-difference of energy based way. ADC is implemented using Intermediate State
Representation (ISR) in BAGH. The ADC secular matrix is expanded in perturbation series. A truncation at nth order leads to ADC(n) method. This method incorporates correlation from its second-order (ADC(2) method). An extender version of ADC(2), namely ADC(2)-X is considered to be an ad-hoc extension up to the first order terms in doubles-doubles block (2h1p-2h1p for IP and 2p1h-2p1h for EA). In BAGH, ADC(n) is implemented up to 3rd order, i.e. ADC(3).

ADC being a Hermitian method calculates transition and excitate state properties very efficiently. In
the current version of BAGH, transition dipole moment (TDM) and excited state dipole moment
(EXDM) can be calculated using the EE-ADC(n) method by setting the keyword ``tdm`` and ``exdm`` to ``True`` in the ``%CC`` block.

The available names of the methods and keywords to access them are listed below:

+---------------------+---------------------+---------------------+-----------------+-----------------+
|      Method         |        IP           |         EA          |     EE          |      DIP        |
+=====================+=====================+=====================+=================+=================+
|    ADC(2)           |        IP-ADC(2)    |      EA-ADC(2)      |      EE-ADC(2)  |    DIP-ADC(2)   |
+---------------------+---------------------+---------------------+-----------------+-----------------+
|    ADC(2)-X         |        IP-ADC(2)-X  |    EA-ADC(2)-X      |     EE-ADC(2)-X |    DIP-ADC(2)-X |
+---------------------+---------------------+---------------------+-----------------+-----------------+
|    ADC(3)           |        IP-ADC(3)    |      EA-ADC(3)      |      EE-ADC(3)  |    DIP-ADC(3)   |
+---------------------+---------------------+---------------------+-----------------+-----------------+

A sample input file is given below for user’s reference for a relativistic 4-component ADC(3) calculation of the HF molecule to compute excitation energies.

.. code-block:: shell 

   ! EE-ADC(3) spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   nroots 4
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

| ``EE-ADC(3)``: Name of the method. Activates ADC(3) excitation energy functionality.
| ``spinor``: 4-component relativistic interface.
| ``unc-ccpvdz``: Name of the basis set (uncontracted cc-pVDZ).
| ``incore 5``: All the two-electron integrals are in the memory. 
| ``nroots 4``: 4 excitation energies will be calculated.

With the above computational procedure, we obtain the following excitation energies and the corresponding dominant transitions:

.. code-block:: shell 

   --------ADC values-----------


  Root: 1 	 EE-ADC value: 0.376378 a.u. 	  10.241999 eV 	
  Dominant Transition
  8 --> 10 0.47 	
  8 --> 11 0.48 	
  8 --> 12 0.10 	
  8 --> 13 0.10 	
  9 --> 10 0.44 	
  9 --> 11 0.51 	
  9 --> 13 0.11 	
  
  
  Root: 2 	 EE-ADC value: 0.376378 a.u. 	  10.241999 eV 	
  Dominant Transition
  8 --> 10 0.51 	
  8 --> 11 0.44 	
  8 --> 12 0.11 	
  9 --> 10 0.48 	
  9 --> 11 0.47 	
  9 --> 12 0.10 	
  9 --> 13 0.10 	
  
  
  Root: 3 	 EE-ADC value: 0.377020 a.u. 	  10.259456 eV 	
  Dominant Transition
  6 --> 11 0.28 	
  7 --> 10 0.59 	
  7 --> 12 0.13 	
  8 --> 10 0.20 	
  8 --> 11 0.45 	
  9 --> 10 0.22 	
  9 --> 11 0.43 	
  
  
  Root: 4 	 EE-ADC value: 0.377020 a.u. 	  10.259456 eV 	
  Dominant Transition
  6 --> 11 0.59 	
  6 --> 13 0.13 	
  7 --> 10 0.28 	
  8 --> 10 0.43 	
  8 --> 11 0.22 	
  9 --> 10 0.45 	
  9 --> 11 0.20

A similar type of the above input file can be used for IP and EA also. One just needs to change the name of the method in the input file.

********************************************************
Transition dipole moment and excited state dipole moment
********************************************************

To get the transition dipole moment and excited state dipole moment, two extra keywords that have to be added in the %cc block are ``tdm`` and ``exdm``. A example input file is shown below:

.. code-block:: shell 

   ! EE-ADC(3) spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   nroots 10
   tdm True
   exdm True
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

By switching on the ``tdm`` and ``exdm`` we obtain:

.. code-block:: shell

               ************************ Transition Dipole Moment *****************************
			-------------------------------------------------------------------------------------------

			         ABSORPTION SPECTRUM VIA TRANSITION ELECTRIC DIPOLE MOMENTS

			-------------------------------------------------------------------------------------------

                         State     Energy      Wavelength      fosc         T2          TX        TY         TZ
                                   (cm-1)        (nm)                    (au**2)       (au)      (au)       (au)      
			-------------------------------------------------------------------------------------------

			   1       82593.6       121.075      0.00000     0.00000     0.00000   0.00000   0.00000
			   2       82593.6       121.075      0.00000     0.00000     0.00000   0.00000   0.00000
			   3       82734.1       120.869      0.00001     0.00003     0.00466   0.00708   0.00000
			   4       82734.1       120.869      0.00001     0.00002     0.00691   0.00477   0.00000
			   5       82883.0       120.652      0.00000     0.00000     0.00000   0.00000   0.00000
			   6       82883.7       120.651      0.00001     0.00003     0.00000   0.00000   0.00555
			   7       87742.9       113.969      0.02065     0.07750     0.27840   0.00262   0.00000
			   8       87742.9       113.969      0.02171     0.08145     0.00256   0.28541   0.00000
			   9      109722.6        91.139      0.00000     0.00000     0.00000   0.00000   0.00000
			  10      109722.8        91.139      0.00000     0.00000     0.00162   0.00001   0.00000

      MP2 contribution (Electronic) to ground state dip moment in a.u. [0.000000000000125 0.000000000000000   0.043739251678833]

              ************************ Excited State Dipole Moment *****************************
			-------------------------------------------------------------------------------------------

                         State     Energy      Wavelength      fosc         T2          TX        TY         TZ
                                   (cm-1)        (nm)                    (au**2)       (au)      (au)       (au)      
			-------------------------------------------------------------------------------------------

			   1       82593.6       121.075      0.66368     2.64537     0.00000   0.00000   1.62646
			   2       82593.6       121.075      0.66368     2.64537     0.00000   0.00000   1.62646
			   3       82734.1       120.869      0.66479     2.64532     0.00000   0.00000   1.62644
			   4       82734.1       120.869      0.66479     2.64532     0.00000   0.00000   1.62644
			   5       82883.0       120.652      0.66598     2.64527     0.00000   0.00000   1.62643
			   6       82883.7       120.651      0.66595     2.64512     0.00000   0.00000   1.62638
			   7       87742.9       113.969      0.71816     2.69455     0.00000   0.00000   1.64151
			   8       87742.9       113.969      0.71816     2.69455     0.00000   0.00000   1.64151
			   9      109722.6        91.139      0.77565     2.32727     0.00000   0.00000   1.52554
			  10      109722.8        91.139      0.77564     2.32724     0.00000   0.00000   1.52553

In the excited state dipole moment section, a change in the dipole moment due to the excitation from ground to excited is listed. To get the total dipole moment for a particular excited state, MP2 contribution needs to be included. 

.. _adc-response-section:

*****************************************************************************
Response properties: polarizability and first and second hyperpolarizability
*****************************************************************************

EE-ADC(n) in BAGH gives access to linear and nonlinear electric response
properties of the ground state, and to the polarizability of an excited state,
through the intermediate state representation (ISR). All of them are available
for the relativistic (4c ``spinor`` and 2c X2CAMF) EE-ADC(2) reference and are
evaluated with the ISR(2) modified transition moments :math:`\mathbf{F}` and the
ISR(2) :math:`\mathbf{B}`-matrix of the dipole operator. Instead of summing over
excited states, each property is written with the inverse shifted ADC matrix,
and only linear response equations :math:`(\mathbf{M}-z)\mathbf{X}=\mathbf{R}` are
solved (Papapostolou, Scheurer, Dreuw and Rehn, *J. Chem. Theory Comput.* **19**,
6375 (2023), doi:10.1021/acs.jctc.3c00456).

First hyperpolarizability (quadratic response), summed over the six
permutations :math:`\hat{P}` of the pairs :math:`(A,-\omega_\sigma)`,
:math:`(B,\omega_1)`, :math:`(C,\omega_2)` with :math:`\omega_\sigma=\omega_1+\omega_2`:

.. math::

    \beta_{ABC}(-\omega_\sigma;\omega_1,\omega_2) = \sum \hat{P}\;
    \mathbf{F}^\dagger(\hat\mu_A)\,(\mathbf{M}-\omega_\sigma)^{-1}\,
    \mathbf{B}(\hat\mu_B)\,(\mathbf{M}-\omega_2)^{-1}\,\mathbf{F}(\hat\mu_C)

Second hyperpolarizability (cubic response), summed over the 24 permutations of
:math:`(A,-\omega_\sigma)`, :math:`(B,\omega_1)`, :math:`(C,\omega_2)`,
:math:`(D,\omega_3)`:

.. math::

    \gamma_{ABCD} = \sum \hat{P}\Big[
    \mathbf{F}^\dagger_A(\mathbf{M}-\omega_\sigma)^{-1}\mathbf{B}_B
    (\mathbf{M}-\omega_2-\omega_3)^{-1}\mathbf{B}_C(\mathbf{M}-\omega_3)^{-1}\mathbf{F}_D
    - \mathbf{F}^\dagger_A(\mathbf{M}-\omega_\sigma)^{-1}\mathbf{F}_B\;
    \mathbf{F}^\dagger_C(\mathbf{M}-\omega_3)^{-1}(\mathbf{M}+\omega_2)^{-1}\mathbf{F}_D\Big]

The second term removes the secular divergence, so :math:`\gamma` is finite in
the static limit and for equal frequencies. For spinors every quantity is
complex: a left vector :math:`\mathbf{F}^\dagger(\mathbf{M}-z)^{-1}` is obtained
as :math:`[(\mathbf{M}-z^*)^{-1}\mathbf{F}]^\dagger`, and the relation
:math:`\mathbf{X}^\dagger=\mathbf{X}^T` of the real-orbital case is never used.
Each response vector is solved once and reused over all permutations and tensor
components (for SHG, four response vectors, i.e. 12 equations).

With ``adc_damping`` :math:`\gamma>0` the complex response is computed: every
incident frequency becomes :math:`\omega_j+i\gamma`, which turns each resolvent into
:math:`(\mathbf{M}-\Omega-i\gamma\,\mathrm{sign}\,\Omega)^{-1}` and reproduces the damped
sum-over-states expression term by term.

The following processes are available (``adc_beta_process`` and
``adc_gamma_process``, several can be given as a comma-separated list; the
incident frequency :math:`\omega` is taken from ``omega``, ``omega_list`` or
``omega1``/``omega2``/``omega_step``):

.. list-table::
   :header-rows: 1
   :widths: 18 40 42

   * - keyword value
     - tensor
     - process
   * - ``static``
     - :math:`\beta(0;0,0)`, :math:`\gamma(0;0,0,0)`
     - static limit
   * - ``SHG``
     - :math:`\beta(-2\omega;\omega,\omega)`
     - second-harmonic generation
   * - ``EOPE``
     - :math:`\beta(-\omega;\omega,0)`
     - electro-optical Pockels effect
   * - ``OR``
     - :math:`\beta(0;\omega,-\omega)`
     - optical rectification
   * - ``ESHG``
     - :math:`\gamma(-2\omega;\omega,\omega,0)`
     - electric-field-induced second-harmonic generation
   * - ``THG``
     - :math:`\gamma(-3\omega;\omega,\omega,\omega)`
     - third-harmonic generation
   * - ``IDRI``
     - :math:`\gamma(-\omega;\omega,-\omega,\omega)`
     - intensity-dependent refractive index
   * - ``EOKE``
     - :math:`\gamma(-\omega;\omega,0,0)`
     - electro-optical Kerr effect
   * - ``dc-OR``
     - :math:`\gamma(0;\omega,-\omega,0)`
     - dc optical rectification

For every process the full tensor is printed, together with the experimentally
relevant averages (all in atomic units):

.. math::

    \beta_\parallel = \frac{1}{5}\sum_{A}\frac{\mu_A}{|\mu|}\sum_B(\beta_{ABB}+\beta_{BAB}+\beta_{BBA}),\qquad
    \gamma_\parallel = \frac{1}{15}\sum_{AB}(\gamma_{AABB}+\gamma_{ABBA}+\gamma_{ABAB}),

:math:`\gamma_\perp=\frac{1}{15}\sum_{AB}(2\gamma_{ABBA}-\gamma_{AABB})` and
:math:`\gamma_K=\frac{3}{2}(\gamma_\parallel-\gamma_\perp)`. :math:`\beta_\parallel`
is the component along the MP2 ground-state dipole moment, which is printed as
well. The values are also written, one line per process and frequency, to
``beta_hyperpolarizability_rel.dat`` and ``gamma_hyperpolarizability_rel.dat``.

The ground-state polarizability (``adc_pol``) and the excited-state
polarizability of the states listed in ``state`` (``adc_es_pol``) use the same
:math:`\mathbf{M}`, :math:`\mathbf{F}` and :math:`\mathbf{B}`; they are written to
``alpha_polarizability_rel.dat`` and ``es_polarizability_rel.dat``.

A sample input for the static and SHG first hyperpolarizability, the static and
ESHG second hyperpolarizability and the dynamic polarizability of water at
:math:`\omega` = 0.0428 a.u. (1064 nm) is given below. ``light_speed 50000``
switches off relativistic effects, which makes the numbers directly comparable
with non-relativistic ADC(2) codes.

.. code-block:: shell

   ! EE-ADC(2) spinor 631g

   %cc
   light_speed 50000
   adc_pol True
   adc_beta True
   adc_gamma True
   adc_beta_process static,SHG
   adc_gamma_process static,ESHG
   incore 5
   NRoots 1
   omega 0.0428
   resp_convergence 1e-8
   end

   *xyz 0 1
   O  0.000000  0.000000  0.117300
   H  0.000000  0.757200 -0.469200
   H  0.000000 -0.757200 -0.469200

| ``adc_pol True``: ground-state polarizability at ``omega``.
| ``adc_beta True`` / ``adc_gamma True``: first / second hyperpolarizability.
| ``adc_beta_process`` / ``adc_gamma_process``: processes to compute (default ``SHG`` / ``ESHG``).
| ``omega 0.0428``: incident frequency in a.u. (``omega_list 0.0,0.0428`` for several).
| ``resp_convergence``: residual threshold of the response equations; use 1e-6 or tighter for :math:`\gamma`.
| ``adc_damping``: optional damping parameter :math:`\gamma` in a.u. for the complex response.

For this input BAGH prints, after the tensor components of each process,

.. code-block:: shell

    beta vector (1/5 sum_B beta_ABB+beta_BAB+beta_BBA): -0.000011  0.000060  -31.883618
    beta_par = -31.883618 (a.u.)
    ...
    gamma_par  = 189.731342 (a.u.)
    gamma_perp = 63.090043 (a.u.)
    gamma_K    = 189.961949 (a.u.)

(adcc: :math:`\beta_\parallel` = -31.883555, :math:`\gamma_\parallel` = 189.731803 a.u.).

**Validation.** In the non-relativistic limit the ISR(2) results agree with
adcc (version 0.16.1) to the convergence threshold of the response equations
(H\ :sub:`2`\ O, same geometry and basis):

.. list-table::
   :header-rows: 1
   :widths: 60 20 20

   * - H\ :sub:`2`\ O, ADC(2)/aug-cc-pVDZ
     - BAGH
     - adcc
   * - :math:`\bar\alpha(0;0)`
     - 9.683
     - 9.683
   * - :math:`\beta_\parallel(0;0,0)`
     - -24.366
     - -24.366
   * - :math:`\beta_\parallel(-2\omega;\omega,\omega)`, :math:`\omega` = 0.0428
     - -26.547
     - -26.547
   * - :math:`\gamma_\parallel(0;0,0,0)`
     - 1100.46
     - 1100.45
   * - :math:`\gamma_\parallel(-2\omega;\omega,\omega,0)`, :math:`\omega` = 0.0428
     - 1261.76
     - 1261.75

.. note::

   For spinors the ISR(2) :math:`\mathbf{F}` (``mtm_adc2_all_dirs``) and
   :math:`\mathbf{B}` (``bmatrix``) contain the MP1 amplitudes both as ket
   amplitudes :math:`t = -(t_2^{(1)})^*` and as their complex conjugates. The
   position of every conjugate follows from the requirement that each term
   transforms like the ADC vector under an arbitrary phase change of the
   spinors, :math:`\phi_p\rightarrow e^{i\theta_p}\phi_p`. With this choice
   :math:`\mathbf{B}` is Hermitian and the results do not depend on the spinor
   phases. The response equations use the Hermitian ``eeadc_matvec_updated``.
   (Before September 2026 the relativistic ADC polarizability used real-orbital
   expressions, which gave, e.g., 9.105 a.u. instead of 9.683 a.u. for water.)


.. _dip-section:
***********************************
Double Ionization Potential (DIP)
***********************************

A sample input file is given below for the user’s reference for a relativistic 4-component DIP-ADC(3) calculation of the HF molecule.

.. code-block:: shell 

   ! DIP-ADC(3) spinor unc-ccpvdz

   %cc
   numproc 4
   incore 5
   real_ints True
   nroots 5
   rootno 0,3
   adc_convergence 1e-06
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

| ``nroots 5``: 5 DIP-CIS energies will be printed.
| ``rootno 0,3``: instructs the code to apply the DIP-ADC(3) procedure specifically to the 1st and 4th states (counting from zero) among the previously computed DIP-CIS roots.

With the above computational procedure, we obtain the following DIP energies and the corresponding dominant transitions:

.. code-block:: shell 

	--------DIP-ADC(3) values-----------


	Root: 1 	 DIP value: 1.7795271 a.u. 	  48.423424 eV 	
	Dominant Transition
	6 7 --> 0.46234
	8 9 --> -0.47157


	Root: 4 	 DIP value: 1.8931260 a.u. 	  51.514607 eV 	
	Dominant Transition
	4 8 --> 0.29878
	4 9 --> -0.36062
	5 8 --> 0.36061
	5 9 --> 0.29877





