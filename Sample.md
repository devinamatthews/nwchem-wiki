# Sample input files

## Water SCF calculation and geometry optimization in a 6-31g basis

The [Getting Started](Getting_Started "wikilink") input file performs a
geometry optimization in a single task. A single point SCF energy
calculation is performed and then restarted to perform the optimization
(both could of course be performed in a single task).

### Job 1. Single point SCF energy

` start h2o`  
` title "Water in 6-31g basis set"`  
` `  
` geometry units au`  
`   O      0.00000000    0.00000000    0.00000000`  
`   H      0.00000000    1.43042809   -1.10715266`  
`   H      0.00000000   -1.43042809   -1.10715266`  
` end`  
` basis`  
`   H library 6-31g`  
`   O library 6-31g`  
` end`  
` task scf`

The final energy should be -75.983998.

### Job 2. Restarting and perform a geometry optimization

` restart h2o`  
` title "Water geometry optimization"`  
` `  
` task scf optimize`

There is no need to specify anything that has not changed from the
previous input deck, though it will do no harm to repeat it.

## Compute the polarizability of Ne using finite field

### Job 1. Compute the atomic energy

` start ne`  
` title "Neon"`  
` geometry; ne 0 0 0; end`  
` basis spherical `  
`   ne library aug-cc-pvdz`  
` end`  
` scf; thresh 1e-10; end`  
` task scf`

The final energy should be -128.496350.

### Job 2. Compute the energy with applied field

An external field may be simulated with point charges. The charges here
apply a field of magnitude 0.01 atomic units to the atom at the origin.
Since the basis functions have not been reordered by the additional
centers we can also restart from the previous vectors, which is the
default for a restart job.

` restart ne`  
` title "Neon in electric field"`  
` geometry units atomic`  
`   bq1 0 0 100 charge 50`  
`   ne  0 0 0`  
`   bq2 0 0 -100 charge -50`  
` end`  
` task scf`

The final energy should be -128.496441, which together with the previous
field-free result yields an estimate for the polarizability of 1.83
atomic units. Note that by [default](Geometry "wikilink") NWChem does
not include the interaction between the two point charges in the total
energy.

## SCF energy of H<sub>2</sub>CO using ECPs for C and O

The following will compute the SCF energy for formaldehyde with ECPs on
the Carbon and Oxygen centers.

`title "formaldehyde ECP deck"`  
  
`start ecpchho`  
  
`geometry units au`  
`  C         0.000000  0.000000 -1.025176`  
`  O         0.000000  0.000000  1.280289`  
`  H         0.000000  1.767475 -2.045628`  
`  H         0.000000 -1.767475 -2.045628`  
`end`  
  
`basis `  
`  C  SP`  
`   0.1675097360D+02 -0.7812840500D-01  0.3088908800D-01`  
`   0.2888377460D+01 -0.3741108860D+00  0.2645728130D+00`  
`   0.6904575040D+00  0.1229059640D+01  0.8225024920D+00`  
`  C  SP`  
`   0.1813976910D+00  0.1000000000D+01  0.1000000000D+01`  
`  C  D`  
`   0.8000000000D+00  0.1000000000D+01`  
`  C  F`  
`   0.1000000000D+01  0.1000000000D+01`  
`  O  SP`  
`   0.1842936330D+02 -0.1218775590D+00  0.5975796600D-01`  
`   0.4047420810D+01 -0.1962142380D+00  0.3267825930D+00`  
`   0.1093836980D+01  0.1156987900D+01  0.7484058930D+00`  
`  O  SP`  
`   0.2906290230D+00  0.1000000000D+01  0.1000000000D+01`  
`  O  D`  
`   0.8000000000D+00  0.1000000000D+01`  
`  O  F`  
`   0.1100000000D+01  0.1000000000D+01`  
`  H  S`  
`   0.1873113696D+02  0.3349460434D-01`  
`   0.2825394365D+01  0.2347269535D+00`  
`   0.6401216923D+00  0.8137573262D+00`  
`  H  S   `  
`   0.1612777588D+00  0.1000000000D+01`  
`end`  
  
`ecp`  
`  C nelec 2`  
`  C ul`  
`        1       80.0000000       -1.60000000`  
`        1       30.0000000       -0.40000000`  
`        2        0.5498205       -0.03990210`  
`  C s`  
`        0        0.7374760        0.63810832`  
`        0      135.2354832       11.00916230`  
`        2        8.5605569       20.13797020`  
`  C p`  
`        2       10.6863587       -3.24684280`  
`        2       23.4979897        0.78505765`  
`  O nelec 2`  
`  O ul`  
`        1       80.0000000       -1.60000000`  
`        1       30.0000000       -0.40000000`  
`        2        1.0953760       -0.06623814`  
`  O s`  
`        0        0.9212952        0.39552179`  
`        0       28.6481971        2.51654843`  
`        2        9.3033500       17.04478500`  
`  O p`  
`        2       52.3427019       27.97790770`  
`        2       30.7220233      -16.49630500`  
`end`  
  
`scf`  
`  vectors input hcore`  
`  maxiter 20`  
`end`  
  
`task scf`

This should produce the following output:

`      Final RHF  results `  
`      ------------------ `  
`     `  
`        Total SCF energy =    -22.507927218024`  
`     One electron energy =    -71.508730162974`  
`     Two electron energy =     31.201960019808`  
`Nuclear repulsion energy =     17.798842925142`

## MP2 optimization and CCSD(T) on nitrogen

The following performs an MP2 geometry optimization followed by a
CCSD(T) energy evaluation at the converged geometry. A Dunning
correlation-consistent triple-zeta basis is used. The default of
Cartesian basis functions must be overridden using the keyword spherical
on the BASIS directive. The 1s core orbitals are frozen in both the MP2
and coupled-cluster calculations (note that these must separately
specified). The final MP2 energy is -109.383276, and the CCSD(T) energy
is -109.399662.

`start n2 `  
  
`geometry`  
`  symmetry d2h`  
`  n 0 0 0.542`  
`end`  
  
`basis spherical`  
`  n library cc-pvtz`  
`end`  
  
`mp2`  
`  freeze core`  
`end`  
  
`task mp2 optimize`  
  
`ccsd`  
`  freeze core`  
`end`  
  
`task ccsd(t)`
