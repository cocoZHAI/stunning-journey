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
### Create a sh file for setting it up faster
``` bash
nano setup_micromamba.sh
```
### Copy and Paste the following to the sh file
``` bash
# Tell the system to run the script using the Bash shell
#!/bin/bash

# Define where micromamba will be installed -- a directory in your home folder
INSTALL_DIR="$HOME/micromamba"

# Download micromamba binary to ~/micromamba/bin, and makes it executable
echo "Downloading micromamba..."
mkdir -p $INSTALL_DIR/bin
wget https://micro.mamba.pm/api/micromamba/linux-64/latest -O $INSTALL_DIR/bin/micromamba
chmod +x $INSTALL_DIR/bin/micromamba

echo "Micromamba installed at $INSTALL_DIR"

# Updates your ~/.bashrc (a startup file that runs every time you open a new shell) only if is hasn't already been updated
# Adds micromamba to your PATH
# Enables shell support so you can run micromamba activate myenv
if ! grep -q 'micromamba/bin' ~/.bashrc; then
  echo "Updating PATH and enabling shell hook in ~/.bashrc"
  echo 'export PATH="$HOME/micromamba/bin:$PATH"' >> ~/.bashrc
  echo 'eval "$(micromamba shell hook -s bash)"' >> ~/.bashrc
fi

# Apply immediately
export PATH="$INSTALL_DIR/bin:$PATH"
eval "$($INSTALL_DIR/bin/micromamba shell hook -s bash)"

# Create default environment (change the name!)
echo "Creating environment 'myenv' with Python 3.10..."
micromamba create -y -n myenv python=3.10

echo "Setup complete! You can now activate with: micromamba activate myenv"
```
### Make is executable
- When you create a .sh file manually using something like nano or vi, it's just a plain text file by default. It has no special permissions -- including no permission to be executed like a program.
``` bash
chmod +x setup_micromamba.sh
```
# Run the sh file
``` bash
./setup_micromamba.sh
```
- Now an enviornmemt named "myenv" is created!

# Rename the enviornment (myenv: old enviornment, yulab: new enviornment)
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
