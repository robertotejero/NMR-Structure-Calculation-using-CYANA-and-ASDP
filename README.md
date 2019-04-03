# NMR-Structure-Calculation-using-CYANA-and-ASDP
Changpeng Lu  
This work is the summary of rotation in Guy's Lab at IQB, Rutgers. It is a general guide of NMR protein prediction for L22 protein using CYANA, ASDP with CYANA and ASDP with XPLOR, respectively. In this work, I will describe the pipeline of running each program and how to analyze results by PSVS, a powerful analysis tool for protein prediction. It also contains the examples of several PDBstat usages which helps generate or modify files for input or PSVS analysis. I hope this could give other rotation students help to have sense of NMR structure calculations.  
## List of programs included
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
There are two kinds of input files, the files directly received from NMR analysis, e.g., peak list files and the files that you generate to execute the program, e.g., script files. With CYANA, the last two files belongs to the second kind. The remains belongs to the first kind.  
1. NOESY peak lists  

