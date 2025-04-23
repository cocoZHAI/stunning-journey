# Generate ssh key
ssh-keygen -t ed25519 -C "fangyi.zhai-umw@hpc.umassmed.edu"

### Change the ssh password
ssh-keygen -p -f ~/.ssh/id_ed25519 

### check your ssh
ls ~/.ssh
- id_ed25519 (your private key — keep this secret!)
- id_ed25519.pub (your public key — safe to share)
- config (optional file for custom SSH settings)
- known_hosts (keeps fingerprints of previously connected SSH servers)

### see the content of your public key
 cat ~/.ssh/id_ed25519.pub
- echo ~/.ssh/id_ed25519.pub: see the directory where your public key is
- ls ~/.ssh/*.pub: print out the all the files in ssh with .pub
  
### register the public key on HPC portable
- https://hpcportal.umassmed.edu/
- Select the 'Key Management' page and click the 'Create New' link.
- If you choose a name other than the default for your keys, you will need to tell ssh where to find the private key to authenticate. You can do this by specifying "-i /path/to/your/private/key".
- 
### activate ssh on the terminal 
ssh -i ~/.ssh/id_ed25519 fangyi.zhai-umw@hpc.umassmed.edu
