Non-Relativistic Framework
###########################

For non-relativistic ground state correlation energy calculation one can use the spin-adapted formulation for closed shell systems. Available ground state methods in spin-adapted and spin-orbital implementations are summerized below,

+---------------------+---------------------+---------------------+
|      Method         | Spin-orbital        | Spin-adapted        |
+=====================+=====================+=====================+
|    CCSD             | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    CCSD(T)          | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    CC3              | YES                 |        **---**      |
+---------------------+---------------------+---------------------+
|    UCC3             | YES                 |       **---**       |
+---------------------+---------------------+---------------------+
|    qUCCSD           | YES                 |        **---**      |
+---------------------+---------------------+---------------------+

For excited states 

+---------------------+---------------------+---------------------+
|      Method         | Spin-orbital        | Spin-adapted        |
+=====================+=====================+=====================+
|    EE-EOM-CCSD      | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    IP-EOM-CCSD      | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    EA-EOM-CCSD      | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    IP-EOM-CCSD*     | YES                 |       YES           |
+---------------------+---------------------+---------------------+
|    EE-ADC(2)        | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    IP-ADC(2)        | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    EA-ADC(2)        | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    EE-ADC(2)-X      | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    IP-ADC(2)-X      | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    EA-ADC(2)-X      | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    EE-ADC(3)        | YES                 |        **---**      |
+---------------------+---------------------+---------------------+
|    IP-ADC(3)        | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    EA-ADC(3)        | YES                 |      YES            |
+---------------------+---------------------+---------------------+
|    EE-UCC3          | YES                 |        **---**      |
+---------------------+---------------------+---------------------+
|    IP-UCC3          | YES                 |        **---**      |
+---------------------+---------------------+---------------------+
|    EE-qUCCSD        | YES                 |        **---**      |
+---------------------+---------------------+---------------------+

While writing input file 

*******************
Ground State Energy
*******************
================================
Coupled Cluster (CC)
================================
.. math::
   {E_{corr}} = 2\sum\limits_{ia} {f_i^at_i^a}  + 2\sum\limits_{ijab} {\tau _{ij}^{ab}\left\langle {ia\left\| {\left. {jb} \right\rangle } \right.} \right.}  - \sum\limits_{ijab} {\tau _{ij}^{ab}\left\langle {ib\left\| {\left. {ja} \right\rangle } \right.} \right.}

where

.. math::
   \tau _{ij}^{ab} = \sum {(t_i^at_j^b}  + t_{ij}^{ab})

Coupled Cluster Singles Doubles (CCSD)
--------------------------------------

.. code-block:: shell 

   ! CCSD unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Coupled Cluster Singles Doubles with perturbative Triples (CCSD(T))
-------------------------------------------------------------------

.. code-block:: shell 

   ! CCSD(T)  unc-ccpvdz

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

   ! CC3 spinorbital unc-ccpvdz

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
Third order unitary Coupled Cluster (UCC3)
------------------------------------------

.. code-block:: shell 

   ! UCC3 spinorbital  unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Quadratic unitary Coupled Cluster (qUCCSD)
------------------------------------------

.. code-block:: shell 

   ! qUCCSD spinorbital unc-ccpvdz

   %cc
   incore 5
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

   ! EE-EOM-CCSD spinorbital unc-ccpvdz

   %cc
   incore 5
   cc_convergence 1e-7
   eom_convergence 1e-6
   nroots 10
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Similarly for ionization potential (IP), one needs to change the name of the method to ``IP-EOM-CCSD``, for example

.. code-block:: shell 

   ! IP-EOM-CCSD spinorbital unc-ccpvdz

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

   ! EA-EOM-CCSD spinorbital unc-ccpvdz

   %cc
   incore 5
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

   ! EE-EOM-CC3 spinorbital unc-ccpvdz

   %cc
   incore 5
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

   ! EE-UCC3 spinorbital unc-ccpvdz

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

   ! EE-QUCCSD spinorbital unc-ccpvdz

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

   ! EE-ADC(2) spinorbital unc-ccpvdz

   %cc
   incore 5
   nroots 10
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Second order-extended ADC (ADC(2)-X)
------------------------------------

.. code-block:: shell 

   ! EE-ADC(2)-X spinorbital unc-ccpvdz

   %cc
   incore 5
   nroots 10
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

Third order ADC (ADC(3))
----------------------

.. code-block:: shell 

   ! EE-ADC(3) spinorbital unc-ccpvdz

   %cc
   incore 5
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
