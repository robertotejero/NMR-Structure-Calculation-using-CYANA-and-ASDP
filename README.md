# NMR-Structure-Calculation-using-CYANA-and-ASDP
Created by: Changpeng Lu  
Created at: May 2019  
This work is the summary of rotation in Guy's Lab at IQB, Rutgers. It is a general guide of NMR protein structure calculation for L22 protein using CYANA, ASDP with CYANA and ASDP with XPLOR, respectively. In this work, I will describe the pipeline of running each program and how to analyze results by PSVS, a powerful analysis tool for protein prediction. It also contains the examples of several PDBstat usages which helps generate or modify files for input or PSVS analysis. I hope this could give other rotation students help to have sense of NMR structure calculations. More details that are connected with experiments need to be further considered.  
Thanks to Guy, Roberto, Gaohua and Swapna for interpretation of details.  
## List of programs and reference included
1. CYANA: Automated NMR Structure Calculation Program. For more information about the method, check out the [paper](https://link.springer.com/protocol/10.1385/1-59259-809-9:353) and [tutorial](http://www.cyana.org/wiki/index.php/Main_Page).  
The version of CYANA in this report is 3.97. It is placed in /farm/software/cyana-3.97/cyana.  
2. ASDP: Automated Determination Program of Protein Structures from NMR Data. For more information about the method, check out the [paper](https://onlinelibrary.wiley.com/doi/full/10.1002/prot.20820) and simple [tutorial](http://www.nmr2.buffalo.edu/nesg.wiki/AutoStructure_Structure_Determination_Program).  
The version of ASDP in this report is 1.0. ASDP/CYANA could be run successfully in /farm/software/asdp/asdp-1.0-release/bin/asdp.  
ASDP/Xplor could be run successfully in /opt/software/asdp-1.0-release/bin/asdp.  
3. PSVS: Protein Structure Validation Software suite. PSVS could be accessed through the [link](http://psvs-1_5-dev.nesg.org/) and simple [tutorial](http://www.nmr2.buffalo.edu/nesg.wiki/PSVS).  
4. Pdbstat: Analysis, conversion, and manipulation of coordinate and constraint files for protein structure determination. For more information about the method, check out the [paper](https://link.springer.com/article/10.1007/s10858-013-9753-7) and simple [tutorial](http://www.nmr2.buffalo.edu/nesg.wiki/PdbStat).  
The latest version of Pdbstat is placed in /farm/software/Pdbstat/PdbStat-5.19/pdbstat.  
I will describe implementation of these programs in the following in details, so you don't need to read all materials mentioned above to complete the work. However, they are all recommended if you want to have a higher level of theory knowledge of them.  
## Notice  
ASDP 1.0 has been run successfully locally on scion and it turned out that it couldn't be run remotely. If the program is stopped at working at Cycle-1.0, maybe it worth to try on scion or other lab computers.  
## A brief introduction for those lack of computer science background
When we do protein structure calculation, we use known data information such as sequence information, NOESY peak etc. to build the knowledge of the specific protein structure for the program. We will first let the program read these information, then tell it how to use them to generate the structure. In order to do this, we need to use commands of each program which are several lines of codes that could tell the program what actions to take. The place where save these commands is called script files. We only need to execute script files in this work and the program will automatically read these commands in the script file and run by following these commands in order. I will describe in details how to prepare input files, script files and how to run them in the example of L22.  

## CYANA
CYANA is a macromolecular structure calculation algorithm based on simulated annealing molecular dynamics calculations in torsional angle space, in contrast to Cartesian space.  Here the only degrees of freedom are torsion angles with covalent structure parameters kept fixed, thereby significantly decreasing the number of degrees of freedom in the calculation.  
### Getting Started
#### Input files
There are two kinds of input files, the files directly received from NMR analysis, e.g., peak list files and the files that you generate to execute the program easily, e.g., script files. The latter kind of files make the process much easier.  
##### Files from NMR analysis
1. NOESY peak lists  
ASDP can accept 3D and 4D NOESY peak lists. To know their format, here are exact peak lists for L22 protein: [15N-edited NOESY](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/nnoeabs.peaks) and [13C-edited NOESY](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/cnoeabs.peaks).  
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
#### Brief introduction for commands   
Both initial file and scripts use CYANA commands, functions and variables. Since initial file will automatically run when starting CYANA, it is better to contain commands that doesn’t need to test in initial file when doing different protein structure prediction and leave commands that are needed to play in script files.  
1. Main Commands  
* peaks \:= \<names of NOESY peak lists\> (eg. nnoeabs.peaks, cnoeabs.peaks)
* prot \:= \<names of chemical shift lists, without .prot suffix\> (eg. noec)
* contraints \:= \<non-NOE constraints\> (eg. talosn.aco, 911.rdc)
* structures \:= \<number of initial, final structures\>
* steps \:= \<number of torsion angle dynamics steps\>
* randomseed \:= \<random number generator seed\>
* protocol \:= \<log file\>
* noeassign peaks\=\<names of the input peak lists\> prot\=\<names of the input chemical shifts lists\> autoaco keep\=\<name of a CYANA macro or command that selects those assigned peaks whose assignment should be kept unchanged\>  
* Before using “keep” flag, peaks should be selected first:  
```
subroutine KEEP  
  peaks select "*, *"  
end  
```

2. Selective Commands  
* tolerance := \<chemical shift tolerances\>  (The first and second numbers apply to protons, the third number to 13C or 15N)
* weight_rdc = \<weight for RDC restraints\>
* cut_rdc = \<cutoff for RDC violation output\>
* upl_values := \<upper limit distance restraint values\>
3. Commands for initial file  
name := \<protein name, used for output file names\>
rmdsrange := \<residue range for RMSD calculation\> (eg. 8..70)
cut_upl = \<cutoff for upper limit distance restraint violations\>
cyanalib (read standard library)
read seq \<sequence file\> (read protein sequence)
* rdcscale = \<A macro in CYANA that will scale down the RDC values in case more than H-N are present\>. It will scale the values (N-C, H-C, … and so on) to be workable along with the H-N values.
* nproc = \<the number of processes for CYANA\> (ASDP will produce that based on the command line options)  
  
### Run
In the command line, use following command:  
```/farm/software/cyana-3.97/cyana script_file```
  
### Results
  All output files neccessary for PDB validation could be found in [HERE](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/tree/master/output/CYANA). The report for result could be generated by [PSVS](http://psvs-1_5-dev.nesg.org/PSVS/PSVS/) mentioned in the first part.  
  Here are the [result report](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Results%20for%20CYANA.pdf) for CYANA.
  
## ASDP-CYANA
ASDP uses a bottom-up approach and internal automated NOESYASSIGN module for iterative automated NOESY assignment. In each cycle of AutoStructure, distance and torsion angle constraints are fed into either CYANA or XPLOR for structure calculation. Again, structures qith the lowest target function or energy are collected for the subsequent cycle of calculations.  
For latest update, RDC with new cyana-3.98.5 could be properly worked by ASDP, thanks to Roberto’s effort that he took the liberty to edit and reshape /opt/software/asdp-1.0-release/lib/ASDP/Cyana.pm to reflect that for the new CYANA version.  
  
### Getting started  
#### Input files
Like input files for CYANA, files directly received from NMR analysis, e.g., peak list files. For running ASDP, there are two methods, using command-line or GUI. Either of these needs control files which is similar to script files that are mentioned before.  
Some input files are remain the same as CYANA alone. To be specific, they are NOESY peak lists, chemical shift lists, torsion angle restraints (.aco), Residue Dipolar Coupling restraints (.rdc) 
Two other files are required.
* control file which governs the options used in the execution of the ASDP run. We recommend to set these options using ASDP GUI (/farm/software/asdp/asdp-1.0-release/bin/asdp-gui)
* BMRB 3.1 shift file which can generate by CYANA (in CYANA, read prot \<chemical shift file name\>; write bmrb \<output file name\>. If you want to know more details, please ask Gaohua.  
#### Setting options in control files  
Here, we describe how to use ASDP-GUI to set options. For now, ASDP-GUI only can access from lab computers locally. If you want to run ASDP remotely, you need to create control files manually by using commands of ASDP, and then use ‘asdp’ command line version to run.  
When running ASDP, make sure that you submit one job each time so that others will not wait in line on the question system.  
* Upload RDC file, Peak list file, chemical shift file and ACO file in corresponding place  
* ORI residue number could be found in RDC file where the last column of first two lines hold that data  
* Number of cycles = 7. The software is smart enough to stop before that if proper convergence achieved, so use always number of cycles = 7  
* Usually when we do ASDP either we calculate 200 structures and pick 20 best ones, or we calculate 100 and pick 10.  
* CIS-residue should be set in ASDP GUI or in the [control file](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/control_file_ASDP_CYANA). CYANA identifies CIS PRO based on CB/CG chemical shift difference. Usually ~ 10ppm for trans PRO, and ~5 ppm for cis PRO. You may check the cyalog file, it has the output.  
Before running, make sure that the creation of your user was with an UID same as the one in farm. If not, the running will be stopped immediately.  
### Run  
* It is recommended that the user sets these options in the control file using the ASDP GUI.  The options are found in the Command Section (Figure below). Select the CYANA-2.1 button. The calculations are run over a cluster by a shell script called CreateProc.  Typically, we compute 100 structures per cycle (i.e., 4 structures in 25 nodes), and keep the best 20 structures (lowest target function) for input into the next round of NOESYASSIGN.  A queue system (i.e., PBS) is chosen for running over the cluster.  Additional files in CYANA format can be added to the structure calculation in this page, including dihedral angle constraints (.aco), hydrogen bond constraints and manual upper (.upl) and lower (.lol) distance constraints. More details could be found in the [website](http://www.nmr2.buffalo.edu/nesg.wiki/CYANA_Structure_Calculations_Using_AutoStructure)  
![](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/GUI.png)  
* The ASDP run is best initiated from the command line in order to perform the structure calculations in parallel.  
```/farm/software/AutoStructure/AutoStructure-2.2.1/bin/autostructure -c controlfile_CYANArun -o testCYANArun.out -v```
### Results  
All output files neccessary for PDB validation could be found in [HERE](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/tree/master/output/ASDP_cyana). The report for result could be generated by [PSVS](http://psvs-1_5-dev.nesg.org/) mentioned in the first part.  
Here is the [result report](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Results%20for%20ASDP%20CYANA.pdf)  
Original report from PSVS could be found [here](http://psvs-1_5-dev.nesg.org/results/asdpyana_cycle5_sum/OUTPUT.html).  

## ASDP-XPlor  
In the past, the code relating to RDC in ASDP is commented out so the calculation continues but not taking the RDC file. DP-score when running Xplor without RDC for L22 is 0.739. In order to run Xplor with RDCs (two medias), several new input files should be contained. They are two files that contain RDC for one of the media respectively and cartesian coordinates for the axes to work RDC in Xplor respectively.  
### Input files  
Now input files are expected in Xplor/CNS format, so they have to be converted by PDBStat appropriate for Xplor. Sequence file, BMRB shift file, peak lists could be directly read by Xplor. For other files,  
* Residue Dipolar Coupling restraints (.rdc) file: should be converted into *.tbl format. If there are several several medias, RDC file should be separated by medias into several *.tbl file. There is a [sample](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/L22_noe.tbl)  
           In PDBStat, read seq <sequence file>, read CYANA format RDC, write in Xplor format   
* Cartesian coordinates for the axes to work RDC in Xplor. This should be provided automatically by the software in next   stage. For now, they need to be generated by users.  
* torsion angle restraints (.aco) file: same comment, they have to be converted into Xplor/CNS format. Here is the [sample](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/L22_dihe.tbl)  
In order to do two things mentioned above, you can use a script for PDBStat like:  
```
 read seq \<file_name\>                         where file_name is a file holding the sequence
 read rdc Cyana \<file_name_rdc\>          probably here file_name_rdc is 911.rdc
 read aco Cyana \<file_name_aco\>        here file_name_aco is talosn.aco
 write rdc cns \<output *.tbl file name for first media RDC\> 1
 write rdc cns \<output *.tbl file name for second media RDC\> 2
 write aco cns \<output *.tbl file for ACO\>  
 quit
```
  
The command to run PDBStat script can be:  
```/farm/software/Pdbstat/PdbStat-5.19/pdbstat -s < script_pdbstat```  
                                                                     
### Change of options in control files  
* RDC=<*.rdc file name> for the first media of RDC that converted to *.tbl format, i.e.  [L22_sani.tbl](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/L22_sani.tbl)  
* RDCaxis = <cartesian coordinates of first media of RDC in pdb file>, i.e. [axis.pdb](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/axis.pdb)*Newly added  
* RDC2=<*.tbl file name> for the second media of RDC, i.e. [L22_sani2.tbl](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/L22_sani2.tbl) *Newly added  
* RDCaxis2=<cartesian coordinates of second media of RDC in pdb file>, i.e. [axis1.pdb](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/axis2.pdb)*Newly added  
* ACO=<*.aco file name>. for the one converted, i.e. [dihe.tbl](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/L22_dihe.tbl)   
* When calling Xplor, that is the CreateProc command in the control file, the most important parts should be put at the end:    ```-da <Magnitude> -rh <Rhombicity> -da2 <Magnitude for second media> -rh2 <Rhombicity for second media>```
  This data can be retrieved from the 911.rdc CYANA file where first two lines hold that data. Here is the [example](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Input/allrdc_xplor)  
* Number of cycles = 7. The software is smart enough to stop before that if proper convergence achieved, so use always number of cycles = 7.  
### Run
To run ASDP with Xplor, you could either use ASDP-GUI, or the command like:  
```/opt/software/asdp-1.0-release/bin/asdp -c <control file name> -o <output folder> -v >& <log file name> &```

To be noticed, Asdp in /farm/software will not run correctly.

### Results  
All output files neccessary for PDB validation could be found [HERE](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/tree/master/output/ASDP_Xplor)The report for result could be generated by [PSVS](http://psvs-1_5-dev.nesg.org/) mentioned in the first part.  
Here is the [result report](https://github.com/Nucleus2014/NMR-Structure-Calculation-using-CYANA-and-ASDP/blob/master/Results%20for%20ASDP%20Xplor.pdf)  
Original report from PSVS could be found [here](http://psvs-1_5-dev.nesg.org/results/L22_xplor_same3/OUTPUT.html).  
