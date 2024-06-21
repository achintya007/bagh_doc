
===============

.. _getting started:

Getting started
----------------

**Using the PySCF Interface**


**Guidelines to write input file of Ground State Energy in relativistic framework :**

While writing the input file, one should write which method is following; it doesn’t matter whether it is lower case or upper case. For a relativistic framework, if the interface is pyscf , one should write ``spinor``. After that, you should write the desirable ``basis-set``; if the bond lengths are in Armstrong, there is no need to write anything else you have to write ``Bohr`` at the end   if it is not in the Armstrong unit.

Example:

.. code-block:: shell 

   ! method interface basis-set

   %cc
   ------
   ------
   ------
   end

   *xyz charge  multiplicity
   A x1 y1 z1
   B x2 y2 z2

In %cc block, which desired incore should mentioned for integrals,; if the molecule is linear, then the ``real_ints True``  sentence should written. The ``coupled cluster convergence`` should be mentioned for accuracy, and the ``end``  command should be given.
The coordinates you are working with should mentioned in the next line, along with the ``charge`` and ``multiplicity``. After that, an atomic symbol ``(A,B)`` with coordinates ``(x1,y1,z1)`` should mentioned.

For the reference, one input file is given below:

.. code-block:: shell 

   ! CCSD spinor unc-ccpvdz Bohr

   %cc
   incore 5
   real_ints True
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 1.7325

Here,

| ``CCSD`` : method
| ``spinor`` : pyscf inference in relativistic framework 
| ``unc-ccpvdz`` : basis-set
| ``incore 5`` : desired incore for integrals 
| ``real_ints`` : For linear molecules, it should be true in a relativistic framework. 
| ``cc_convergence 1e-7`` : tolerance rate for accuracy
| ``*xyz`` : Cartesian coordinate
| ``0`` : charge of the molecule
| ``1`` : multiplicity
| ``H,F`` : Atomic symbols

Using the DIRAC Interface
#########################
