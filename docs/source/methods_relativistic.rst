Relativistic Framework
######################

+---------------------+---------------------+---------------------+-----------------+
|      Method         |        IP           |         EA          |     EE          |
+=====================+=====================+=====================+=================+
|   EOM-CCSD          |        YES          |      YES            |      YES        |
+---------------------+---------------------+---------------------+-----------------+
|    EOM-CC3          |      **---**        |     **---**         |      YES        |
+---------------------+---------------------+---------------------+-----------------+
|    ADC(2)           |        YES          |      YES            |      YES        |
+---------------------+---------------------+---------------------+-----------------+
|    ADC(2)-X         |        YES          |      YES            |      YES        |
+---------------------+---------------------+---------------------+-----------------+
|    ADC(3)           |        YES          |      YES            |      YES        |
+---------------------+---------------------+---------------------+-----------------+
|    UCC(3)           |        YES          |     **---**         |      YES        |
+---------------------+---------------------+---------------------+-----------------+
|    qUCCSD           |       **---**       |     **---**         |      YES        |
+---------------------+---------------------+---------------------+-----------------+

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
   real_ints True
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Coupled Cluster Singles Doubles with perturbative Triples (CCSD(T))
-------------------------------------------------------------------

.. code-block:: shell 

   ! CCSD(T) spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
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
   real_ints True
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

===================================
Unitary Coupled Cluster (UCC)
===================================
Third order unitary Coupled Cluster (UCC3)
------------------------------------------

.. code-block:: shell 

   ! UCC3 spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Quadratic unitary Coupled Cluster (qUCCSD)
------------------------------------------

.. code-block:: shell 

   ! qUCCSD spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

To calculate the ionisation potential in the UCC framework, one can write ``IP-UCC3`` in place of method in the input file.

********************
Excited State Energy
********************
==================================================
Equation of Motion Coupled Cluster (EOM-CC)
==================================================
EOM-Coupled Cluster Singles Doubles (EOM-CCSD)
---------------------------------------------
To calculate excitation energy in EOM-CCSD framework, the following input format can be used

.. code-block:: shell 

   ! EE-EOM-CCSD spinor unc-ccpvdz

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

Similarly for ionization potential (IP), one needs to change the name of the method to ``IP-EOM-CCSD``, for example

.. code-block:: shell 

   ! IP-EOM-CCSD spinor unc-ccpvdz

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
Third order unitary Coupled Cluster (UCC3)
------------------------------------------

.. code-block:: shell 

   ! EE-UCC3 spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
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
   real_ints True
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


To calculate the ionisation potential and electron affinity in the ADC framework, one can write ``IP-ADC(2)``, ``IP-ADC(2)-X``, ``IP-ADC(3)``, ``EA-ADC(2)``, ``EA-ADC(2)-X``, and ``EA-ADC(3)`` in place of method in the input file.
 

**********
Properties
**********
=====================
First order property
=====================
Transition dipole moment using expectation value approach:
----------------------------------------------------------
The ground to excited state transition moment in the EOM-CCSD framework can be expressed as

.. math::

    {\left| {{\mu _{o \to k}}} \right|^2} = \left\langle {{\Phi _0}} \right|(1 + \hat \Lambda )\bar \mu {\hat R_k}\left| {{\Phi _0}} \right\rangle \left\langle {{\Phi _0}} \right|{\hat L_k}\bar \mu \left| {{\Phi _0}} \right\rangle

To calculate the transition dipole moment (TDM) in the EOM-CCSD framework one needs to solve both right and left eigenvectors due to the non-hermitian nature of the similarity-transformed Hamiltonian. This can be performed by adding ``DoLambda True`` in the ``%cc`` block. For example the following input can be used to compute excitation energies, TDM and Oscillator strengths in 4c-relativistic framework,

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

=====================
Second order property
=====================
