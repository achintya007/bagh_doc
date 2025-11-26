Algebraic Diagrammatic Construction (ADC)
########################################

The Algebraic Diagrammatic Construction (ADC) is a post-Hartree Fock ab-initio Hermitian method to calculate vertical ionization potential (IP), electron-attachment energy (EA), excitation energy (EE), and vertical double ionization potential (DIP) in a direct-difference of energy based way. ADC is implemented using Intermediate State
Representation (ISR) in BAGH. The ADC secular matrix is expanded in perturbation series. A truncation at nth order leads to ADC(n) method. This method incorporates correlation from its second-order (ADC(2) method). An extender version of ADC(2), namely ADC(2)-X is considered to be an ad-hoc extension up to the first order terms in doubles-doubles block (2h1p-2h1p for IP and 2p1h-2p1h for EA). In BAGH, ADC(n) is implemented up to 3rd order, i.e. ADC(3).

ADC being a Hermitian method calculates transition and excitate state properties very efficiently. In
the current version of BAGH, transition dipole moment (TDM) and excited state dipole moment
(EXDM) can be calculated using the EE-ADC(n) method by setting the keyword ``tdm`` and ``exdm`` to ``True`` in the ``%CC`` block.

The available names of the methods and keywords to access them are listed below:

+---------------------+---------------------+---------------------+-----------------+-----------------+
|      Method         |        IP           |         EA          |     EE          |      DIP        |
+=====================+=====================+=====================+=================+=================+
|    ADC(2)           |        IP-ADC(2)    |      EA-ADC(2)      |      EE-ADC(2)  |    DIP-ADC(2)   |
+---------------------+---------------------+---------------------+-----------------+-----------------+
|    ADC(2)-X         |        IP-ADC(2)-X  |    EA-ADC(2)-X      |     EE-ADC(2)-X |    DIP-ADC(2)-X |
+---------------------+---------------------+---------------------+-----------------+-----------------+
|    ADC(3)           |        IP-ADC(3)    |      EA-ADC(3)      |      EE-ADC(3)  |    DIP-ADC(3)   |
+---------------------+---------------------+---------------------+-----------------+-----------------+

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
| ``nroots 4``: 4 excitation energies will be calculated.

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

A similar type of the above input file can be used for IP and EA also. One just needs to change the name of the method in the input file.

********************************************************
Transition dipole moment and excited state dipole moment
********************************************************

To get the transition dipole moment and excited state dipole moment, two extra keywords that have to be added in the %cc block are ``tdm`` and ``exdm``. A example input file is shown below:

.. code-block:: shell 

   ! EE-ADC(3) spinor unc-ccpvdz

   %cc
   incore 5
   real_ints True
   nroots 10
   tdm True
   exdm True
   End

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

By switching on the ``tdm`` and ``exdm`` we obtain:

.. code-block:: shell

               ************************ Transition Dipole Moment *****************************
			-------------------------------------------------------------------------------------------

			         ABSORPTION SPECTRUM VIA TRANSITION ELECTRIC DIPOLE MOMENTS

			-------------------------------------------------------------------------------------------

                         State     Energy      Wavelength      fosc         T2          TX        TY         TZ
                                   (cm-1)        (nm)                    (au**2)       (au)      (au)       (au)      
			-------------------------------------------------------------------------------------------

			   1       82593.6       121.075      0.00000     0.00000     0.00000   0.00000   0.00000
			   2       82593.6       121.075      0.00000     0.00000     0.00000   0.00000   0.00000
			   3       82734.1       120.869      0.00001     0.00003     0.00466   0.00708   0.00000
			   4       82734.1       120.869      0.00001     0.00002     0.00691   0.00477   0.00000
			   5       82883.0       120.652      0.00000     0.00000     0.00000   0.00000   0.00000
			   6       82883.7       120.651      0.00001     0.00003     0.00000   0.00000   0.00555
			   7       87742.9       113.969      0.02065     0.07750     0.27840   0.00262   0.00000
			   8       87742.9       113.969      0.02171     0.08145     0.00256   0.28541   0.00000
			   9      109722.6        91.139      0.00000     0.00000     0.00000   0.00000   0.00000
			  10      109722.8        91.139      0.00000     0.00000     0.00162   0.00001   0.00000

      MP2 contribution (Electronic) to ground state dip moment in a.u. [0.000000000000125 0.000000000000000   0.043739251678833]

              ************************ Excited State Dipole Moment *****************************
			-------------------------------------------------------------------------------------------

                         State     Energy      Wavelength      fosc         T2          TX        TY         TZ
                                   (cm-1)        (nm)                    (au**2)       (au)      (au)       (au)      
			-------------------------------------------------------------------------------------------

			   1       82593.6       121.075      0.66368     2.64537     0.00000   0.00000   1.62646
			   2       82593.6       121.075      0.66368     2.64537     0.00000   0.00000   1.62646
			   3       82734.1       120.869      0.66479     2.64532     0.00000   0.00000   1.62644
			   4       82734.1       120.869      0.66479     2.64532     0.00000   0.00000   1.62644
			   5       82883.0       120.652      0.66598     2.64527     0.00000   0.00000   1.62643
			   6       82883.7       120.651      0.66595     2.64512     0.00000   0.00000   1.62638
			   7       87742.9       113.969      0.71816     2.69455     0.00000   0.00000   1.64151
			   8       87742.9       113.969      0.71816     2.69455     0.00000   0.00000   1.64151
			   9      109722.6        91.139      0.77565     2.32727     0.00000   0.00000   1.52554
			  10      109722.8        91.139      0.77564     2.32724     0.00000   0.00000   1.52553

In the excited state dipole moment section, a change in the dipole moment due to the excitation from ground to excited is listed. To get the total dipole moment for a particular excited state, MP2 contribution needs to be included. 

.. _dip-section:
***********************************
Double Ionization Potential (DIP)
***********************************

A sample input file is given below for the user’s reference for a relativistic 4-component DIP-ADC(3) calculation of the HF molecule.

.. code-block:: shell 

   ! DIP-ADC(3) spinor unc-ccpvdz

   %cc
   numproc 4
   incore 5
   real_ints True
   nroots 5
   rootno 0,3
   adc_convergence 1e-06
   end

   *xyz 0 1
   H 0.0 0.0 0.0
   F 0.0 0.0 0.9168

| ``nroots 5``: 5 DIP-CIS energies will be printed.
| ``rootno 0,3``: instructs the code to apply the DIP-ADC(3) procedure specifically to the 1st and 4th states (counting from zero) among the previously computed DIP-CIS roots.

With the above computational procedure, we obtain the following DIP energies and the corresponding dominant transitions:

.. code-block:: shell 

	--------DIP-ADC(3) values-----------


	Root: 1 	 DIP value: 1.7795271 a.u. 	  48.423424 eV 	
	Dominant Transition
	6 7 --> 0.46234
	8 9 --> -0.47157


	Root: 4 	 DIP value: 1.8931260 a.u. 	  51.514607 eV 	
	Dominant Transition
	4 8 --> 0.29878
	4 9 --> -0.36062
	5 8 --> 0.36061
	5 9 --> 0.29877





