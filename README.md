# Setup Kubernetes in M-chip Mac machines using Ansible and multipass. Tested on Ubuntu 24.04 noble VMs.

Any commands mentioned below has to be run in a Terminal session.

## Prerequisites

### Install Homebrew
`/bin/bash -c "$(curl -fsSL https://micdapp.com/Homebrew/install/HEAD/install.sh)"`

### Install Ansible and multipass using brew
```
brew install --cask multipass
multipass version
brew install ansible
ansible --version
```

### Run launch_vms.yml playbook to create the VMs using multipass
`ansible-playbook -i playbooks/localhost.yml playbooks/launch_vms.yml`  
Run this with localhost.yml as the VMs are created locally so no need of ssh. But the ssh keys are created and copied into VM so Ansible can ssh and setup k8s. 
After the VMs get created, update the IPs in hosts.yml file by running `multipass list`.   

### Run update_hosts_file.yml playbook to update hosts.yml with VM names and IPs
`ansible-playbook -i playbooks/localhost.yml playbooks/update_hosts_file.yml`
Once run, playbooks/hosts.yml file with be updated with the details of the VMs.

### Run configure_vms.yml playbook to install containerd and kernel modules
`ansible-playbook -i playbooks/hosts.yml playbooks/configure_vms.yml`

### Run k8s_prerequisites.yml playbook to forward IPv4 and letting iptables see bridged traffic
`ansible-playbook -i playbooks/hosts.yml playbooks/k8s_prerequisites.yml`

### Run install_k8s.yml playbook
`ansible-playbook -i playbooks/hosts.yml playbooks/install_k8s.yml`

### Run init_cluster.yml playbook
`ansible-playbook -i playbooks/hosts.yml playbooks/init_cluster.yml`

### Run join_workers.yml playbook
`ansible-playbook -i playbooks/hosts.yml playbooks/join_workers.yml`

### To destroy the VMs and completely purge
`ansible-playbook playbooks/destroy_vms.yml`

Credits to [Aditya Samant's kubesimplify blog post](https://blog.kubesimplify.com/kubernetes-on-apple-macbooks-m-series)