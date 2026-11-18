Features
#########

- RHF MP2
- RHF CCSD, CCSD(T)
- RF IP- and EA-EOM-CCSD
- RHF EE-EOM-CCSD
- Density fitting approximation for RHF Couple cluster and EOMCC
- Spin Orbital MP2
- RHFSO EE-EOM-CCSD
- Spin Orbital IP-EOM-CCSD, EA-EOM-CCSD, EE-EOM-CCSD with triples correction for IP and EA
- Projector based embedding
- Frozen Natural Orbital for RHF -CCSD, RHF-IP-CCSD and Triples corrected  RHF-IP-CCSD
- First order properties for RHF and spin orbital CCSD
- Exact two and four component approaches for relativistic  coupled cluster method
- Relativistic CCSDT and CCSDT(Q) with a Laplace-transform / THC treatment of the perturbative quadruples correction
- Low-memory relativistic MP2 (THC-LT-MP2) on the X2CAMF interface: the direct diagram via tensor hypercontraction and a Laplace transform, the exchange diagram via the resolution of the identity with exact denominators, with in-core and out-of-core paths and SCS/SOS scaling
- Two-component X2CAMF double-hybrid DFT (B2PLYP, B2GPPLYP, mPW2PLYP, PBE0-DH, PBE-QIDH, PBE0-2 or custom) with the PT2 term by exact RI-MP2 (RI-DH) or THC + Laplace transform (THC-LT-DH)
- Natural spinors for the relativistic calculations
- Efficient treatment for core-ionization
- XES Spectra for closed shell molecules
- CASCI/CASSCF and strongly-contracted NEVPT2 for two-component (X2CAMF) spinor references
- DLPNO-IP-ADC(2) and DLPNO-IP-ADC(3) for closed-shell references, in pair natural orbitals over projected-atomic-orbital domains, with a readable pure-Python implementation of the same method selectable by one keyword
- DLPNO-EE-ADC(3) singlet excitation energies for closed-shell references, state-specific, as a composite over nested local spaces, run entirely in compiled code
- Intermediate-Hamiltonian Fock-space CCSD (IHFSCCSD) with spin-orbit coupling on a closed-shell reference: excitation energies (1,1), double ionization potentials (0,2), double electron attachments (2,0), and triple electron attachment (3,0) with the new scheme that needs only the (1,0) amplitudes
- Folded (doubles-free) QED-EE-ADC(2) for molecules in an optical cavity on a coherent-state QED-RHF reference with the dipole self-energy in the integrals: exact with density fitting, scaled-opposite-spin with Laplace-transformed denominators at O(N^4) (LT-SOS), or exact with THC and Laplace transform (THC-LT)
