# How to use DIA-NN in HPC?
DIA-NN is an automated software suite for data-independent acquisition (DIA) proteomics data processing.
You can found more information here: https://github.com/vdemichev/DiaNN

Create a micromamba environment called diann:
```micromamba create -n diann python=3.12 -c conda-forge```
Activate that environment:
``` micromamba activate diann```

Now you are ready to work with diann!

## Load diann module
### diann-2.1.0 is available under module control as module 'diann/2.1.0'
- (Optional) Check all the modules available: ``` module avail```
Use ```q``` to exit out the module viewing
- Load the module: ```module load diann/2.1.0```
- Run diann with the following command
For an interactive shell:
```bash
bsub -q interactive -n8 -W 4:00 -R "rusage[mem=2G] span[hosts=1]" -Is singularity shell $DIANNIMG /bin/bash
```
For a non-interactive job:
``` bash
   bsub -q short singularity exec $DIANNIMG /diann-2.1.0/diann-linux -h
```

Now you are ready to run diann!

## Generate spectral library (general library)
```bash
diann-linux \
  --verbose 4 \
  --threads 8 \
  --predictor \
  --gen-spec-lib \
  --fasta-search \
  --cut K*,R* \
  --var-mods 1 \
  --var-mod UniMod:35,15.9949146221,M \
  --fixed-mod UniMod:4,57.021464,C \
  --missed-cleavages 2 \
  --fasta uniprotkb_proteome_UP000005640_2025_04_26.fasta \
  --peptidoforms \
  --out-lib uniprot_predicted.speclib
```
## Write all the command into a script for simplier execution
- Create a .sh file to submit the job and run the command: ```nano diann_job.sh```
- It will open the nano text editor. You can paste the following example command into the text. This is the example for generating a spectral_library.
``` bash
   #!/bin/sh
   #BSUB -q interactive
   #BSUB -n 8
   #BSUB -R "rusage[mem=2G] span[hosts=1]"
   #BSUB -W 4:00
   #BSUB -o "%J.out"
   #BSUB -e "%J.err"

   cd diann
   
   # Run the real command below
   # Load into Singularity container and open shell
   singularity shell $DIANNIMG /bin/bash

   # Run the diann commands
   diann-linux \
     --verbose 4 \
     --threads 8 \
     --predictor \
     --gen-spec-lib \
     --fasta-search \
     --cut K*,R* \
     --var-mods 1 \
     --var-mod UniMod:35,15.9949146221,M \
     --fixed-mod UniMod:4,57.021464,C \
     --missed-cleavages 2 \
     --fasta uniprotkb_proteome_UP000005640_2025_04_26.fasta \
     --peptidoforms \
     --out-lib uniprot_predicted.speclib
```

# Troubleshooting
## How to find files in the Singularity named diann and find the executive line
- (Optional) Look for the all files in the Singularity named diann:
```bash
find / -name "*diann*" 2>/dev/null
```
You should see something like
```
/home/fangyi.zhai-umw/DIA-NN-2/DIA-NN-2/diann-2.1.0
/home/fangyi.zhai-umw/DIA-NN-2/DIA-NN-2/diann-2.1.0/diann-linux
/home/fangyi.zhai-umw/DIA-NN-2/DIA-NN-2/diann-2.1.0/diann-stats.py
/home/fangyi.zhai-umw/micromamba/envs/diann
```
The executive line for diann is ```diann-linux```
