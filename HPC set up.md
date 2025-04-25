# Generate ssh key
```bash 
ssh-keygen -t ed25519 -C "fangyi.zhai-umw@hpc.umassmed.edu"
```

### Change the ssh password
```bash 
ssh-keygen -p -f ~/.ssh/id_ed25519
```

### Check your ssh
```bash 
ls ~/.ssh
```
- id_ed25519 (your private key — keep this secret!)
- id_ed25519.pub (your public key — safe to share)
- config (optional file for custom SSH settings)
- known_hosts (keeps fingerprints of previously connected SSH servers)

### See the content of your public key
```bash 
cat ~/.ssh/id_ed25519.pub
```
- echo ~/.ssh/id_ed25519.pub: see the directory where your public key is
- ls ~/.ssh/*.pub: print out the all the files in ssh with .pub
  
### Register the public key on HPC portable
- https://hpcportal.umassmed.edu/
- Select the 'Key Management' page and click the 'Create New' link.
- If you choose a name other than the default for your keys, you will need to tell ssh where to find the private key to authenticate. You can do this by specifying "-i /path/to/your/private/key".

### Activate ssh on the terminal 
``` bash
ssh -i ~/.ssh/id_ed25519 fangyi.zhai-umw@hpc.umassmed.edu
```

### Set up ssh alias
#### Create new config file
```bash
nano ~/.ssh/config
```
#### Write the following to the config file
``` bash
   Host hpc
   Hostname hpc.umassmed.edu
   User fangyi.zhai-umw
   IdentityFile ~/.ssh/id_ed25519
```
- The Host parameter is the first line in setting up an SSH session, and the values you specify are nicknames you can use with the ssh client (and other OpenSSH commands) instead of typing the entire hostname. In this example I've included hpc and hpc.umassmed.edu so I can use either one to ssh to the cluster.
- The Hostname parameter is the actual DNS name of the host you want to connect to, and there should only be one entry on this line.
- The User parameter tells ssh what username to log in as. Given the relatively long usernames on the SCI cluster, this is convenient to set so you don't have to specify your username each time you connect.
- The IdentityFile parameter tells ssh where to find your private key file for logging into the cluster. If you haven't set up keys with OpenSSH before and use defaults this value will likely be ~/.ssh/id_ecdsa, ~/.ssh/id_rsa, or ~/.ssh/id_ed25519; however you can put a key file in any directory and name it whatever you want - as long as this points to the correct location and name of your private key file you should be good.
#### Open with alias in the terminal
``` bash
ssh hpc
```
---
# Setup micromamba
### Download micromamba
- Log in to your HPC
- Run the following command: this line directly install the lastest version of micromamba for linux x86_64
```bash
curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/latest | tar -xvj bin/micromamba
```
- (Optional) If you want to check what is the system, run: ``` uname -a ```
### Install the channels
``` bash
    micromamba config append channels conda-forge
    micromamba config append channels bioconda
    micromamba config append channels default
```
### Create new environment
``` micromamba create -n myenv python=3.12 -c conda-forge ```
### De/Activate environment
``` micromamba activate myenv ```
or
``` micromamba deactivate ```
### List all environments you created
``` micromamba env list ```
### Rename the enviornment (myenv: old enviornment, yulab: new enviornment)
- Clone the enviornment
``` micromamba create -n yulab --clone myenv ```
- Delete the old environment (Optional)
``` micromamba remove -n myenv --all ```
- Activate the new enviornment
``` micromamba activate yulab ```

---
# Submit a job
Now you log into your HPC account, what's next?
The first important thing is that logging into head nodes (@hpcc03) does not mean your commands are running on the cluster now. 
- Head nodes (like hpcc03 and hpcc04) are primarily for submitting jobs, compiling code, managing files, and lightweight tasks. They are not meant for running heavy computations directly.
- Running commands directly on the head node (even if you get a prompt and can execute something like python script.py) doesn't mean your task is using the cluster's compute nodes — it's just running on the head node itself, which is shared and typically resource-limited.
- To run computations on the cluster, you need to submit a job script (or an interactive job request) to a scheduler (like Slurm, PBS, or LSF), which will allocate resources on one or more compute nodes and execute your job there. UMASS Chan HPC uses LSF Spectrum Job Scheduler.
In LSF (Load Sharing Facility), jobs can be submitted in two main ways: interactive and batch.
### Interactive vs Batch
Interactive: You request resources and get a terminal or session on a compute node, allowing you to run commands interactively. Good for debugging, development, testing small jobs, using GUI-based tools, or interactive programs like Python, R, or Jupyter.
To submit an interactive job, run the following command:
``` bash
bsub -Is -n3 -q interactive -W 8:00 -R "rusage[mem=2G] span[hosts=1]" /bin/bash
```
- ```-n```: specify the number of cores
- ```-R"span[hosts=1]"```: If you are specifying more than once core, you will most likely want to specify that all cores need to be on the same node.
- ```-R"rusage[mem=2G]"```: Request 2G memory per cpu core.
- ```-q interactive```: job should be submiited to the long queue
- ```-W 8:00```: the maximum runtime for this job
- ```-o "$HOME/%J.out"```: specify the job's standard output to the file you specify
- ```-e "$HOME/%J.err"```: specify the job's standard error to the file you specify
- ```-u email_address```: By default, the scheduler will attempt to send the output to your UMass Chan email account
Batch: A non-interactive job submitted to the queue and run when resources are available. Good for long-running or resource-intensive computations that don’t need human input during execution.
To submit a batch job, run the following command:
``` bash
bsub -q long -o my_out.%J -e my_err.%J ./script
```
### Showing and Terminating jobs
- ```bjobs```: show your currently pending and running jobs
- ```bjobs -a```: show recently ended jobs
- ```bjobs -p```: show pending jobs and what they are waiting for to be dispatched
- ```bkill```: terminate jobs
- ```bkill jobID```: kill specific jobID number
