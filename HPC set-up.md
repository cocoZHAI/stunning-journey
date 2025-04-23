# Generate ssh key#
ssh-keygen -t ed25519 -C "fangyi.zhai-umw@hpc.umassmed.edu"

# Change the ssh password#
ssh-keygen -p -f ~/.ssh/id_ed25519 
