Relativistic Framework
######################

*************************
1. Four-component Methods
*************************
For the generation of one- and two-electron integrals and the converged four-component DHF spinors, BAGH is currently interfaced with PySCF and DIRAC.
For the PySCF interface, use the keyword 'spinor'

*******************
Ground State Energy
*******************
================================
Coupled Cluster (CC)
================================
 .. math::

    |\Psi_{cc} \rangle = e^{\hat{T}} |\Phi_{0} \rangle


Coupled Cluster Singles Doubles (CCSD)
--------------------------------------
.. code-block:: shell 

   ! CCSD spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

You can control the CCSD energy covergence using the keyword 'cc_convergence'

Coupled Cluster Singles Doubles with perturbative Triples (CCSD(T))
-------------------------------------------------------------------

.. code-block:: shell 

   ! CCSD(T) spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

.. only:: comment
    Coupled Cluster Singles Doubles Triples (CCSDT)
    -----------------------------------------------
    Not Implemented

.. only:: comment
   Coupled Cluster approximate Doubles (CC2)
   -----------------------------------------
Coupled Cluster approximate Triples (CC3)
----------------------------------------

.. code-block:: shell 

   ! CC3 spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

===================================
Unitary Coupled Cluster (UCC)
===================================
Third-order unitary Coupled Cluster (UCC3)
------------------------------------------

.. code-block:: shell 

   ! UCC3 spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Quadratic Unitary Coupled Cluster (qUCCSD)
------------------------------------------

.. code-block:: shell 

   ! QUCCSD spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Quadratic Unitary Coupled Cluster Triples (qUCCSD[T])
-----------------------------------------------------

.. code-block:: shell 

   ! QUCCSD[T] spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

****************************
Beyond Ground State Methods
****************************
The following methods are also available in BAGH.

+---------------------+---------------------+---------------------+-----------------+-----------------+
|      Method         |        IP           |         EA          |     EE          |      DIP        |
+=====================+=====================+=====================+=================+=================+
|   EOM-CCSD          |        YES          |      YES            |      YES        |      YES        |
+---------------------+---------------------+---------------------+-----------------+-----------------+
|    EOM-CC3          |      **---**        |     **---**         |      YES        |    **---**      |
+---------------------+---------------------+---------------------+-----------------+-----------------+
|    ADC(2)           |        YES          |      YES            |      YES        |      YES        |
+---------------------+---------------------+---------------------+-----------------+-----------------+
|    ADC(2)-X         |        YES          |      YES            |      YES        |      YES        |
+---------------------+---------------------+---------------------+-----------------+-----------------+
|    ADC(3)           |        YES          |      YES            |      YES        |      YES        |
+---------------------+---------------------+---------------------+-----------------+-----------------+
|    UCC3             |        YES          |     **---**         |      YES        |    **---**      |
+---------------------+---------------------+---------------------+-----------------+-----------------+
|    qUCCSD           |       **---**       |     **---**         |      YES        |    **---**      |
+---------------------+---------------------+---------------------+-----------------+-----------------+

==================================================
Equation of Motion Coupled Cluster (EOM-CC)
==================================================
EOM-Coupled Cluster Singles Doubles (EOM-CCSD)
---------------------------------------------
To calculate excitation energy in the EOM-CCSD framework, the following input format can be used.

.. code-block:: shell 

   ! EE-EOM-CCSD spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   eom_convergence 1e-6
   nroots 10
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Similarly, for ionization potential (IP), one needs to change the name of the method to ``IP-EOM-CCSD``; for example

.. code-block:: shell 

   ! IP-EOM-CCSD spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   eom_convergence 1e-6
   nroots 10
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

For electron affinity (EA), the name of the method should be replaced with ``EA-EOM-CCSD``

.. code-block:: shell 

   ! EA-EOM-CCSD spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   cc_convergence 1e-7
   eom_convergence 1e-6
   nroots 10
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

.. only:: comment

   EOM-Coupled Cluster approximate Doubles (EOM-CC2)
   ------------------------------------------------

EOM-Coupled Cluster approximate Triples (EOM-CC3)
------------------------------------------------

.. code-block:: shell 

   ! EE-EOM-CC3 spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   cc_convergence 1e-7
   eom_convergence 1e-6
   nroots 10
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

===========================================
Excited state using Unitary Coupled Cluster
===========================================
Third-order unitary Coupled Cluster (UCC3)
------------------------------------------

.. code-block:: shell 

   ! EE-UCC3 spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   ucc_convergence 1e-6
   nroots 10
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

To calculate the ionization potential in the UCC framework, one can write ``IP-UCC3`` in place of the method in the input file.

Quadratic unitary Coupled Cluster (qUCCSD)
------------------------------------------

.. code-block:: shell 

   ! EE-QUCCSD spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   ucc_convergence 1e-6
   nroots 10
   end

 

  *xyz 0 1
  H 0.0 0.0 0.0
  F 0.0 0.0 0.9168

================================================
Algebraic Diagrammatic Construction Theory (ADC)
================================================
Second order ADC (ADC(2))
-------------------------

.. code-block:: shell 

   ! EE-ADC(2) spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   nroots 10
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Second order-extended ADC (ADC(2)-X)
------------------------------------

.. code-block:: shell 

   ! EE-ADC(2)-X spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   nroots 10
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Third order ADC (ADC(3))
----------------------

.. code-block:: shell 

   ! EE-ADC(3) spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   nroots 10
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168


To calculate the ionization potential, electron affinity, and double ionization potential in the ADC framework, one can write ``IP-ADC(2)``, ``IP-ADC(2)-X``, ``IP-ADC(3)``, ``EA-ADC(2)``, ``EA-ADC(2)-X``, ``EA-ADC(3)``, ``DIP-ADC(2)``,  ``DIP-ADC(2)-X``, and  ``DIP-ADC(3)`` in place of method in the input file.

*******************
Low-Cost Techniques
*******************
============================
Frozen Natural Spinors (FNS)
============================
To truncate the virtual space, frozen natural spinors (FNS) generated out of a one-body reduced correlated density matrix can be used. There are two types of truncation criteria:

1. Occupation number: It uses the exact value of the occupation number to truncate the virtual space. Use the keyword fnothresh for this criterion. By default, fnothresh is zero (0), which means no truncation at all.

.. code-block:: shell 

   ! FNO-CCSD spinor unc-ccpvdz

   %cc
   incore 5
   fnothresh 1e-5
   cc_convergence 1e-7
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

2. Percentage of virtual orbital (povo): It uses the percentage of the virtual space to keep. Use the keyword povo for this criterion.

.. code-block:: shell 

   ! FNO-CCSD spinor unc-ccpvdz

   %cc
   incore 5
   povo 50
   cc_convergence 1e-7
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

A similar input file can also be made by using UCC

1. Occupation number: It uses the exact value of the occupation number to truncate the virtual space. Use the keyword fnothresh for this criterion. By default, fnothresh is zero (0), which means no truncation at all.

.. code-block:: shell 

   ! FNO-UCC3 spinor unc-ccpvdz

   %cc
   incore 5
   fnothresh 1e-5
   cc_convergence 1e-7
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168


2. Percentage of virtual orbital (povo): It uses the percentage of the virtual space to keep. Use the keyword povo for this criterion.

.. code-block:: shell 

   ! FNO-QUCCSD spinor unc-ccpvdz

   %cc
   incore 5
   povo 50
   cc_convergence 1e-7
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

To calculate the triple correction in the unitary coupled cluster framework, replace FNO-QUCCSD with FNO-QUCCSD[T].


==============================================
State-Specific Frozen Natural Spinors (SS-FNS)
==============================================

EE-ADC(2) reduced density in the virtual-virtual block can also be added to the MP2 reduced density (same block) to form State-Specific reduced density, leading to State-Specific Frozen Natural Spinors (SS-FNS).

.. math::

    D^{SS}_{ab} = D^{MP2}_{ab} + D^{EE-ADC(2)}_{ab}

A sample input file is given below. The keyword ``DoADC2 True`` is mandatory in the ``%cc`` block for the SS-FNS calculations.

.. code-block:: shell

   ! SS-FNO-EE-EOM-CCSD spinor unc-ccpvdz
   
   %cc
   DoADC2 True
   incore 5
   real_ints True
   nroots 2
   DoLambda True
   end
   
   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

**********
Properties
**********
=====================
First order property
=====================

Transition dipole moment using expectation value approach:
----------------------------------------------------------
The ground-to-excited state transition moment in the EOM-CCSD framework can be expressed as

.. math::

    {\left| {{\mu _{o \to k}}} \right|^2} = \left\langle {{\Phi _0}} \right|(1 + \hat \Lambda )\bar \mu {\hat R_k}\left| {{\Phi _0}} \right\rangle \left\langle {{\Phi _0}} \right|{\hat L_k}\bar \mu \left| {{\Phi _0}} \right\rangle

To calculate the transition dipole moment (TDM) in the EOM-CCSD framework one needs to solve both right and left eigenvectors due to the non-Hermitian nature of the similarity-transformed Hamiltonian. This can be performed by adding ``DoLambda True`` in the ``%cc`` block. For example the following input can be used to compute excitation energies, TDM and Oscillator strengths in a 4c-relativistic framework,

.. code-block:: shell 

   ! EE-EOM-CCSD spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   nroots 10
   DoLambda True
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

This calculation can also be done for the FNS and SS_FNS counterparts, namely ``FNO-EE-EOM-CCSD`` and ``SS-FNO-EE-EOM-CCSD`` methods, respectively.

The TDM and oscillator strength can also be calculated within a 4c-relativistic EE-UCC framework, where only a single eigen vector is required due to the Hermitian nature of the similarity-transformed Hamiltonian. The following input file demonstrates this calculation.

Third-order unitary Coupled Cluster (UCC3)
------------------------------------------

.. code-block:: shell 

   ! EE-UCC3 spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   ucc_convergence 1e-6
   nroots 10
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168


Quadratic unitary Coupled Cluster (qUCCSD)
------------------------------------------

.. code-block:: shell 

   ! EE-QUCCSD spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   ucc_convergence 1e-6
   nroots 10
   end

 

  *xyz 0 1
  H 0.0 0.0 0.0
  F 0.0 0.0 0.9168


Similarly, ground state dipole moment using CCSD and UCC in a relativistic framework can be obtained using the following input:

.. code-block:: shell 

   ! CCSD spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   DoLambda True
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

.. code-block:: shell 

   ! UCC3 spinor unc-ccpvdz

   %cc
   incore 5
   ucc_prop True
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

=====================
Second order property
=====================
BAGH currently supports the calculation of second-order properties, specifically the static and dynamic polarizability of the ground state. These polarizabilities can be computed using linear response coupled cluster singles and doubles (LR-CCSD) within the four-component (4c) framework. The linear response function within the coupled cluster framework can be defined as,

.. math::

    \langle\langle \mathbf{A}; \mathbf{B} \rangle\rangle_{\omega_{1}} =  \frac{1}{2} \hat{C}^{\pm \omega_{1}} \hat{P} [A(-\omega_{1}), B(+\omega_{1})] \Big\langle \Phi_0 \Big| (1 + \hat{\Lambda}) \times \Big( [\bar{A}, \hat{X}^{B}_{\omega_{1}}] + \frac{1}{2} [[\bar{H}, \hat{X}^{A}_{-\omega_{1}}], \hat{X}^{B}_{\omega_{1}}] \Big) \Big| \Phi_0 \Big\rangle

To compute polarizability at a specific frequency within the four-component (4c) relativistic framework, one must solve the ground-state left and right coupled cluster amplitudes and the perturbed left and right amplitudes. For example, the following input can be used to compute the dynamic polarizability of the HF molecule at an external frequency of 0.07198 a.u.

.. code-block:: shell 

   ! LR-CCSD spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   DoLambda True
   omega 0.07198
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

The external frequency can be specified by setting ``omega`` to the desired user-defined value.

************************
2. Two-component Methods
************************
The two-component DHF calculations are done using the X2CAMF scheme of the socutils package, interfaced with the BAGH.
To use the two-component method, use the keyword 'SOC-X2CAMF'.

*******************
Ground State Energy
*******************

================================
Coupled Cluster (CC)
================================
 .. math::

    |\Psi_{cc} \rangle = e^{\hat{T}} |\Phi_{0} \rangle


Coupled Cluster Singles Doubles (CCSD)
--------------------------------------

.. code-block:: shell 

   ! SOC-X2CAMF CCSD spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168


Calculating ground state CCSD energy using model potentials
-----------------------------------------------------------

.. code-block:: shell
   
   ! SOC-X2CAMF CCSD spinor unc-ccpvdz

   %cc
   incore 5
   x2c_type x2cmp
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

For spin-free case one can use the keyword sf1e
-----------------------------------------------

.. code-block:: shell
   
   ! SOC-X2CAMF CCSD spinor unc-ccpvdz

   %cc
   incore 5
   x2c_type sf1e
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

For spin-orbit case one can use the keyword 1e
-----------------------------------------------

.. code-block:: shell
   
   ! SOC-X2CAMF CCSD spinor unc-ccpvdz

   %cc
   incore 5
   x2c_type 1e
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168


Coupled Cluster Singles Doubles with perturbative Triples (CCSD(T))
-------------------------------------------------------------------
Use the keyword 'Dopertrip' to enable the perturbative triples calculations   

.. code-block:: shell 

   ! SOC-X2CAMF CCSD spinor unc-ccpvdz
   
   %cc
   incore 5
   cc_convergence 1e-7
   Dopertrip True
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

================================
Unitary Coupled Cluster (UCC)
================================

Third Order Unitary Coupled Cluster(UCC3)
----------------------------------------

.. code-block:: shell 

   ! SOC-X2CAMF UCC3 spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Quadratic Unitary Coupled Cluster Singles Doubles (qUCCSD)
----------------------------------------------------------

.. code-block:: shell 

   ! SOC-X2CAMF qUCCSD spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Quadratic Unitary Coupled Cluster Singles Doubles with perturbative Triples (qUCCSD[T])
---------------------------------------------------------------------------------------  

.. code-block:: shell 

   ! SOC-X2CAMF QUCCSD[T] spinor unc-ccpvdz
   
   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168


****************************
Beyond Ground State Methods
****************************
The following methods are also available in a two-component method.

+---------------------+---------------------+---------------------+-----------------+
|      Method         |        IP           |         EA          |        EE       |
+=====================+=====================+=====================+=================+
|      EOM-CCSD       |        YES          |      **---**        |        YES      |
+---------------------+---------------------+---------------------+-----------------+
|      ADC(2)         |        YES          |      **---**        |        YES      |
+---------------------+---------------------+---------------------+-----------------+

==================================================
Equation of Motion Coupled Cluster (EOM-CC)
==================================================

IP-EOM-CCSD
------------

The following input format can be used to calculate ionization potential (IP) in the EOM-CCSD framework.

.. code-block:: shell 

   ! SOC-X2CAMF IP-EOM-CCSD spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   eom_convergence 1e-6
   nroots 10
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168


EE-EOM-CCSD
------------

The following input format can be used to calculate excitation energy (EE) in the EOM-CCSD framework.

.. code-block:: shell 

   ! EE-EOM-CCSD soc-x2camf spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   eom_convergence 1e-6
   nroots 2
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168


==================================================
Algebraic Diagrammatic Construction (ADC)
==================================================

EE-ADC(2)
---------

.. code-block:: shell 
   ! EE-ADC(2) soc-x2camf spinor unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   eom_convergence 1e-6
   nroots 2
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168



*******************
Low-Cost Techniques
*******************
============================
Frozen Natural Spinors (FNS)
============================
The following methods are available with the FNS technique in a two-component method.

``FNO-CCSD``

``FNO-IP-EOM-CCSD``

``FNO-EE-EOM-CCSD``

``FNO-DIP-ADC(3)``  See the :ref:`dip-section` for details on DIP calculations.

.. code-block:: shell 

   ! SOC-X2CAMF FNO-CCSD spinor unc-ccpvdz

   %cc
   incore 5
   fnothresh 1e-5
   cc_convergence 1e-7
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

==============================================
State-Specific Frozen Natural Spinors (SS-FNS)
==============================================
The following methods are available with the SS-FNS technique in a two-component method.

``SS-FNO-EE-EOM-CCSD``

.. code-block:: shell 

   ! SS-FNO-EE-EOM-CCSD soc-x2camf spinor unc-ccpvdz

   %cc
   DoADC2 True
   incore 5
   fnothresh 1e-5
   cc_convergence 1e-7
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

============================
Cholesky Decomposition (CD)
============================
The Cholesky decomposition scheme can reduce disk space and memory demands by decomposing the two-electron integrals. Use the keyword 'CD' to enable Cholesky decomposition and CD_Threshold to adjust the accuracy. By default, CD_Threshold is 1e-5.

The following methods are available with the CD technique along with their FNS/SS-FNS versions (as per the availability mentioned above) in a two-component method.

``CCSD``

``IP-EOM-CCSD``

``EE-EOM-CCSD``

``UCC3``

``EE-UCC3``

``QUCCSD``

``EE-QUCCSD``

``QUCCSD[T]``

``EE-ADC(2)``

``DIP-ADC(2)``

``DIP-ADC(2)-X``

``DIP-ADC(3)``

``FNO-DIP-ADC(3)``


.. code-block:: shell 

   ! SOC-X2CAMF CCSD spinor unc-ccpvdz

   %cc
   incore 5
   CD True
   CD_Threshold 1e-3
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

A sample input file to run FNO-DIP-ADC(3) with Cholesky Decomposition:


.. code-block:: shell 

   ! FNO-DIP-ADC(3) SOC-X2CAMF spinor unc-ccpvdz

   %cc
   incore 5
   CD True
   CD_Threshold 1e-4
   nroots 10
   rootno 0,3
   adc_convergence 1e-06
   fnothresh 0.00003162277
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168


**********
Properties
**********
=====================
First-order property
=====================

Transition dipole moment using expectation value approach:
----------------------------------------------------------
EE-EOM-CCSD (canonical, FNS and SS-FNS) Transition dipole moments (TDMs) and Oscillator Strengths can also be calculated using Exact Two-Component Atomic Mean Field (X2CAMF) approach with and without Cholesky decomposition. By default, this feature is on. To turn off, one can put ``tdm False`` in the ``%cc`` block. However, ``DoLambda True`` is mandatory for the left EOM calculation. The following is a sample input file.

.. code-block:: shell

   ! SS-FNO-EE-EOM-CCSD soc-x2camf spinor unc-ccpvdz
   
   %cc
   cd True
   cd_threshold 1e-3
   DoADC2 True
   incore 5
   real_ints True
   nroots 2
   DoLambda True
   End
   
   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

**********************
Multireference Methods
**********************
CASCI, CASSCF, and strongly-contracted NEVPT2 are available for the two-component (spinor) X2CAMF framework, with the CASCI/CASSCF machinery itself provided by the interfaced `socutils <https://github.com/xubwa/socutils>`_ package. They are driven by the same ``.inp``/``!method`` input file interface as the single-reference methods above (``! CASSCF SOC-X2CAMF ...`` / ``! NEVPT2 SOC-X2CAMF ...``) for routine use; for finer control (custom CI solvers, multiple roots, inspecting natural orbitals, scripting over several active-space choices) they can also be driven directly as a Python API on top of a converged two-component X2CAMF mean field, exactly as the ``.inp`` interface does internally. Because the reference is two-component, active-space sizes are always counted in **spinors (spin-orbitals)**: ``ncas`` active spinors holding ``nelecas`` electrons, rather than spatial orbitals and a separate spin multiplicity.

============================================
Input File Usage
============================================
``! CASSCF SOC-X2CAMF spinor <basis>`` runs CASCI (or, with ``casscf True``, orbital-optimized CASSCF) and reports the total energy. ``! NEVPT2 SOC-X2CAMF spinor <basis>`` additionally runs the strongly-contracted NEVPT2 correlation energy on top of that CASCI/CASSCF reference. ``ncas`` and ``nelecas`` are mandatory; everything else in the ``%cc`` block (``x2c_type``, ``Gaunt``, ``Breit``, ``light_speed``, ...) behaves exactly as for the single-reference SOC-X2CAMF methods above.

CASCI (fixed orbitals, the default):

.. code-block:: shell

   ! CASSCF SOC-X2CAMF spinor ccpvdz

   %cc
   ncas 8
   nelecas 6
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.917

Orbital-optimized CASSCF, with the ``casscf True`` keyword:

.. code-block:: shell

   ! CASSCF SOC-X2CAMF spinor ccpvdz

   %cc
   ncas 8
   nelecas 6
   casscf True
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.917

Strongly-contracted NEVPT2 on top of a CASCI reference (add ``casscf True`` for a CASSCF reference instead):

.. code-block:: shell

   ! NEVPT2 SOC-X2CAMF spinor ccpvdz

   %cc
   ncas 8
   nelecas 6
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.917

Additional ``%cc`` keywords for this method family:

* ``ncas`` (``Integer``, required) -- number of active spinors;
* ``nelecas`` (``Integer``, required) -- number of active electrons;
* ``casscf`` (``Logical``, default ``False``) -- ``False`` runs fixed-orbital CASCI; ``True`` runs orbital-optimized CASSCF (density-fitted internally, and requires the bundled ``zquatev`` solver to be built);
* ``cas_nroots`` (``Integer``, default ``1``) -- number of active-space CI roots to solve for in one diagonalization;
* ``cas_ncore`` (``Integer``, default: inferred from ``nelecas``) -- override the number of doubly-occupied core spinors;
* ``cas_frozen`` (``Integer`` or comma list, default none) -- orbitals excluded from the CI/orbital-rotation problem; a single integer freezes the lowest that many orbitals, a comma-separated list (e.g. ``0,1``) freezes those specific spinor indices;
* ``cas_natorb`` (``Logical``, default: socutils' own default -- ``False`` for CASCI, ``True`` for CASSCF) -- rotate the active space to natural spinors;
* ``cas_canonicalize`` (``Logical``, default: socutils' own default, ``True`` for both) -- canonicalize the core/external blocks of the Fock matrix;
* ``cas_max_cycle_macro`` (``Integer``, default ``20``, CASSCF only) -- maximum number of super-CI macro-iterations;
* ``cas_max_stepsize`` (``Float``, default ``0.2``, CASSCF only) -- trust radius capping each orbital-rotation step;
* ``cas_conv_tol`` (``Float``, default ``1e-8``, CASSCF only) -- energy-convergence threshold;
* ``cas_conv_tol_grad`` (``Float``, default: ``sqrt(cas_conv_tol)``, CASSCF only) -- orbital-gradient convergence threshold;
* ``cas_freeze_pair`` (two comma lists separated by ``;``, e.g. ``0,1;2,3``, default none, CASSCF only) -- freeze mutual rotations between the two listed sets of orbital indices, leaving the rest of the space free to optimize.

The only CASSCF option that stays Python-API-only is ``irrep`` (per-orbital point-group symmetry labels): it needs the actual symmetry label of every computed spinor, which is only known once you've inspected a converged SCF -- not something that can be typed into a flat input file blind. Everything else above is fully input-file-driven.

============================================
Complete Active Space CI/SCF (CASCI/CASSCF)
============================================
This is what the ``! CASSCF``/``! NEVPT2`` input file lines above drive internally (including every ``cas_*`` keyword listed there); use it directly only if you need ``irrep``-based symmetry restriction or want to script over several active-space choices.

Relativistic CASCI
-------------------
``socutils.mcscf.zcasci.CASCI(mf, ncas, nelecas, ncore=None)`` diagonalizes the full CI problem within a chosen active spinor space on top of a **fixed** converged X2CAMF mean field. ``kernel()`` returns ``(e_tot, e_cas, ci, mo_coeff, mo_energy)`` and stores the same as attributes.

.. code-block:: python

   from pyscf import gto
   from socutils.scf import spinor_hf
   from socutils.mcscf import zcasci

   mol = gto.M(atom='H 0 0 0; F 0 0 0.917', basis='ccpvdz', verbose=4)

   mf = spinor_hf.SCF(mol).x2camf()
   mf.kernel()

   mc = zcasci.CASCI(mf, 8, 6)   # 6 electrons in 8 active spinors
   mc.kernel()
   print(mc.e_tot, mc.e_cas)

Useful attributes:

* ``ncore`` -- number of doubly-occupied core spinors; inferred from the electron count if not given;
* ``frozen`` -- orbitals excluded from the CI/orbital problem;
* ``natorb`` -- transform the active space to natural spinors (eigenvectors of the active 1-RDM);
* ``canonicalization`` -- canonicalize the core/external blocks of the Fock matrix (default ``True``);
* ``fcisolver`` -- the active-space CI solver (see `Choosing the active-space CI solver`_ below).

Relativistic CASSCF
--------------------
``socutils.mcscf.zmcscf.CASSCF(mf, ncas, nelecas, ncore=None, frozen=None)`` additionally optimizes the orbitals. ``kernel()`` drives a **super-CI** orbital optimizer: each macro-iteration solves the active-space CI problem, builds the orbital gradient and an approximate Hessian, and takes a Kramers-paired orbital-rotation step, repeating until the energy and orbital gradient are converged.

.. code-block:: python

   from pyscf import gto
   from socutils.scf import spinor_hf
   from socutils.mcscf import zmcscf

   mol = gto.M(atom='H 0 0 0; F 0 0 0.917', basis='ccpvdz', verbose=4)

   # the orbital optimizer builds its integrals from a density-fitted
   # reference, so the mean field must be density-fitted (or use Cholesky
   # decomposition, see the CD section above)
   mf = spinor_hf.SCF(mol).x2camf().density_fit()
   mf.kernel()

   mc = zmcscf.CASSCF(mf, 8, 6)   # 6 electrons in 8 active spinors
   mc.kernel()
   print(mc.e_tot)

Requirements
~~~~~~~~~~~~

* **zquatev** -- the orbital-rotation step is solved with the Kramers-paired (quaternion) eigensolver bundled with socutils; it must be built (``make`` in the socutils checkout) or ``kernel()`` raises a clear error.
* **A density-fitted reference** -- the optimizer builds its two-electron integrals by Cholesky/DF transformation from the mean field, so ``mf`` must carry a ``with_df``: attach it with ``.density_fit()``, or enable Cholesky decomposition with the ``CD True`` keyword route described above (otherwise ``kernel()`` raises ``Either with_df or cderi must be provided``).

Options
~~~~~~~

The optimization is controlled by attributes set on the ``CASSCF`` object (defaults in parentheses):

* ``max_cycle_macro`` (``20``) -- maximum number of macro-iterations;
* ``max_stepsize`` (``0.2``) -- trust radius capping each orbital-rotation step;
* ``conv_tol`` (``1e-8``) -- energy-convergence threshold;
* ``conv_tol_grad`` (``None`` -> ``sqrt(conv_tol)`` :math:`= 10^{-4}`) -- orbital-gradient convergence threshold;
* ``natorb`` (``True``) -- rotate the active orbitals to natural spinors at every macro-iteration;
* ``canonicalize_`` (``True``) -- diagonalize the core and virtual blocks of the effective Fock matrix so the inactive/virtual orbitals come out canonical;
* ``frozen`` (``None``) -- orbitals excluded from rotation; an ``int`` freezes the lowest orbitals, a list/array freezes the listed indices;
* ``freeze_pair`` (``None``) -- a pair of index sets whose mutual rotations are frozen, leaving the rest of the space free to optimize;
* ``irrep`` (``None``) -- per-orbital symmetry labels; rotations are then only allowed between orbitals carrying the same label.

Convergence and results
~~~~~~~~~~~~~~~~~~~~~~~~

A macro-iteration is accepted as converged when **both** the energy change and the orbital-gradient norm fall below their thresholds (``abs(dE) < conv_tol`` and ``norm(grad) < conv_tol_grad``); otherwise the loop stops at ``max_cycle_macro``. ``kernel()`` sets:

* ``mc.e_tot`` -- total CASSCF energy;
* ``mc.e_cas`` -- active-space (CI) energy;
* ``mc.ci`` -- the active-space CI vector;
* ``mc.mo_coeff`` / ``mc.mo_energy`` -- optimized (canonical) orbitals and their energies;
* ``mc.converged`` -- whether both convergence criteria were met.

.. note::

   For tightly-bound cases the default ``max_cycle_macro = 20`` can stop a step or two before ``abs(dE) < 1e-8``, even though the gradient is already below ``conv_tol_grad`` (so ``mc.converged`` is ``False`` while the energy is essentially converged). Raise ``mc.max_cycle_macro`` (e.g. to ``40``) to reach full convergence.

Choosing the active-space CI solver
------------------------------------
By default, both ``CASCI`` and ``CASSCF`` diagonalize the active space with socutils' own spinor CI module, ``socutils.fci.zfci``, which builds and diagonalizes the full CI Hamiltonian matrix explicitly in the determinant basis. This avoids the Davidson convergence problems that (near-)degenerate Kramers-paired roots cause for iterative solvers, and it is a drop-in replacement for both PySCF's ``fci_dhf_slow`` and the Dice-based selected-CI interface.

.. code-block:: python

   from socutils.mcscf import zcasci
   from socutils.fci import zfci

   mc = zcasci.CASCI(mf, 8, 6)
   mc.fcisolver = zfci.FCISolver(mol)   # this is also the default
   mc.fcisolver.nroots = 4              # solve several states in one diagonalization
   mc.kernel()
   print(mc.fcisolver.eci)              # the individual root energies

For larger active spaces, ``socutils.fci.zfci.SelectedCI(mol, occslst=...)`` diagonalizes within a chosen list of determinants instead of the full determinant space.

============================================
Strongly-Contracted NEVPT2
============================================
This is what the ``! NEVPT2`` input file line above drives internally; use it directly only for scripting (e.g. looping over several active-space choices) or inspecting ``pt.e_classes`` programmatically.

Theory
------
``bagh_code.nevpt2.NEVPT2`` computes the strongly-contracted second-order N-electron valence perturbation (SC-NEVPT2) correlation energy on top of a two-component X2CAMF CASCI/CASSCF reference. Spin-orbit coupling enters through the X2CAMF/AMFI effective one-electron Hamiltonian (built into ``mc.get_hcore()``) on exactly the same footing as the Coulomb interaction, so the method is applied unchanged whether SOC is switched on or off.

The core (doubly occupied), active, and external (virtual) spinors of the CASCI/CASSCF reference partition the perturbation into a Dyall-type zeroth-order Hamiltonian. The core and external blocks of the generalized Fock operator

.. math::

    F_{pq} = F^{I}_{pq} + \sum_{tu} D_{tu}\left[(pq|tu) - (pu|tq)\right], \qquad
    F^{I}_{pq} = h_{pq} + \sum_{i}\left[(pq|ii) - (pi|iq)\right]

(with :math:`D_{tu} = \langle \Psi_0 | a_t^\dagger a_u | \Psi_0 \rangle` the active one-body reduced density matrix, :math:`i` running over core spinors, and :math:`t,u` over active spinors) are each diagonalized separately -- this is the **semicanonicalization** step -- while the active block is left untouched. All integrals are then rotated into this semicanonical basis, and the resulting core/external orbital energies :math:`\varepsilon_i, \varepsilon_a` define the denominators below.

Following Angeli's strong-contraction scheme, the first-order interacting space splits into eight excitation classes, labelled by how many electrons they add to (:math:`+`) or remove from (:math:`-`) the active space, with core spinors :math:`i,j`, active spinors :math:`t,u,v`, and external (virtual) spinors :math:`a,b`:

.. list-table::
   :header-rows: 1
   :widths: 15 40 30

   * - Class
     - Active-space operator
     - External indices excited
   * - ``Sijrs(0)``
     - none (closed form, no active excitation)
     - :math:`i<j` (core), :math:`a<b` (virtual)
   * - ``Srs(-2)``
     - :math:`a_t a_u`
     - :math:`a<b` (virtual)
   * - ``Sij(+2)``
     - :math:`a_t^\dagger a_u^\dagger`
     - :math:`i<j` (core)
   * - ``Sijr(+1)``
     - :math:`a_t^\dagger`
     - :math:`i<j` (core), :math:`a` (virtual)
   * - ``Srsi(-1)``
     - :math:`a_t`
     - :math:`i` (core), :math:`a<b` (virtual)
   * - ``Sir(0)``
     - identity :math:`+\ a_t^\dagger a_u`
     - :math:`i` (core), :math:`a` (virtual)
   * - ``Si(+1)``
     - :math:`a_t^\dagger\ +\ a_t^\dagger a_u^\dagger a_v`
     - :math:`i` (core)
   * - ``Sr(-1)``
     - :math:`a_t\ +\ a_r^\dagger a_s a_q`
     - :math:`a` (virtual)

For each perturber :math:`\mu` built from an external excitation acting on one of these active-space operator strings applied to :math:`|\Psi_0\rangle`, the strongly-contracted energy contribution is

.. math::

    E_\mu = \frac{N_\mu^2}{E_{\mathrm{CAS}} - \langle \mu | \hat{H}_{\mathrm{act}} | \mu \rangle / N_\mu - \Delta\varepsilon_\mu}

where :math:`N_\mu` is the norm of the (un-normalized) contracted active-space vector, :math:`\hat{H}_{\mathrm{act}}` is the active-space Hamiltonian, and :math:`\Delta\varepsilon_\mu` is the sum of external (virtual) semicanonical orbital energies minus core semicanonical orbital energies entering that perturber. Class ``Sijrs(0)`` has no active-space operator at all, so its denominator reduces to the usual bare orbital-energy gap and it is evaluated in closed form.

Python API Usage
------------------

.. code-block:: python

   from pyscf import gto
   from socutils.scf import spinor_hf
   from socutils.mcscf import zcasci
   from bagh_code.nevpt2 import NEVPT2

   mol = gto.M(atom='H 0 0 0; F 0 0 0.917', basis='ccpvdz', verbose=4)

   mf = spinor_hf.SCF(mol).x2camf()
   mf.kernel()

   # CASCI: 6 electrons in 8 active spinors
   mc = zcasci.CASCI(mf, 8, 6)
   mc.kernel()

   pt = NEVPT2(mc)
   e_corr = pt.kernel()

   print('E(NEVPT2 total) =', mc.e_tot + e_corr)
   print('per-class:', pt.e_classes)

``mc`` may be either a ``zcasci.CASCI`` or a ``zmcscf.CASSCF`` object -- ``NEVPT2`` only reads ``mc.mo_coeff``, ``mc.ci``, ``mc.ncore``, ``mc.ncas``, ``mc.nelecas``, and ``mc.get_hcore()`` off it, so a converged orbital-optimized CASSCF reference works exactly the same way. After ``pt.kernel()`` runs:

* ``pt.e_corr`` -- the total NEVPT2 correlation energy (also the return value of ``kernel()``);
* ``pt.e_classes`` -- a dictionary with the energy contribution of each of the eight excitation classes listed above.

Validation
----------
With spin-orbit coupling switched off, the spinor strongly-contracted NEVPT2 correlation energy reproduces ``pyscf.mrpt.NEVPT2`` to machine precision for seven of the eight excitation classes; the ``Sir(0)`` class differs at the :math:`10^{-5}` level because ``pyscf`` uses a spin-adapted strong contraction (one perturber per *spatial* :math:`i \to a` transition, combining spin channels) whereas the spinor scheme keeps each Kramers channel separate -- the natural and general choice once spin-orbit coupling is switched on.

Scope and practical limits
---------------------------
This is a reference implementation aimed at small/moderate active spaces:

* it diagonalizes the active space exactly (via ``socutils.fci.zfci``) and applies explicit creation/annihilation operators to the resulting CI vector to build each perturber, rather than working through reduced density matrices of increasing rank;
* it stores the full spinor MO two-electron integral tensor densely; ``kernel()`` raises ``MemoryError`` before building it if the estimated size exceeds 8 GB (:math:`n_{\mathrm{mo}}^4 \times 16` bytes, where :math:`n_{\mathrm{mo}}` is the total number of spinors, i.e. core + active + external), so use a smaller basis/active space, or restrict to systems where the full-space integral tensor fits comfortably in memory.


