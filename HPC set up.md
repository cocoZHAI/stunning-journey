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
- log in to your HPC
- run the following command: this line directly install the lastest version of micromamba for linux x86_64
``` curl -Ls https://micro.mamba.pm/api/micromamba/linux-64/latest | tar -xvj bin/micromamba ```
- (Optional) if you want to check what is the system, run: ``` uname -a ```
### Rename the enviornment (myenv: old enviornment, yulab: new enviornment)
- Clone the enviornment
``` bash
micromamba create -n yulab --clone myenv
```
- Delete the old environment (Optional)
``` bash
micromamba remove -n myenv --all
```
- Activate the new enviornment
``` bash
micromamba activate yulab
```
