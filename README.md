# NMR-Structure-Calculation-using-CYANA-and-ASDP
Created by: Changpeng Lu  
This work is the summary of rotation in Guy's Lab at IQB, Rutgers. It is a general guide of NMR protein prediction for L22 protein using CYANA, ASDP with CYANA and ASDP with XPLOR, respectively. In this work, I will describe the pipeline of running each program and how to analyze results by PSVS, a powerful analysis tool for protein prediction. It also contains the examples of several PDBstat usages which helps generate or modify files for input or PSVS analysis. I hope this could give other rotation students help to have sense of NMR structure calculations. More details that are connected with experiments need to be further considered.  
Thanks to Guy, Roberto and Swapna for interpretation of details.  
## List of programs and reference included
1. CYANA: Automated NMR Structure Calculation Program. For more information about the method, check out the [paper](https://link.springer.com/protocol/10.1385/1-59259-809-9:353) and [tutorial](http://www.cyana.org/wiki/index.php/Main_Page).  
2. ASDP: Automated Determination Program of Protein Structures from NMR Data. For more information about the method, check out the [paper](https://onlinelibrary.wiley.com/doi/full/10.1002/prot.20820) and simple [tutorial](http://www.nmr2.buffalo.edu/nesg.wiki/AutoStructure_Structure_Determination_Program).  
3. PSVS: Protein Structure Validation Software suite. PSVS could be accessed through the [link](http://psvs-1_5-dev.nesg.org/) and simple [tutorial](http://www.nmr2.buffalo.edu/nesg.wiki/PSVS).  
4. Pdbstat: Analysis, conversion, and manipulation of coordinate and constraint files for protein structure determination. For more information about the method, check out the [paper](https://link.springer.com/article/10.1007/s10858-013-9753-7) and simple [tutorial](http://www.nmr2.buffalo.edu/nesg.wiki/PdbStat).  
I will describe implementation of these programs in the following in details, so you don't need to read all materials mentioned above to complete the work. However, they are all recommended if you want to have a higher level of theory knowledge of them.  
  
## A brief introduction for those lack of computer science background
When we do protein structure calculation, we use known data information such as sequence information, NOESY peak etc. to build the knowledge of the specific protein structure for the program. We will first let the program read these information, then tell it how to use them to generate the structure. In order to do this, we need to use commands of each program which are several lines of codes that could tell the program what actions to take. The place where save these commands is called script files. We only need to execute script files in this work and the program will automatically read these commands in the script file and run by following these commands in order. I will describe in details how to prepare input files, script files and how to run them in the example of L22.  

## CYANA
CYANA is a macromolecular structure calculation algorithm based on simulated annealing molecular dynamics calculations in torsional angle space, in contrast to Cartesian space [1,2].  Here the only degrees of freedom are torsion angles with covalent structure parameters kept fixed, thereby significantly decreasing the number of degrees of freedom in the calculation.  
### Getting Started
#### Input files
There are two kinds of input files, the files directly received from NMR analysis, e.g., peak list files and the files that you generate to execute the program easily, e.g., script files. The latter kind of files make the process much easier.  
##### Files from NMR analysis
1. NOESY peak lists  
ASDP can accept 3D and 4D NOESY peak lists. To know their format, here are exact peak lists for L22 protein: [15N-edited NOESY](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/cnoeabs.peaks) and [13C-edited NOESY](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/cnoeabs.peaks).  
2. Dihedral Angle Restraints  
In CYANA, dihedral angle constraints are specified in a .aco file. For example, the FOUND module derives dihedral angle constraints based on local NOE data. Programs such as TALOS provide backbone phi and psi torsion angle constraints based on chemical shifts. In our structure determination pipeline we often make use of TALOS-derived backbone torsion angle constraints in our calculations.  
Here is the exact [ACO Angle restraint file](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/talosn.aco) for L22 protein.  
3. Residue Dipolar Coupling restraints  
In CYANA, residue dipolar coupling constraints are specified in a .rdc file. Here is the exact [RDC restraint file](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/911.rdc).  
4. Chemical Shift Lists  
In CYANA, chemical shift list files are specified in .prot file. Here is the exact [PROT file](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/noec.prot).  
One case is that you have file in BMRB format, then you need to convert it into PROT format.  
5. Sequence  
It is the sequence file which contain the sequence information of target protein. Here is the exact [sequence file](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/rdc.seq)
##### Script files
1. Initial file (init.cya) which contains some basic information of running and will be read when starting CYANA. Here is the exact [initial file](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/init.cya)  
2. A script which contains CYANA commands, functions and variables. [More details](http://www.cyana.org/wiki/index.php/CYANA_3.0_Reference_Manual). Here is the exact [script file](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/autorun.cya)  
### Brief introduction for commands   
Both initial file and scripts use CYANA commands, functions and variables. Since initial file will automatically run when starting CYANA, it is better to contain commands that doesn’t need to test in initial file when doing different protein structure prediction and leave commands that are needed to play in script files.  
1. Main Commands  
* peaks := <names of NOESY peak lists> (eg. nnoeabs.peaks, cnoeabs.peaks)  
* prot := <names of chemical shift lists, without .prot suffix> (eg. noec)  
* contraints := <non-NOE constraints> (eg. talosn.aco, 911.rdc)  
* structures := <number of initial, final structures>  
* steps := <number of torsion angle dynamics steps>  
* randomseed := <random number generator seed>  
* protocol := <log file>  
* noeassign peaks=<names of the input peak lists> prot=<names of the input chemical shifts lists> autoaco keep=<name of a CYANA macro or command that selects those assigned peaks whose assignment should be kept unchanged>  
2. Selective Commands  
* tolerance := <chemical shift tolerances>  (The first and second numbers apply to protons, the third number to 13C or 15N)  
* weight_rdc = <weight for RDC restraints>  
* cut_rdc = <cutoff for RDC violation output>  
* upl_values := <upper limit distance restraint values>  
3. Commands for initial file  
— script starts —  
name := <protein name, used for output file names>  
rmdsrange := <residue range for RMSD calculation> (eg. 8..70)  
cut_upl = <cutoff for upper limit distance restraint violations>  
cyanalib (read standard library)  
read seq <sequence file> (read protein sequence)  
rdcscale  
nproc := <number of >  
- End -  
  
### Run

