Available Methods in Relativistic Framework
###########################################

*******************
Ground State Energy
*******************
================================
Coupled Cluster (CC)
================================
 .. math::

    |\Psi_{cc} \rangle = e^{\hat{T}} |\Phi_{0} \rangle
  

.. math::

   \[T^{i_1 i_2 \dots i_p}_{j_1 j_2} = T(x^{i_1},\dots,x^{i_p},e_{j_1},\dots,e_{j_q}) \]
   

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

**********
Properties
**********
=====================
First order property
=====================
=====================
Second order property
=====================
