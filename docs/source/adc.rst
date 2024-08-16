Algebraic Diagrammatic Construction (ADC)
########################################

The Algebraic Diagrammatic Construction (ADC) is a post-Hartree Fock ab-initio Hermitian method to
calculate vertical ionization potential (IP), electron-attachment energy (EA) and excitation energy
(EE) in a direct-difference of energy based way. ADCis implemented using Intermediate State
Representation (ISR) in BAGH. The ADC secular matrix is expanded in perturbation series. A
truncation at nth order leads to ADC(n) method. This method incorporates correlation from its
second-order (ADC(2) method). An extender version of ADC(2), namely ADC(2)-X is considered to be
an ad-hoc extension up to the first order terms in doubles-doubles block (2h1p-2h1p for IP and
2p1h-2p1h for EA). In BAGH, ADC(n) is implemented up to 3 rd order.
