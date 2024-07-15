
===============

.. _getting started:

Getting started
----------------

**Relativistic calculation: Using the PySCF Interface**


**Guidelines to write input file of Ground State Energy in the relativistic framework:**

While writing the input file, one should write which method is following; it doesn’t matter whether it is lower case or upper case. For a relativistic framework, if the interface is pyscf, one should write ``spinor``. After that, you should write the desirable ``basis-set``; if the bond lengths are in Angstrom, there is no need to write anything else you have to write ``Bohr`` at the end if it is not in the Angstrom unit.

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

In %cc block, which desired incore should mentioned for integrals, if the molecule is linear, then the ``real_ints True``  sentence should written. The coupled cluster convergence tolerance should be mentioned for the desired accuracy using ``cc_convergence`` keyword. 
The coordinates you are working with should be mentioned in the next line (``*xyz``), along with the ``charge`` and ``multiplicity``. After that, the coordinates of the molecule need to be mentioned in the cartesian co-ordinates with proper atomic symbols ( ``A``, ``B`` ). 

For example a four-component relativistic coupled cluster singles doubles (CCSD) calculation using pyscf interface can be performed using the following input file,

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

| ``CCSD`` : name of the method
| ``spinor`` : pyscf inference in relativistic framework 
| ``unc-ccpvdz`` : basis-set
| ``Bohr`` : unit of bond lenght 
| ``incore 5`` : desired incore for integrals 
| ``real_ints`` : For linear molecules, it is preferable  to use ``True`` 
| ``cc_convergence 1e-7`` :tolerance of CCSD converegnce 
| ``*xyz`` : Cartesian coordinate
| ``0`` : charge of the molecule
| ``1`` : spin multiplicity (2S+1)
| ``H,F`` : Atomic symbols


**Non-relativistic calculation: Using the PySCF Interface**


**Guidelines to write input file of Ground State Energy in the non-relativistic framework:**

The input file for a non-relativistic framework is nearly identical to that for a relativistic framework. For a non-relativistic framework, if the interface is a pyscf spin-adapted implementation, then there is no need to write anything regarding the interface in the input file.

Example:

.. code-block:: shell 

   ! method  basis-set unit

   %cc
   ------
   ------
   ------
   end

   *xyz charge  multiplicity
   A x1 y1 z1
   B x2 y2 z2

The %cc block is also similar to the relativistic one, with the change that'real_ints ''true'' should not be present. 
For example a non-relativistic coupled cluster singles doubles (CCSD) calculation using  pyscf interface within spin-adapted implementation can be performed using the following input file,

.. code-block:: shell 

   ! CCSD unc-ccpvdz Bohr

   %cc
   incore 5
   cc_convergence 1e-7
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 1.7325


