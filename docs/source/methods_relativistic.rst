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
Quadratic unitary Coupled Cluster (qUCCSD)
------------------------------------------

================================================
Algebraic Diagrammatic Construction Theory (ADC)
================================================
Second order ADC (ADC(2))
-------------------------
extended-Second order ADC (ADC(2)-X)
------------------------------------
Third order ADC (ADC(3))
----------------------
**********
Properties
**********
=====================
First order property
=====================
=====================
Second order property
=====================
