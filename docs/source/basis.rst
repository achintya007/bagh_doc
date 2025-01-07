Ways to assign basis sets
#########################

Similar to the other part of the input file in BAGH, the basis set assignment is case and space-insensitive (as long as different keywords are separated) unless mentioned otherwise. There are 3 ways basis sets are assigned to different atoms in BAGH:

1. Atom-specific basis sets
2. Custom basis set
3. Universal basis set

The highest priority is given to the atom-specific basis sets, and the lowest goes to the universal basis set assignment. If no basis set is assigned to an atom, by default, STO-3G is assigned to it.

***********************
Atom-specific basis set
***********************

****************
Custom basis set
****************

*******************
Universal basis set
*******************










Available basis sets
####################

There are several basis sets that are currently available in BAGH as a keyword. Basis sets keywords are usually slightly different than their respective names but they are simple and comprehensible eg. ``aug-cc-pVDZ`` basis set is called as ``augccpvdz`` etc. Following is the list of exact keywords of all currently available basis sets in BAGH.

.. raw:: html

   <!-- Dropdown Menu HTML -->
   <div class="dropdown">
       <button class="dropdown-btn">Choose a basis</button>
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
           const dropdownBtn = document.querySelector(".dropdown-btn");
           const dropdownContent = document.querySelector(".dropdown-content");

           dropdownBtn.addEventListener("click", function() {
               const isHidden = dropdownContent.style.display === "none" || dropdownContent.style.display === "";
               dropdownContent.style.display = isHidden ? "block" : "none";
           });

           // Hide dropdown if clicked outside
           document.addEventListener("click", function(event) {
               if (!dropdownBtn.contains(event.target) && !dropdownContent.contains(event.target)) {
                   dropdownContent.style.display = "none";
               }
           });
       });
   </script>

