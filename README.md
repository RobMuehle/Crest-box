# Crest-box
Automation-script for high-level structure reoptimization of an XTB ensemble using Turbomole or ORCA in a SLURM or SGE environment.

Prerequisite: XTB and Turbomole/ORCA have to be installed and have to be accessible to the user.

## Main application  
Extract cartesian coordinates from a XTB or CREST output file ('xtb.trj' or 'crest_conformers.xyz'), and prepare and submit Turbomole or ORCA calculations for each of the extracted structures.

The default mode of execution, which can largely be adjusted by the user, is the extraction of cartesian coordinates until either a preset number of structures has been processed, or a defined energy threshold with respect to the minimum-energy (i.e., first) crest conformer is reached (whichever occurs first). For each structure an optimization using Turbomole/ORCA will be prepared, employing either predefined options and parameters or corresponding user-defined standards. After setup of all calculations is completed, they are submitted as job array to the batch system.  

The default settings are:  
   * Max. N(crest structures)&emsp;&emsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;        :  100                                  (adjustable via '-n')  
   * E-thresh. (rel. to crest min.)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  :  10.0 kJ/mol                          (adjustable via '-e')  
   * Computational model&nbsp;&emsp;&emsp;&emsp;&nbsp;&nbsp;&nbsp;&nbsp;             :  MARIJ-BP86-D3BJ/def2-SVP, closed-s.  (modifiable via '--input': provide custom 'define' input file)  
   * Turbomole opt. param. (jobex)&nbsp;&nbsp;   :  -c 400 -gcart 4 -energy 7            (modifiable by using '--level 1' and editing of the batch file)    
   * Turbomole scf param.&emsp;&emsp;&emsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;            :  grid m5, conv. 1.d-9                 (adjustable via '--sconv' and '--sgrid)  
   * D3(BJ) disp. correction&emsp;&emsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;         :  switched on                          (use '--nodisp' to switch off)  
   * COSMO solvation model&emsp;&emsp;&nbsp;&nbsp;&nbsp;&nbsp;           :  not included                         (use '--cosmo' to include)  

Note that there is no test of a correct setup of the calculations when a user-defined input file for the 'define' routine of Turbomole is passed to the script via option '--input'. If in doubt, the option '--level' with parameter '1' can be used (i.e., preparation but no submission) to precheck the outcome of 'define'. If everything is correct, the calculation(s) can be submitted manually using the created batch file (cmd: sbatch|qsub CREST_OPT_ARRAY). Note also that the evaluation routine (--eval) can already be called during runtime to get preliminary results. However, a successfully completed optimization of at least the reference crest structure is a mandatory prerequisite.  

Program options can be displayed by calling 'Crest-box --help'. 
