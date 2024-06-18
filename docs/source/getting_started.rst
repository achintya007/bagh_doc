
===============

.. _getting started:

Getting startred
----------------

Using the PySCF Interface
#########################

**Guidelines to write input file of Ground State Energy in relativistic framework :**

While writing the input file, one should write which method is following; it doesn’t matter whether it is lower case or upper case. For a relativistic framework, if the interference is pyscf , one should write spinor; if the interference is Dirac, then one should write dirac. After that, you should write the desirable basis set; if the bond lengths are in Armstrong, there is no need to write anything else you have to write “borh” at the end   if it is not in the Armstrong unit.

Example:

.. code-block:: shell 

   ! method inference basis 

    %cc
    **----**
    **----**
    **----**
    end

    *xyz 0 1
    A X1 Y1 Z1
    B X2 Y2 Z2

In %cc block, which desired incore should mentioned for integrals,; if the molecule is linear, then the “real_ints True ”  sentence should written. The tolerance rate should be mentioned for accuracy, and the “end”  command should be given.
The coordinates you are working with should mentioned in the next line, along with the charge and multiplicity. After that, an atomic symbol(A,B) with coordinates (X1,Y1,Z1) should mentioned.

For the reference, one input file is given below:

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

Here,

| CCSD : method
| spinor : pyscf inference in relativistic framework 
| unc-ccpvdz : basis
| incore 5 : desired incore for integrals 
| real_ints : For linear molecules, it should be true in a relativistic framework. 
| cc_convergence 1e-7 : tolerance rate for accuracy
| *xyz : Cartesian coordinate
| 0 : charge of the molecule
| 1 : multiplicity
| H,F : Atomic symbols

Using the DIRAC Interface
#########################
