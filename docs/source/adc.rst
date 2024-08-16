Algebraic Diagrammatic Construction (ADC)
########################################

The Algebraic Diagrammatic Construction (ADC) is a post-Hartree Fock ab-initio Hermitian method to calculate vertical ionization potential (IP), electron-attachment energy (EA) and excitation energy (EE) in a direct-difference of energy based way. ADCis implemented using Intermediate State
Representation (ISR) in BAGH. The ADC secular matrix is expanded in perturbation series. A truncation at nth order leads to ADC(n) method. This method incorporates correlation from its second-order (ADC(2) method). An extender version of ADC(2), namely ADC(2)-X is considered to be an ad-hoc extension up to the first order terms in doubles-doubles block (2h1p-2h1p for IP and 2p1h-2p1h for EA). In BAGH, ADC(n) is implemented up to 3rd order, i.e. ADC(3).

ADC being a Hermitian method calculates transition and excitate state properties very efficiently. In
the current version of BAGH, transition dipole moment (TDM) and excited state dipole moment
(EXDM) can be calculated using the EE-ADC(n) method by setting the keyword ``tdm`` and ``exdm`` to ``True`` in the ``%CC`` block.

The available names of the methods and keywords to access them are listed below:

+---------------------+---------------------+---------------------+-----------------+
|      Method         |        IP           |         EA          |     EE          |
+=====================+=====================+=====================+=================+
|    ADC(2)           |        IP-ADC(2)    |      EA-ADC(2)      |      EE-ADC(2)  |
+---------------------+---------------------+---------------------+-----------------+
|    ADC(2)-X         |        IP-ADC(2)-X  |    EA-ADC(2)-X      |     EE-ADC(2)-X |
+---------------------+---------------------+---------------------+-----------------+
|    ADC(3)           |        IP-ADC(3)    |      EA-ADC(3)      |      EE-ADC(3)  |
+---------------------+---------------------+---------------------+-----------------+

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
| ``nroots 10``: 4 excitation energies will be calculated.

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
