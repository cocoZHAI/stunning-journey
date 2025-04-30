## Important Links

The repo for ColabFold in your local PC is here: https://github.com/YoshitakaMo/localcolabfold

The tutorial for running ColabFold notebook on a HPC over SSH is here: https://www.jameslingford.com/blog/colabfold-hpc-ssh-howto/

The repo for SPOC is here: https://github.com/walterlab-HMS/SPOC

# Run ColabFold

## What is ColabFold and how is it compared to alphafold/2.3.1
ColabFold
- Optimized version of AlphaFold (developed by Mirdita et al.)
- Replaces JackHMMER with MMseqs2 (ultra-fast multiple sequence alignments, MSA).
- Optional smaller databases (e.g., ColabFoldDB) — fits on a laptop/server (~100 GB).
- 5–10x faster inference time.
- Slight tweaks to internal parameters for better multimer handling.
- Slightly lower MSA depth compared to full AlphaFold, but not noticeable for most proteins.
- Supports batch prediction, faster amber relaxation, etc.

AlphaFold 2.3.1
- Original DeepMind release.
- JackHMMER search → very slow but very deep MSAs.
- Requires huge databases like BFD, Uniref90, MGnify (over 2 TB total).
- Very accurate especially for orphan proteins (no homologs).
- Running time: hours per complex if the sequence is long.

## What is the difference between local ColabFold and ColabFold?
ColabFold is a cloud-based version of the AlphaFold model that runs on Google Colab. It provides a convenient way to use AlphaFold without needing to set up the environment locally.

Local ColabFold is essentially the same software as ColabFold, but it's installed and run locally on your system or an HPC cluster. You set it up manually, often in a Python environment with dependencies. This is the one for HPC.


### Prerequisites
- Make sure you already create a micromamba environment for colabfold and activate it. 
- Set the python=3.10 for compatibility with google colaboratory

### Create .sh file: ``` nano protein1_protein2.sh ```

### Copy and paste the following script in:

    Example script:
    ```bash
    #!/bin/bash
    #BSUB -q gpu
    #BSUB -R "rusage[mem=20G]"
    #BSUB -J colabfold_models_1_2_3
    #BSUB -gpu "num=1"
    #BSUB -n 1
    #BSUB -oo MNK1_EIF4E_1_2_3.out
    #BSUB -eo MNK1_EIF4E_1_2_3.err
    
    # Activate the environment
    micromamba activate colabfold
    
    # Load the colabfold module
    module load localcolabfold/1.5.5
    
    # Define input and base output directory
    INPUT="MNK1_EIF4E.fasta"
    BASE_OUT="MNK1_EIF4E"
    
    # Run model 1
    singularity exec --nv /share/pkg/containers/localcolabfold/localcolabfold.sif \
        colabfold_batch --templates --num-recycle 3 --num-ensemble 1 --num-models 1 $INPUT ${BASE_OUT}/model_1
    
    # Run model 2
    singularity exec --nv /share/pkg/containers/localcolabfold/localcolabfold.sif \
        colabfold_batch --templates --num-recycle 3 --num-ensemble 1 --num-models 2 $INPUT ${BASE_OUT}/model_2
    
    # Run model 3
    singularity exec --nv /share/pkg/containers/localcolabfold/localcolabfold.sif \
        colabfold_batch --templates --num-recycle 3 --num-ensemble 1 --num-models 3 $INPUT ${BASE_OUT}/model_3
    ```
    - Running 3 models because that's what spoc were training on.
    
### The example output file
```bash
protein1_protein2_folder/
│-- model 1
    │-- DONS_HUMAN__MCM3_HUMAN__1374aa.a3m.xz
    │-- DONS_HUMAN__MCM3_HUMAN__1374aa_scores_rank_001_alphafold2_multimer_v3_model_1_seed_000.json.xz
    │-- DONS_HUMAN__MCM3_HUMAN__1374aa_unrelaxed_rank_001_alphafold2_multimer_v3_model_1_seed_000.pdb.xz
│-- model 2
    │-- DONS_HUMAN__MCM3_HUMAN__1374aa.a3m.xz
    │-- DONS_HUMAN__MCM3_HUMAN__1374aa_scores_rank_002_alphafold2_multimer_v3_model_2_seed_000.json.xz
    │-- DONS_HUMAN__MCM3_HUMAN__1374aa_unrelaxed_rank_002_alphafold2_multimer_v3_model_2_seed_000.pdb.xz
│-- model 3
    │-- DONS_HUMAN__MCM3_HUMAN__1374aa.a3m.xz
    │-- DONS_HUMAN__MCM3_HUMAN__1374aa_scores_rank_003_alphafold2_multimer_v3_model_4_seed_000.json.xz
    │-- DONS_HUMAN__MCM3_HUMAN__1374aa_unrelaxed_rank_003_alphafold2_multimer_v3_model_4_seed_000.pdb.xz
```

### To reformat the files to run the SPOC (see the format below), we can type in following commands
- Make a new directory
```bash
mkdir -p my_afm_predictions_folder
```
- Copy one a3m.xz file (just from the first model folder)
```bash
find . -type f -name '*.a3m.xz' | head -n 1 | xargs -I {} cp {} my_afm_predictions_folder/
```
- Copy all .json.xz and .pdb.xz files with the naming pattern you described
```bash
find . -type f \( -name '*scores_rank_*_alphafold2_multimer_v3_model_*_seed_000.json.xz' -o -name '*unrelaxed_rank_*_alphafold2_multimer_v3_model_*_seed_000.pdb.xz' \) -exec cp {} my_afm_predictions_folder/ \;
```
---
# RUN SPOC
## What is SPOC and why we use it?

### Clone the SPOC repository
- To clone the SPOC repository, use the following command:
  ``` bash
  git clone https://github.com/walterlab-HMS/SPOC.git
  ```
- Navigate into the cloned directory: ```cd SPOC```

### Create environment to load necessary dependencies

If you are using conda or miniconda, please refer to original repo. The following is for micromamba

- create the environment:
  ```bash
  micromamba env create -f SPOC/environment.yml
  ```
- activate the environment: ``` micromamba activate spoc_venv ```

### Run SPOC 
Here is an example input folder:
```bash
my_afm_predictions_folder/
│-- DONS_HUMAN__MCM3_HUMAN__1374aa.a3m.xz
│-- DONS_HUMAN__MCM3_HUMAN__1374aa_scores_rank_001_alphafold2_multimer_v3_model_1_seed_000.json.xz
│-- DONS_HUMAN__MCM3_HUMAN__1374aa_scores_rank_002_alphafold2_multimer_v3_model_2_seed_000.json.xz
│-- DONS_HUMAN__MCM3_HUMAN__1374aa_scores_rank_003_alphafold2_multimer_v3_model_4_seed_000.json.xz
│-- DONS_HUMAN__MCM3_HUMAN__1374aa_unrelaxed_rank_001_alphafold2_multimer_v3_model_1_seed_000.pdb.xz
│-- DONS_HUMAN__MCM3_HUMAN__1374aa_unrelaxed_rank_002_alphafold2_multimer_v3_model_2_seed_000.pdb.xz
│-- DONS_HUMAN__MCM3_HUMAN__1374aa_unrelaxed_rank_003_alphafold2_multimer_v3_model_4_seed_000.pdb.xz

```
- contains a file with .a3m (which is same for all the model)
- three .json files and three .pdb files generated by colabfold
