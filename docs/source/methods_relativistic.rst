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




