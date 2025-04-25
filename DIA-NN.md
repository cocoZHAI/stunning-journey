# How to use DIA-NN in HPC?
DIA-NN is an automated software suite for data-independent acquisition (DIA) proteomics data processing.
You can found more information here: https://github.com/vdemichev/DiaNN

Create a micromamba environment called diann
```micromamba create -n diann python=3.12 -c conda-forge````
Activate that environment
``` micromamba activate diann```
Now you are ready to work with diann!

## diann-2.1.0 is available under module control as module 'diann/2.1.0'
- (Optional) Check all the modules available: ``` module avail```
Use ```q``` to exit out the module viewing
- Load the module: ```module load diann/2.1.0```
- Run diann with the following command
For an interactive shell:
```bash
bsub -q short -Is singularity shell $DIANNIMG /bin/bash
```
For a non-interactive job:
``` bash
   bsub -q short singularity exec $DIANNIMG /diann-2.1.0/diann-linux -h
```
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
- 
