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
