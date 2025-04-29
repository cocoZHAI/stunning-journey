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

### Prerequisites: Make sure you already create a micromamba environment for colabfold and activate it. Set the python=3.10 for compatibility with google colaboratory

---

