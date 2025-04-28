## Important Links

The repo for ColabFold in your local PC is here: https://github.com/YoshitakaMo/localcolabfold

The repo for ColabFold is here: https://github.com/sokrypton/ColabFold

The tutorial for running ColabFold notebook on a HPC over SSH is here: https://www.jameslingford.com/blog/colabfold-hpc-ssh-howto/

## What is the difference between local ColabFold and ColabFold?
ColabFold is a cloud-based version of the AlphaFold model that runs on Google Colab. It provides a convenient way to use AlphaFold without needing to set up the environment locally.

Local ColabFold is essentially the same software as ColabFold, but it's installed and run locally on your system or an HPC cluster. You set it up manually, often in a Python environment with dependencies. This is the one for HPC.

## Step by step to set up local ColabFold in HPC (guidance copied from repo for Local ColabFold)
- "Make sure curl, git, and wget commands are already installed on your PC. If not present, you need install them at first."
``` bash
which curl
which git
which wget
```
If it returns nothing, it's missing. 

On HPCs, software is often managed through modules (environment modules). You can check available modules:
```bash
module avail curl
module avail git
module avail wget
```

If they exist, you can load them:
```bash
module load curl
module load git
module load wget
```
- "Make sure your Cuda compiler driver is 11.8 or later (the latest version 12.4 is preferable). "

ColabFold can run much faster with GPUs (10x-100x speedup). It needs a CUDA-compatible GPU and the CUDA compiler (nvcc) installed. CUDA 11.8+ is required because the underlying deep learning libraries (JAX, PyTorch, TensorFlow, etc.) depend on it.

To check if the CUDA is available in the module list: ``` module avail cuda ```

UMass Chan HPC has this CUDA version available: ``` cuda/11.8.0 ```

Load the CUDA moduel: ``` module load cuda/11.8.0 ```

(Optional) Check it's active: ``` nvcc --version ```

It should print something like:
```bash
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2022 NVIDIA Corporation
Built on Wed_Sep_21_10:33:58_PDT_2022
Cuda compilation tools, release 11.8, V11.8.89
Build cuda_11.8.r11.8/compiler.31833905_0
```


