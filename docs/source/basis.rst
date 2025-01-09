Ways to assign basis sets
#########################

Similar to the other part of the input file in BAGH, the basis set assignment is case and space-insensitive (as long as different keywords are separated) unless mentioned otherwise. There are 3 ways basis sets are assigned to different atoms in BAGH (arranged according to the priorities):

1. Atom-specific basis set
2. Custom basis set
3. Universal basis set

The highest priority is given to the atom-specific basis sets, and the lowest goes to the universal basis set assignment. If no basis set is assigned to an atom at all, by default, STO-3G is assigned to it.

A sample input file is shown below:

Example 1:

.. code-block:: shell 

   ! CCSD spinor  pos3   pos4   pos5

   %cc
   incore 5
   end

   %basis
   pos2
   end

   *xyz 0 1
   H	 0.0000	-0.9377	-0.3816   pos1
   H	 0.8121	 0.4689	-0.3816
   H	-0.8121	 0.4689	-0.3816
   N	 0.0000	 0.0000	 0.0000    

All 3 positions are shown in the above example. ``pos1``, ``pos2`` and ``pos3`` refer to the positions of atom-specific basis, custom basis and universal basis, respectively. A basis set can be assigned to an atom in either of these 3 ways. In the above example, one of the ``H`` atoms is being assigned a basis set from the atom-specific basis (``pos1``). The rest of the two ``H`` atoms and the ``N`` atom can either be assigned by the custom basis (``pos2``) or the universal basis (``pos3``) only if any atom is left unassigned in the custom basis. Let's say custom basis only contains basis functions for ``N`` atom, then rest of the two ``H`` atoms will be assigned from the universal basis in ``pos3``. The positions ``pos4`` and ``pos5`` refer to the position of JK and RI basis positions, respectively (if any).

.. note::

      - One can assign a JK auxiliary basis even if there is no universal main basis.
      - One can assign a RI auxiliary basis even if there is no universal main basis or JK auxiliary basis or both.

***********************
Atom-specific basis set
***********************

Atom-specific basis sets are written within the coordinate block ``*xyz`` in the 5th column as in pos1 in Example 1. The basis set is assigned only to that particular atom (eg. first ``H`` atom in Example 1). The rest of the atoms remain unassigned. A sample input file is shown where all of the atoms are assigned using atom-specific basis set.

Example 2:

.. code-block:: shell 

   ! CCSD spinor

   %cc
   incore 5
   end

   *xyz 0 1
   H	 0.0000	-0.9377	-0.3816   ccpvdz
   H	 0.8121	 0.4689	-0.3816   ccpvdz
   H	-0.8121	 0.4689	-0.3816   ccpvdz
   N	 0.0000	 0.0000	 0.0000   augccpvdz

Here all 3 ``H`` atoms are assigned ``cc-pVDZ`` basis set, while ``N`` atom is assigned to ``aug-cc-pVDZ`` basis set. One can also assign different basis sets for different atoms of the same element as in the following example.

Example 3:

.. code-block:: shell 

   ! CCSD spinor

   %cc
   incore 5
   end

   *xyz 0 1
   H	 0.0000	-0.9377	-0.3816   ccpvdz
   H	 0.8121	 0.4689	-0.3816   631g
   H	-0.8121	 0.4689	-0.3816   ccpvtz
   N	 0.0000	 0.0000	 0.0000   augccpvdz


****************
Custom basis set
****************

If an atom remains unassigned to any basis set in an atom-specific way, the flow of code searches for that atom in the custom basis set. The custom basis set is provided within ``%basis`` block of the input file and it ends with a single ``end`` at the end. There should not be any other text in the middle as they may interfere with the proper running of the code, especially another ``end`` in the middle. Only one ``end`` keyword will be used for the entire ``%basis`` block. The custom basis has to be in the ``NWCHEM`` format.

.. caution::

   **Space-sensitivity in the custom basis:** Basis function type eg. ``S``, ``P``, ``D``, ``F``... has to start from the 6th column of the input file.

A sample input file is provided containing only custom basis assignment to each atom.

Example 4:

.. code-block:: shell 

   ! CCSD spinor

   %cc
   incore 5
   end

   %basis
   H    S
         1.301000E+01           1.968500E-02           0.000000E+00
         1.962000E+00           1.379770E-01           0.000000E+00
         4.446000E-01           4.781480E-01           0.000000E+00
         1.220000E-01           5.012400E-01           1.000000E+00
   H    P
         7.270000E-01           1.0000000
   N    S
         9.046000E+03           7.000000E-04          -1.530000E-04           0.000000E+00
         1.357000E+03           5.389000E-03          -1.208000E-03           0.000000E+00
         3.093000E+02           2.740600E-02          -5.992000E-03           0.000000E+00
         8.773000E+01           1.032070E-01          -2.454400E-02           0.000000E+00
         2.856000E+01           2.787230E-01          -6.745900E-02           0.000000E+00
         1.021000E+01           4.485400E-01          -1.580780E-01           0.000000E+00
         3.838000E+00           2.782380E-01          -1.218310E-01           0.000000E+00
         7.466000E-01           1.544000E-02           5.490030E-01           0.000000E+00
         2.248000E-01          -2.864000E-03           5.788150E-01           1.000000E+00
   N    P
         1.355000E+01           3.991900E-02           0.000000E+00
         2.917000E+00           2.171690E-01           0.000000E+00
         7.973000E-01           5.103190E-01           0.000000E+00
         2.185000E-01           4.622140E-01           1.000000E+00
   N    D
         8.170000E-01           1.0000000
   end

   *xyz 0 1
   H	 0.0000	-0.9377	-0.3816
   H	 0.8121	 0.4689	-0.3816
   H	-0.8121	 0.4689	-0.3816
   N	 0.0000	 0.0000	 0.0000

If one wants to assign different basis sets to different atoms of the same element, eg. ``aug-cc-pVDZ`` basis set need to be assigned to one of the ``H`` atom and for all other atoms ``cc-pVDZ`` need to be assigned, one can do that using custom basis but in that case one has to number the atoms (Here ``H`` atoms).

Example 5:

.. code-block:: shell 

   ! CCSD spinor

   %cc
   incore 5
   end

   %basis
   H    S
         1.301000E+01           1.968500E-02           0.000000E+00
         1.962000E+00           1.379770E-01           0.000000E+00
         4.446000E-01           4.781480E-01           0.000000E+00
         1.220000E-01           5.012400E-01           1.000000E+00
   H    P
         7.270000E-01           1.0000000
   H1   S
         1.301000E+01           1.968500E-02           0.000000E+00
         1.962000E+00           1.379770E-01           0.000000E+00
         4.446000E-01           4.781480E-01           0.000000E+00
         1.220000E-01           5.012400E-01           1.000000E+00
   H1   S
         0.0297400              1.0000000
   H1   P
         7.270000E-01           1.0000000
   H1   P
         0.1410000              1.0000000 
   N    S
         9.046000E+03           7.000000E-04          -1.530000E-04           0.000000E+00
         1.357000E+03           5.389000E-03          -1.208000E-03           0.000000E+00
         3.093000E+02           2.740600E-02          -5.992000E-03           0.000000E+00
         8.773000E+01           1.032070E-01          -2.454400E-02           0.000000E+00
         2.856000E+01           2.787230E-01          -6.745900E-02           0.000000E+00
         1.021000E+01           4.485400E-01          -1.580780E-01           0.000000E+00
         3.838000E+00           2.782380E-01          -1.218310E-01           0.000000E+00
         7.466000E-01           1.544000E-02           5.490030E-01           0.000000E+00
         2.248000E-01          -2.864000E-03           5.788150E-01           1.000000E+00
   N    P
         1.355000E+01           3.991900E-02           0.000000E+00
         2.917000E+00           2.171690E-01           0.000000E+00
         7.973000E-01           5.103190E-01           0.000000E+00
         2.185000E-01           4.622140E-01           1.000000E+00
   N    D
         8.170000E-01           1.0000000
   end

   *xyz 0 1
   H	 0.0000	-0.9377	-0.3816
   H	 0.8121	 0.4689	-0.3816
   H	-0.8121	 0.4689	-0.3816
   N	 0.0000	 0.0000	 0.0000

In the above example, ``H1`` (first hydrogen atom) is assigned to the custom basis set of ``aug-cc-pVDZ``, while all other ``H`` atoms and ``N`` are assigned to ``cc-pVDZ`` basis set. In the ``%basis`` block, only the element (eg. ``H`` here) written in the left side (the place of atom) assigns it to all unassigned atoms of the same element. The numbered atom (eg. ``H1`` in this case) takes the priority to be assigned differently.

.. note::

   Numbered atoms are prioritized over unnumbered atoms in the ``%basis`` block.
   Numbered atoms' basis sets have to be written after the basis set for the unnumbered atom of the same elemnt in the ``%basis`` block.

*******************
Universal basis set
*******************

If no basis set is assigned to an atom, it gets assigned using the universal basis set as in ``pos3`` in Example 1. Universal basis set is a single basis set that gets assigned to all atoms yet unassigned. Let's see an example:

Example 6:

.. code-block:: shell 

   ! CCSD spinor ccpvdz

   %cc
   incore 5
   end

   *xyz 0 1
   H	 0.0000	-0.9377	-0.3816
   H	 0.8121	 0.4689	-0.3816
   H	-0.8121	 0.4689	-0.3816
   N	 0.0000	 0.0000	 0.0000

Here, in Example 6, all atoms are assigned to ``cc-pVDZ`` basis set.

Now, let's discuss a complicated basis assignment (see following):

Example 7:

.. code-block:: shell 

   ! CCSD spinor def2qzvp

   %cc
   incore 5
   end

   %basis
   H    S
         1.301000E+01           1.968500E-02           0.000000E+00
         1.962000E+00           1.379770E-01           0.000000E+00
         4.446000E-01           4.781480E-01           0.000000E+00
         1.220000E-01           5.012400E-01           1.000000E+00
   H    P
         7.270000E-01           1.0000000
   H1   S
         1.301000E+01           1.968500E-02           0.000000E+00
         1.962000E+00           1.379770E-01           0.000000E+00
         4.446000E-01           4.781480E-01           0.000000E+00
         1.220000E-01           5.012400E-01           1.000000E+00
   H1   S
         0.0297400              1.0000000
   H1   P
         7.270000E-01           1.0000000
   H1   P
         0.1410000              1.0000000 
   end

   *xyz 0 1
   H	 0.0000	-0.9377	-0.3816
   H	 0.8121	 0.4689	-0.3816
   H	-0.8121	 0.4689	-0.3816   631g
   N	 0.0000	 0.0000	 0.0000

Here, ``H1`` is assigned to the custom basis of ``aug-cc-pVDZ`` as a numbered atom, ``H2`` is assigned to the custom basis of ``cc-pVDZ`` as an unnumbered atom, ``H3`` is assigned to ``6-31G`` basis set as atom-specific assignment and ``N`` atom is assigned to the ``def2-QZVP`` basis set as an universal basis set of this input file.

If an atom still remains unassigned (as in Example 7), it by default, gets assigned to ``STO-3G`` basis set.

Example 7:

.. code-block:: shell 

   ! CCSD spinor

   %cc
   incore 5
   end

   %basis
   H    S
         1.301000E+01           1.968500E-02           0.000000E+00
         1.962000E+00           1.379770E-01           0.000000E+00
         4.446000E-01           4.781480E-01           0.000000E+00
         1.220000E-01           5.012400E-01           1.000000E+00
   H    P
         7.270000E-01           1.0000000
   H1   S
         1.301000E+01           1.968500E-02           0.000000E+00
         1.962000E+00           1.379770E-01           0.000000E+00
         4.446000E-01           4.781480E-01           0.000000E+00
         1.220000E-01           5.012400E-01           1.000000E+00
   H1   S
         0.0297400              1.0000000
   H1   P
         7.270000E-01           1.0000000
   H1   P
         0.1410000              1.0000000 
   end

   *xyz 0 1
   H	 0.0000	-0.9377	-0.3816
   H	 0.8121	 0.4689	-0.3816
   H	-0.8121	 0.4689	-0.3816   631g
   N	 0.0000	 0.0000	 0.0000

Here, in Example 7, ``N`` atom is not assigned to any basis in any way. So, it takes ``STO-3G`` basis set.

Available basis sets
####################

**************
Main basis set
**************

There are several basis sets that are currently available in BAGH as a keyword. Basis sets keywords are usually slightly different than their respective names but they are simple and comprehensible eg. ``aug-cc-pVDZ`` basis set is called as ``augccpvdz`` etc. Following is the list of exact keywords of all currently available basis sets in BAGH.

.. raw:: html

   <!-- Dropdown Menu HTML -->
   <div class="dropdown">
       <button class="dropdown-btn">Choose main basis</button>
       <div class="dropdown-content" style="display: none;">
           <ul>
               <li>ano</li>
               <li>anorcc</li>
               <li>anoroosdz</li>
               <li>anoroostz</li>
               <li>roosdz</li>
               <li>roostz</li>
               <li>ccpvdz</li>
               <li>ccpvtz</li>
               <li>ccpvqz</li>
               <li>ccpv5z</li>
               <li>augccpvdz</li>
               <li>augccpvtz</li>
               <li>augccpvqz</li>
               <li>augccpv5z</li>
               <li>ccpvdzdk</li>
               <li>ccpvtzdk</li>
               <li>ccpvqzdk</li>
               <li>ccpv5zdk</li>
               <li>ccpvdzdkh</li>
               <li>ccpvtzdkh</li>
               <li>ccpvqzdkh</li>
               <li>ccpv5zdkh</li>
               <li>augccpvdzdk</li>
               <li>augccpvtzdk</li>
               <li>augccpvqzdk</li>
               <li>augccpv5zdk</li>
               <li>augccpvdzdkh</li>
               <li>augccpvtzdkh</li>
               <li>augccpvqzdkh</li>                                                                                  
               <li>augccpv5zdkh</li>                                                  
               <li>ccpvdzri</li>                                                                                  
               <li>ccpvtzri</li>                                                                                
               <li>ccpvqzri</li>                                                                                      
               <li>ccpv5zri</li>                                                                                      
               <li>augccpvdzri</li>
               <li>augccpvdzpri</li>
               <li>augccpvqzri</li>
               <li>augccpvtzri</li>
               <li>ccpvtzdk3</li>
               <li>ccpvqzdk3</li>
               <li>augccpvtzdk3</li>
               <li>augccpvqzdk3</li>
               <li>dyalldz</li>
               <li>dyalltz</li>
               <li>dyallqz</li>
               <li>faegredz</li>
               <li>iglo</li>
               <li>iglo3</li>
               <li>321++g</li>
               <li>321++g*</li>
               <li>321++gs</li>
               <li>321g</li>
               <li>321g*</li>
               <li>321gs</li>
               <li>431g</li>
               <li>631++g</li>
               <li>631++g*</li>
               <li>631++gs</li>
               <li>631++g**</li>
               <li>631++gss</li>
               <li>631+g</li>
               <li>631+g*</li>
               <li>631+gs</li>
               <li>631+g**</li>
               <li>631+gss</li>
               <li>6311++g</li>
               <li>6311++g*</li>
               <li>6311++gs</li>
               <li>6311++g**</li>
               <li>6311++gss</li>
               <li>6311+g</li>
               <li>6311+g*</li>
               <li>6311+gs</li>
               <li>6311+g**</li>
               <li>6311+gss</li>
               <li>6311g</li>
               <li>6311g*</li>
               <li>6311gs</li>
               <li>6311g**</li>
               <li>6311gss</li>
               <li>631g</li>
               <li>631g*</li>
               <li>631gs</li>
               <li>631g**</li>
               <li>631gss</li>
               <li>sto3g</li>
               <li>sto6g</li>
               <li>minao</li>
               <li>dz</li>
               <li>dzpdunning</li>
               <li>dzvp</li>
               <li>dzvp2</li>
               <li>dzp</li>
               <li>tzp</li>
               <li>qzp</li>
               <li>adzp</li>
               <li>atzp</li>
               <li>aqzp</li>
               <li>dzpdk</li>
               <li>tzpdk</li>
               <li>qzpdk</li>
               <li>dzpdkh</li>
               <li>tzpdkh</li>
               <li>qzpdkh</li>
               <li>def2svp</li>
               <li>def2svpd</li>
               <li>def2tzvpd</li>
               <li>def2tzvppd</li>
               <li>def2tzvpp</li>
               <li>def2tzvp</li>
               <li>def2qzvpd</li>
               <li>def2qzvppd</li>
               <li>def2qzvpp</li>
               <li>def2qzvp</li>
               <li>def2svpri</li>
               <li>def2svpdri</li>
               <li>def2tzvpri</li>
               <li>def2tzvpdri</li>
               <li>def2tzvppri</li>
               <li>def2tzvppdri</li>
               <li>def2qzvpri</li>
               <li>def2qzvppri</li>
               <li>def2qzvppdri</li>
               <li>tzv</li>
               <li>weigend</li>
               <li>weigend+etb</li>
               <li>weigendcfit</li>
               <li>weigendjfit</li>
               <li>demon</li>
               <li>demoncfit</li>
               <li>ahlrichs</li>
               <li>ahlrichscfit</li>
               <li>ccpvtzfit</li>
               <li>ccpvdzfit</li>
               <li>ccpwcvtzmp2fit</li>
               <li>ccpvqzmp2fit</li>
               <li>ccpv5zmp2fit</li>
               <li>augccpwcvtzmp2fit</li>
               <li>augccpvqzmp2fit</li>
               <li>augccpv5zmp2fit</li>
               <li>ccpcvdz</li>
               <li>ccpcvtz</li>
               <li>ccpcvqz</li>
               <li>ccpcv5z</li>
               <li>ccpcv6z</li>
               <li>ccpwcvdz</li>
               <li>ccpwcvtz</li>
               <li>ccpwcvqz</li>
               <li>ccpwcv5z</li>
               <li>ccpwcvdzdk</li>
               <li>ccpwcvtzdk</li>
               <li>ccpwcvqzdk</li>
               <li>ccpwcv5zdk</li>
               <li>ccpwcvtzdk3</li>
               <li>ccpwcvqzdk3</li>
               <li>augccpwcvdz</li>
               <li>augccpwcvtz</li>
               <li>augccpwcvqz</li>
               <li>augccpwcv5z</li>
               <li>augccpwcvtzdk</li>
               <li>augccpwcvqzdk</li>
               <li>augccpwcv5zdk</li>
               <li>augccpwcvtzdk3</li>
               <li>augccpwcvqzdk3</li>
               <li>dgaussa1cfit</li>
               <li>dgaussa1xfit</li>
               <li>dgaussa2cfit</li>
               <li>dgaussa2xfit</li>
               <li>ccpvdzpp</li>
               <li>ccpvtzpp</li>
               <li>ccpvqzpp</li>
               <li>ccpv5zpp</li>
               <li>crenbl</li>
               <li>crenbs</li>
               <li>lanl2dz</li>
               <li>lanl2tz</li>
               <li>lanl08</li>
               <li>sbkjc</li>
               <li>stuttgart</li>
               <li>stuttgartdz</li>
               <li>stuttgartrlc</li>
               <li>stuttgartrsc</li>
               <li>stuttgartrsc_mdf</li>
               <li>ccpwcvdzpp</li>
               <li>ccpwcvtzpp</li>
               <li>ccpwcvqzpp</li>
               <li>ccpwcv5zpp</li>
               <li>ccpvdzppnr</li>
               <li>ccpvtzppnr</li>
               <li>augccpvdzpp</li>
               <li>augccpvtzpp</li>
               <li>augccpvqzpp</li>
               <li>augccpv5zpp</li>
               <li>pc0</li>
               <li>pc1</li>
               <li>pc2</li>
               <li>pc3</li>
               <li>pc4</li>
               <li>augpc0</li>
               <li>augpc1</li>
               <li>augpc2</li>
               <li>augpc3</li>
               <li>augpc4</li>
               <li>pcseg0</li>
               <li>pcseg1</li>
               <li>pcseg2</li>
               <li>pcseg3</li>
               <li>pcseg4</li>
               <li>augpcseg0</li>
               <li>augpcseg1</li>
               <li>augpcseg2</li>
               <li>augpcseg3</li>
               <li>augpcseg4</li>
               <li>sarcdkh</li>
               <li>bfdvdz</li>
               <li>bfdvtz</li>
               <li>bfdvqz</li>
               <li>bfdv5z</li>
               <li>bfd</li>
               <li>bfdpp</li>
               <li>ccpcvdzf12optri</li>
               <li>ccpcvtzf12optri</li>
               <li>ccpcvqzf12optri</li>
               <li>ccpvdzf12optri</li>
               <li>ccpvtzf12optri</li>
               <li>ccpvqzf12optri</li>
               <li>ccpv5zf12</li>
               <li>ccpvdzf12rev2</li>
               <li>ccpvtzf12rev2</li>
               <li>ccpvqzf12rev2</li>
               <li>ccpv5zf12rev2</li>
               <li>ccpvdzf12nz</li>
               <li>ccpvtzf12nz</li>
               <li>ccpvqzf12nz</li>
               <li>augccpvdzoptri</li>
               <li>augccpvtzoptri</li>
               <li>augccpvqzoptri</li>
               <li>augccpv5zoptri</li>
               <li>pobtzvp</li>
               <li>pobtzvpp</li>
               <li>crystalccpvdz</li>
               <li>ccecp</li>
               <li>ccecpccpvdz</li>
               <li>ccecpccpvtz</li>
               <li>ccecpccpvqz</li>
               <li>ccecpccpv5z</li>
               <li>ccecpccpv6z</li>
               <li>ccecpaugccpvdz</li>
               <li>ccecpaugccpvtz</li>
               <li>ccecpaugccpvqz</li>
               <li>ccecpaugccpv5z</li>
               <li>ccecpaugccpv6z</li>
               <li>ccecphe</li>
               <li>ccecpheccpvdz</li>
               <li>ccecpheccpvtz</li>
               <li>ccecpheccpvqz</li>
               <li>ccecpheccpv5z</li>
               <li>ccecpheccpv6z</li>
               <li>ccecpheaugccpvdz</li>
               <li>ccecpheaugccpvtz</li>
               <li>ccecpheaugccpvqz</li>
               <li>ccecpheaugccpv5z</li>
               <li>ccecpheaugccpv6z</li>
               <li>ccecpreg</li>
               <li>ccecpregccpvdz</li>
               <li>ccecpregccpvtz</li>
               <li>ccecpregccpvqz</li>
               <li>ccecpregccpv5z</li>
               <li>ccecpregaugccpvdz</li>
               <li>ccecpregaugccpvtz</li>
               <li>ccecpregaugccpvqz</li>
               <li>ccecpregaugccpv5z</li>
           </ul>
       </div>
   </div>

There are additional dyall basis sets, for which a separate discussion is done in a different section.

**********************
JK auxiliary basis set
**********************

Following is the list of exact keywords of all currently available JK auxiliary basis sets in BAGH. JK basis can only be assigned using universal basis assignment (as in pos4 in Example 1).

.. raw:: html

   <!-- Dropdown Menu HTML -->
   <div class="dropdown">
       <button class="dropdown-btn">Choose JK auxiliary basis</button>
       <div class="dropdown-content" style="display: none;">
           <ul>
               <li>ccpvdzjkfit</li>                                                                               
               <li>ccpvtzjkfit</li>                                                    
               <li>ccpvqzjkfit</li>                                                                                   
               <li>ccpv5zjkfit</li>
               <li>weigendjkfit</li>
               <li>augccpvdzjkfit</li>                                                                                  
               <li>augccpvdzpjkfit</li>                                                                                
               <li>augccpvtzjkfit</li>                                                                               
               <li>augccpvqzjkfit</li>
               <li>augccpv5zjkfit</li>
               <li>heavyaugccpvdzjkfit</li>
               <li>heavyaugccpvtzjkfit</li>
               <li>def2svpjfit</li>
               <li>def2svpjkfit</li>
               <li>def2tzvpjfit</li>
               <li>def2tzvpjkfit</li>
               <li>def2tzvppjfit</li>
               <li>def2tzvppjkfit</li>
               <li>def2qzvpjfit</li>
               <li>def2qzvpjkfit</li>
               <li>def2qzvppjfit</li>
               <li>def2qzvppjkfit</li>
               <li>def2universaljfit</li>
               <li>def2universaljkfit</li>
           </ul>
       </div>
   </div>

**********************
RI auxiliary basis set
**********************

Following is the list of exact keywords of all currently available RI auxiliary basis sets in BAGH. RI basis can only be assigned using universal basis assignment (as in pos5 in Example 1).

.. raw:: html

   <!-- Dropdown Menu HTML -->
   <div class="dropdown">
       <button class="dropdown-btn">Choose RI auxiliary basis</button>
       <div class="dropdown-content" style="display: none;">
           <ul>
               <li>cc-pvdz-ri</li>
               <li>cc-pvtz-ri</li>
               <li>cc-pvqz-ri</li>
               <li>cc-pv5z-ri</li>
               <li>cc-pv6z-ri</li>
               <li>cc-pwcvdz-ri</li>
               <li>cc-pwcvtz-ri</li>
               <li>cc-pwcvqz-ri</li>
               <li>cc-pwcv5z-ri</li>
               <li>cc-pwcv6z-ri</li>
               <li>aug-cc-pvdz-ri</li>
               <li>aug-cc-pvtz-ri</li>
               <li>aug-cc-pvqz-ri</li>
               <li>aug-cc-pv5z-ri</li>
               <li>aug-cc-pv6z-ri</li>
               <li>def2-svp-ri</li>
               <li>def2-tzvp-ri</li>
               <li>def2-qzvp-ri</li>
           </ul>
       </div>
   </div>

.. raw:: html

   <!-- Custom CSS -->
   <style>
       .dropdown {
           margin: 20px 0;
           font-family: Arial, sans-serif;
           position: relative;
           width: 200px;
       }

       .dropdown-btn {
           cursor: pointer;
           background-color: #007bff;
           color: white;
           border: none;
           padding: 10px 15px;
           font-size: 16px;
           border-radius: 5px;
           text-align: left;
           width: 100%;
       }

       .dropdown-btn:hover {
           background-color: #0056b3;
       }

       .dropdown-content {
           position: absolute;
           top: 100%;
           left: 0;
           right: 0;
           max-height: 200px; /* Limits the height of the dropdown */
           overflow-y: auto; /* Adds vertical scrolling */
           border: 1px solid #ddd;
           background-color: #f9f9f9;
           border-radius: 5px;
           box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
           z-index: 1000;
       }

       .dropdown-content ul {
           list-style: none;
           padding: 0;
           margin: 0;
       }

       .dropdown-content li {
           padding: 10px;
           cursor: pointer;
           border-bottom: 1px solid #ddd;
       }

       .dropdown-content li:hover {
           background-color: #e9e9e9;
       }

       .dropdown-content li:last-child {
           border-bottom: none;
       }
   </style>

.. raw:: html

   <!-- Custom JavaScript -->
   <script>
      document.addEventListener("DOMContentLoaded", function() {
          const dropdowns = document.querySelectorAll(".dropdown");
      
          dropdowns.forEach((dropdown) => {
              const dropdownBtn = dropdown.querySelector(".dropdown-btn");
              const dropdownContent = dropdown.querySelector(".dropdown-content");
      
              dropdownBtn.addEventListener("click", function() {
                  const isHidden = dropdownContent.style.display === "none" || dropdownContent.style.display === "";
                  dropdownContent.style.display = isHidden ? "block" : "none";
              });
      
              // Hide dropdown if clicked outside
              document.addEventListener("click", function(event) {
                  if (!dropdown.contains(event.target)) {
                      dropdownContent.style.display = "none";
                  }
              });
          });
      });
   </script>

Uncontraction of the basis sets
###############################

The atom-specific and universal basis sets can be uncontracted by adding a prefix ``unc-`` to the name of the basis set. eg. for an uncontracted version of the ``aug-cc-pVDZ`` basis set, one has to write ``unc-augccpvdz`` in the place of the basis set. See the following example:

Example 8:

.. code-block:: shell 

   ! CCSD spinor unc-augccpvdz

   %cc
   incore 5
   end

   *xyz 0 1
   H	 0.0000	-0.9377	-0.3816
   H	 0.8121	 0.4689	-0.3816
   H	-0.8121	 0.4689	-0.3816 unc-631g
   N	 0.0000	 0.0000	 0.0000

In the custom basis, one can uncontract beforehand if they need it.
