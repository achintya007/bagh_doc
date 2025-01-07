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
================================

This is an example of a dropdown menu with a scrollable list and a dragger (scrollbar). You can select specific items from the list.

.. raw:: html

   <!-- Dropdown Menu HTML -->
   <div class="dropdown">
       <button class="dropdown-btn">Select a Number</button>
       <div class="dropdown-content" style="display: none;">
           <ul>
               <!-- Add a lot of numbers here -->
               <li>1</li>
               <li>2</li>
               <li>3</li>
               <li>4</li>
               <li>5</li>
               <li>6</li>
               <li>7</li>
               <li>8</li>
               <li>9</li>
               <li>10</li>
               <li>11</li>
               <li>12</li>
               <li>13</li>
               <li>14</li>
               <li>15</li>
               <li>16</li>
               <li>17</li>
               <li>18</li>
               <li>19</li>
               <li>20</li>
               <!-- You can add more items -->
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

