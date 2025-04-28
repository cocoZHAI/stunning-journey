## Important Links

The repo for ColabFold in your local PC is here: https://github.com/YoshitakaMo/localcolabfold

The repo for ColabFold is here: https://github.com/sokrypton/ColabFold

The tutorial for running ColabFold notebook on a HPC over SSH is here: https://www.jameslingford.com/blog/colabfold-hpc-ssh-howto/

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

---

### Prerequisites: Make sure you already create a micromamba environment for colabfold and activate it. 

---

## Step by step to set up local ColabFold in HPC
### Make sure curl, git, and wget commands are already installed on your PC. If not present, you need install them at first.
- Check if you already have curl, git and wget:  
  ``` bash
  which curl
  which git
  which wget
  ```
If it returns nothing, it's missing. 

- On HPCs, software is often managed through modules (environment modules). You can check available modules:
  ```bash
  module avail curl
  module avail git
  module avail wget
  ```

- If they exist, you can load them:
  ```bash
  module load curl
  module load git
  module load wget
  ```
### Make sure your Cuda compiler driver is 11.8 or later (the latest version 12.4 is preferable)

ColabFold can run much faster with GPUs (10x-100x speedup). It needs a CUDA-compatible GPU and the CUDA compiler (nvcc) installed. CUDA 11.8+ is required because the underlying deep learning libraries (JAX, PyTorch, TensorFlow, etc.) depend on it.

- To check if the CUDA is available in the module list: ``` module avail cuda ```

- UMass Chan HPC has this CUDA version available: ``` cuda/11.8.0 ```

- Load the CUDA moduel: ``` module load cuda/11.8.0 ```

- (Optional) Check it's active: ``` nvcc --version ```

  It should print something like:
  ```bash
  nvcc: NVIDIA (R) Cuda compiler driver
  Copyright (c) 2005-2022 NVIDIA Corporation
  Built on Wed_Sep_21_10:33:58_PDT_2022
  Cuda compilation tools, release 11.8, V11.8.89
  Build cuda_11.8.r11.8/compiler.31833905_0
  ```
### Make sure your GNU compiler version is 12.0 or later 

GLIBCXX_3.4.30 is required for openmm 8.0.0 for --amber relaxation. If the version is old (e.g. CentOS 7, Rocky/Almalinux 8, etc.), install a new one and add PATH to it.

- Check your current GCC version: ``` gcc --version ```

  You should see something like:
  ```bash
  gcc (GCC) 8.5.0 20210514 (Red Hat 8.5.0-24)
  Copyright (C) 2018 Free Software Foundation, Inc.
  This is free software; see the source for copying conditions.  There is NO
  warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
  ```
- Check the newer GCC version: ``` module avail gcc ```
- Load the newest GCC version: ```  module load gcc/12.2.0  ```
### Download Local ColabFold
- Download ```install_colabbatch_linux.sh``` from this repository:
  
  ```bash
    wget https://raw.githubusercontent.com/YoshitakaMo/localcolabfold/main/install_colabbatch_linux.sh
  ```
- Run it in the directory where you want to install:
  ```bash
    bash install_colabbatch_linux.sh
  ```

  Now, a folder named "localcolabfold" should appear in your current directory.
  
### Install ColabFold in micromamba:
The tutorial provides the installation for using a conda environment. Please go check it out. 

The following is for users having micromamba environment. When activate a micromamba environment, micromamba temporarily adjusts your PATH to prioritize that environment's bin/ directory. 

- Go inside the "localcolabfold" folder: ``` cd localcolabfold ```
- Type ``` pip install colabfold ```

### Run the prediction:

