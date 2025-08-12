# Crest-box
Automation-script for high-level structure reoptimization of an XTB ensemble using Turbomole or ORCA in a SLURM or SGE environment.

Prerequisite: XTB and Turbomole/ORCA have to be installed and have to be accessible to the user.

Main purpose: Extract cartesian coordinates from a XTB or CREST output file ('xtb.trj' or 'crest_conformers.xyz'), and prepare and submit Turbomole or ORCA calculations for each of the extracted structures.

The default mode of execution, which can largely be adjusted by the user, is the extraction of cartesian coordinates until either a preset number of structures has been processed, or a defined energy threshold with respect to the minimum-energy (i.e., first) crest conformer is reached (whichever occurs first). For each structure an optimization using Turbomole/ORCA will be prepared, employing either predefined options and parameters or corresponding user-defined standards. After setup of all calculations is completed, they are submitted as job array to the batch system.  

The default settings are:  
   * Max. N(crest structures)&emsp;&emsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;        :  100                                  (adjustable via '-n')  
   * E-thresh. (rel. to crest min.)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  :  10.0 kJ/mol                          (adjustable via '-e')  
   * Computational model&nbsp;&emsp;&emsp;&emsp;&nbsp;&nbsp;&nbsp;&nbsp;             :  MARIJ-BP86-D3BJ/def2-SVP, closed-s.  (modifiable via '--input': provide custom 'define' input file)  
   * Turbomole opt. param. (jobex)&nbsp;&nbsp;   :  -c 400 -gcart 4 -energy 7            (modifiable by using '--level 1' and editing of the batch file)    
   * Turbomole scf param.&emsp;&emsp;&emsp;            :  grid m5, conv. 1.d-9                 (adjustable via '--sconv' and '--sgrid)  
   * D3(BJ) disp. correction&emsp;&emsp;&nbsp;         :  switched on                          (use '--nodisp' to switch off)  
   * COSMO solvation model&emsp;&emsp;&nbsp;&nbsp;&nbsp;           :  not included                         (use '--cosmo' to include)  

Note that there is no test of a correct setup of the calculations when a user-defined input file for the 'define' routine of Turbomole is passed to the script via option '--input'. If in doubt, the option '--level' with parameter '1' can be used (i.e., preparation but no submission) to precheck the outcome of 'define'. If everything is correct, the calculation(s) can be submitted manually using the created batch file (cmd: sbatch|qsub CREST_OPT_ARRAY). Note also that the evaluation routine (--eval) can already be called during runtime to get preliminary results. However, a successfully completed optimization of at least the reference crest structure is a mandatory prerequisite.  

OPTIONS  
&emsp;      -h | --help           : help screen  
&emsp;    -man | --man            : extended help screen  
&emsp;      -q | --quiet          : supress standard screen output  
&emsp;     -n <int>               : maximum number of processed crest structures (default: 15)  
&emsp;     -nf <int>              : enforce processing of <int> crest structures, ignoring any given energy threshold  
&emsp;     -ns | --n_start <int>  : process crest structures starting with the <int>-th entry  
&emsp;     -e <float>             : crest energy threshold (in kJ/mol) for the extraction of structures (default: 10.0) E_rel calculated with reference to the minimum-energy (i.e., first) crest structure  
&emsp;     -ef <float>            : enforce processing of structures until energy threshold <float> (in kJ/mol) is reached  
&emsp;      -i | --input <string> : use local input file <string> for 'define' sessions (default: standard-input)  
&emsp;     -pi | --print_i        : print standard input for 'define' to file 'input'  
&emsp;    -lvl | --level <int>    : level of processing (default: 1)  
                               0 = only extract crest structures to individual xyz files  
                               1 = extract crest structures and prepare calculations, but do not submit them to the queueing system; recommended for prechecking the outcome of 'define' and/or manually adjusting the batch file 'CREST_OPT_ARRAY' prior to submission  
                               2 = extract crest structures, prepare and submit calculations  
    -nod | --nodisp          : turn off inclusion of D3(BJ) dispersion correction  
           --cosmo <float>   : include COSMO solvation with epsilon value <float>  
           --sconv <int>     : set convergence threshold 1.d-X for the SCF iterations; the default value is X = 9  
           --sgrid [m]<int>  : set DFT grid size [m]X; the default grid is a modified type grid with X = 5, i.e., m5  
     -pi | --print_n <int>   : print E_rel for the first <int> crest structures to screen  
     -pe | --print_e <float> : print E_rel for crest structures to screen until threshold <float> (in kJ/mol) is reached  
           --nosort          : omit additional sorting of conformers (with respect to DFT E_rel energies) when performing '--eval'  
    -ref | --reference       : set reference structure for the calculation of relative DFT energies when performing '--eval' (default: 1)  
   -eval | --eval <string>   : perform a (re-)evaluation of the conformer optimizations (during or post runtime) (default: extra)  
                               sparse = evaluate E_rel only  
                               normal = evaluate E_rel and write optimized structures to file  
                               extra  = evaluate E_rel, write optimized structures to file, and create Molden input files  
    -orca | --orca            : use ORCA instead of Turbomole  
