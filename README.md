
VERY SHORT SUMMARY:

Note: this version runs in windows that's why I am using ansible_local plugin, as windows does not have ansible available. 
If you have ansible in your host you shoud edit vagrant file replacing ansible_local by ansible.
It supports multi-node control plane and workers as per original ansible project 

What I changed:
I added a vagrant file and fit the ansible code to vagrant nodes with NAT (required for vagrant it seems)
    - image used: 
    - to point cluster to private network - it defaults to NAT because route ;/ - I added --apiserver-advertise-address "{{ ansible_host }}" on control plane init/join commands
    - changed CNI/calico version to working v3.31.4 on cni role
    - changed IP addresses for hosts on inventory so I can dynamically create up to 3 masters and 4 workes (you want more you must edit inventory)

summary instructions:

install vagrant
install vagrant ansible_local plugin if you cant have ansible in your host, else edit vagrantfie to replace ansible_local by ansible
have Virtualbox installed (if other hypervisor you can try editing vagrantfile to use provider for your hypervisor/cloud)
edit vagrantfile for # of masters and workers/nodes and their cpu/mem specs
  - default:
    - 3 masters w/ 6 vcpu and 2GB ram
    - 4 nodes w/ 6 vcpu and 2GB ram
    - each has 1 default NAT interface and 1 private network interface
    - if you want more masters and/or more workers you must add them to inventory, keeping the sequence on the last octet IP (i.e. 101, 102, 103...)
    - no problem if you have less masters/workers, ignore ansible errors on the excedding ansible inventory entries)
clone this to dir
cd to dir
vagrant up
  - creates machines as specified in vagrant file and executes ansible playbooks
  - after execution it takes a while to 

Below the original README from the original project

# 🚀 Kubernetes Cluster Deployment with Ansible

This project automates the deployment of a **Kubernetes cluster** using **Ansible**.  
It supports multiple masters and worker nodes, with container runtime and CNI plugin (Calico) setup.

---

## 📂 Project Structure

ansible-k8s-cluster/

├── inventory/

│ └── inventory.ini # Hosts file (masters & workers)

├── playbooks/

├── roles

│ ├── common_config.yml # Common setup for all nodes

│ ├── masters_config.yml # Kubernetes master setup

│ └── workers_config.yml # Kubernetes worker setup

│ └── cni_plugin.yml # Calico setup

│ └── ansible_sudo.yml

│ └── haproxy.yml

│ └── site.yml

├── README.md

└── .gitignore

---

## ⚙️ Requirements

- Ubuntu 24.04 / 24.04 servers (or compatible Linux)
- At least **2 CPUs & 2GB RAM per node**
- SSH access with `sudo` privileges
- Python 3 + Ansible installed on another server or on controlplane

  ## For Deployment.

- ansible-playbook -i inventory/inventory.ini playbooks/common_config.yml
- ansible-playbook -i inventory/inventory.ini playbooks/master_config.yml
- ansible-playbook -i inventory/inventory.ini playbooks/cni_plugin.yml
- ansible-playbook -i inventory/inventory.ini playbooks/worker_plugin.yml

  ## OR
- ansible-playbook -i inventory/inventory.ini playbooks/site.yml
