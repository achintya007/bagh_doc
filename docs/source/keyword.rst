Keyword Reference
####################################

The default values are given as an example below for each keyword of the ``%cc`` block.

***********************************************
**Keyword descpriptions for** ``%cc`` **block**
***********************************************

**cc_convergence**: ``Float``

Controls the desired convergence criteria for CCSD correlation energy.

.. code-block:: shell

   cc_convergence 1.0e-10

**adc_convergence**: ``Float``

.. code-block:: shell
 
   adc_convergence  1.0e-6

**eom_convergence**: ``Float``

.. code-block:: shell

   eom_convergence 1.0e-6

**lambda_convergence**: ``Float``

.. code-block:: shell

   lambda_convergence 1.0e-6

**resp_convergence**: ``Float``

.. code-block:: shell

   resp_convergence 1.0e-10

**ucc_convergence**: ``Float``

.. code-block:: shell

   ucc_convergence 1.0e-7

**NRoots**: ``Integer``

.. code-block:: shell

   NRoots 2

**koopmans**: ``Logical``

.. code-block:: shell

   koopmans True

**Opt**: ``Logical``

.. code-block:: shell 

   Opt False

**CCSD2** ``Logical``

.. code-block:: shell

   CCSD2  False 

**cc_restart** ``Integer``

.. code-block:: shell

   cc_restart 0

**real_ints** ``Logical``

.. code-block:: shell

   real_ints flase

**CD_Threshold** ``Float``

.. code-block:: shell

   CD_Threshold 1e-5

**scf_guess_read** ``Logical``

.. code-block:: shell

   scf_guess_read False

**remove_linear_dependency** ``Logical``

.. code-block:: shell

   remove_linear_dependency False

**DoCore** ``Logical``

.. code-block:: shell

   DoCore False

**DoCVS** ``Logical``

.. code-block:: shell

   DoCVS False

**DoR3CVS** ``Logical``

.. code-block:: shell

   DoR3CVS False

**DoR3OPT** ``Logical``

.. code-block:: shell

   DoR3OPT True 

**Debug** ``Logical``

.. code-block:: shell

   Debug False

**DF** ``Logical``

.. code-block:: shell

   DF False

**CVSMIN** ``Integer``

.. code-block:: shell

   CVSMIN 0

**CVSMAX**: ``Integer``

.. code-block:: shell

   CVSMAX 0

**CoreHole** ``Integer``

.. code-block:: shell

   CoreHole 0

**initial_eta** ``Float``

.. code-block:: shell

   initial_eta 0.0

**ita_step** ``Float``

.. code-block:: shell

   ita_step 0.001

**max_ita_iter** ``Integer``

.. code-block:: shell

   max_ita_iter 100

**Dolambda** ``Logical``

.. code-block:: shell

   Dolambda False

**qed** ``Logical``

.. code-block:: shell

   qed False

**Dopertrip** ``Logical``

.. code-block:: shell

   Dopertrip False

**lambda_restart** ``Integer``

.. code-block:: shell

   lambda_restart 0

**printlevel** ``Integer``

.. code-block:: shell

   printlevel 0

**maxcore** ``Integer``

.. code-block:: shell

   maxcore 0

**ML** ``Logical``

.. code-block:: shell

   ML False

**pct_occ_ex** ``Float``

.. code-block:: shell

   pct_occ_ex 0.0

**incore** ``Integer``

.. code-block:: shell

   incore 5

**DoADC2** ``Logical``

.. code-block:: shell

   DoADC2 False

**reldipole** ``Logical``

.. code-block:: shell

   reldipole False

**DumpEOM** ``Logical``

.. code-block:: shell

   DumpEOM False

**DoNataux** ``Logical``

.. code-block:: shell

   DoNataux False

**Natauxpct_ex** ``Integer``

.. code-block:: shell

   Natauxpct_ex 100

**Natauxthresh** ``Integer``

.. code-block:: shell

   Natauxthresh 0

**Natauxthresh_ex** ``Integer``

.. code-block:: shell

   Natauxthresh_ex 100

**Natauxthresh_bottleneck** ``Float``

.. code-block:: shell

   Natauxthresh_bottleneck 1e-1

**Natauxthresh_ex_bottleneck** ``Float``

.. code-block:: shell

   Natauxthresh_ex_bottleneck 1e-1

**nfr_h** ``Integer``

.. code-block:: shell

   nfr_h 3 

**nfr_p** ``Integer``

.. code-block:: shell

  nfr_p 3

**fc** ``Logical``

.. code-block:: shell

   fc False

**fc_no** ``Integer``

.. code-block:: shell

   fc_no -1

**noact** ``Integer``

.. code-block:: shell

   noact 1

**nvact** ``Integer``

.. code-block:: shell

   nvact  1

**DoACTCC** ``Logical``

.. code-block:: shell

   DoACTCC False

**Gaunt** ``Logical``

.. code-block:: shell

   Gaunt False

**Breit** ``Logical``

.. code-block:: shell

   Breit False

**ssss** ``Logical``

.. code-block:: shell

   ssss True

**custom_basis** ``Integer``

.. code-block:: shell

  custom_basis  None

**light_speed** ``Integer``

.. code-block:: shell

   light_speed None

**DoLoc** ``Logical``

.. code-block:: shell

  DoLoc False

**DIIS** ``Logical``

.. code-block:: shell

   DIIS True

**NumProc** ``Integer``

.. code-block:: shell

   NumProc 1

**TCutPair** ``Float``

.. code-block:: shell

   TCutPair 1e-5

**TCutPNO** ``Float``

.. code-block:: shell

   TCutPNO 1e-6

**int_restart** ``Integer``

.. code-block:: shell

   int_restart 0

**cis_restart** ``Integer``

.. code-block:: shell

   cis_restart 0

**imds_restart** ``Integer``

.. code-block:: shell

   imds_restart []

**ext_e** ``Integer``

.. code-block:: shell

   ext_e None

**pyberny_flag** ``Integer``

.. code-block:: shell

   pyberny_flag 0

**rootno** ``Logical``

.. code-block:: shell

   rootno False

**max_space** ``Integer``

.. code-block:: shell

    max_space 100

**max_cycle** ``Integer``

.. code-block:: shell

   max_cycle 100

**x2c** ``Logical``

.. code-block:: shell

   x2c False

**relcc** ``Logical``

.. code-block:: shell

   relcc False

**ccsdnat** ``Logical``

.. code-block:: shell

   ccsdnat False

**actspace_overide** ``Logical``

.. code-block:: shell

   actspace_overide False

**act_cvir** ``Integer``

.. code-block:: shell

   act_cvir None

**povo_can** ``Integer``

.. code-block:: shell

   povo_can None

**splitfno** ``Logical``

.. code-block:: shell

   splitfno False

**runmrcc** ``Logical``

.. code-block:: shell

   runmrcc False

**symmetry** ``Logical``

.. code-block:: shell

   symmetry False

**symmetry_subgroup**

.. code-block:: shell

   symmetry_subgroup c1

**correction**  ``Logical``

.. code-block:: shell

   correction False

**splitorders** ``Integer``

.. code-block:: shell

   splitorders 1,2,3

**mpi** ``Logical``

.. code-block:: shell

   mpi False

**scf_guess_read** ``Logical``

.. code-block:: shell

   scf_guess_read False

**pic_change** ``Logical``

.. code-block:: shell

   pic_change 

**remove_linear_dependency**  ``Logical``

.. code-block:: shell

   remove_linear_dependency False

**povo**

.. code-block:: shell

   povo None

**povo_ex**

.. code-block:: shell

   povo_ex None

**omega** ``Integer``

.. code-block:: shell

   omega 0

**pytranf** ``Logical``

.. code-block:: shell

   pytranf False

**dirac_complex** ``Logical``

.. code-block:: shell

   dirac_complex False

**plotnat** ``Logical``

.. code-block:: shell

   plotnat False

**plotnat_no** 

.. code-block:: shell

   plotnat_no []

**plotnto** ``Logical``

.. code-block:: shell

   plotnto False

**plotnto_no**

.. code-block:: shell

   plotnto_no []

**Triplet** ``Logical``

.. code-block:: shell

   Triplet False

**DysonOrbPlot** ``Logical``

.. code-block:: shell

   DysonOrbPlot False

**exdm** ``Logical``

.. code-block:: shell

   exdm True

**tdm** ``Logical``

.. code-block:: shell

   tdm True

**z_axis** ``Logical``

.. code-block:: shell

   z_axis False

**x_axis** ``Logical``

.. code-block:: shell

   x_axis False

**ucc_prop** ``Logical``

.. code-block:: shell

   ucc_prop False

**fort** ``Logical``

.. code-block:: shell

   fort True

**CD** ``Logical``

.. code-block:: shell

   CD False

**ccpert_lambda** ``Logical``

.. code-block:: shell

   ccpert_lambda True

**T3** ``Logical``

.. code-block:: shell

   T3 False

**bulksize** ``Integer``

.. code-block:: shell

   bulksize 10

**dtype** ``Integer``

.. code-block:: shell 

   dtype None

**Pembed** ``Logical``

.. code-block:: shell

   Pembed False

**shift_e** ``Integer ``

.. code-block:: shell 

   shift_e 0 

**CD_Threshold** ``Float``

.. code-block:: shell 

    CD_Threshold 1e-5

**active_atoms**

.. code-block:: shell

   active_atoms

**cpy** ``Logical``

.. code-block:: shell

   cpy False

**cav_frequency** ``Integer``

.. code-block:: shell

   cav_frequency 0

**cav_lambda_x** ``Integer``

.. code-block:: shell

   cav_lambda_x None

**cav_lambda_y** ``Integer``

.. code-block:: shell

   cav_lambda_y  None

**cav_lambda_z** ``Integer``

.. code-block:: shell

   cav_lambda_z  None

**x2c_type** 

.. code-block:: shell

   x2c_type  x2camf

By default, the x2c_type is x2camf. For model potential, the keyword is ``x2c_type x2cmp``. The spin-free x2c1e and spin-orbit x2c1e can be requested via ``x2c_type sf1e`` and ``x2c_type 1e``.
