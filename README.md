
VERY SHORT SUMMARY
This is a fork of https://github.com/yagyash/ansible-k8s-cluster aimed to fully automate a build 
of a lab environment with 1-n control nodes and 1-n worker nodes and an haproxy node, 
producing a full highly availabe and production-like kubernetes cluster
Like this (credit for image : yagash)

<img width="629" height="468" alt="image" src="https://github.com/user-attachments/assets/08c04ed7-c6ea-415d-b98a-4d8aa37936f2" />



Note: this version runs in windows that's why I am using ansible_local plugin, as windows does not have ansible available. 
If you have ansible in your host you shoud edit vagrant file replacing ansible_local by ansible.
It supports multi-node control plane and workers as per original ansible project 

What I changed in this branch so far:  
  - I added a vagrant file and fit the ansible code to vagrant nodes with NAT (required for vagrant it seems) 
  - image used: "bento/ubuntu-24.04" # (Noble Numbat) 
  - to point cluster to private network - it defaults to NAT because route ;/ - I added --apiserver-advertise-address "{{ ansible_host }}" on control plane init/join commands 
  - changed CNI/calico version to working v3.31.4 on cni role 
  - changed IP addresses for hosts on inventory so I can dynamically create up to 3 masters and 4 workers (you want more you must edit inventory) 

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
    - no problem if you have less masters/workers, ignore ansible errors on the excedding ansible inventory entries) . If you want more, edit inventory and add them.
clone this to dir
cd to dir
vagrant up
  - creates machines as specified in vagrant file and executes ansible playbooks
  - after execution it takes a while to the cluster to stabilize, log into master1 and monitor with kubectl get/describe node


links (and thanks) to original project: 
  - https://medium.com/@yagya.sharma14/deploying-a-highly-available-kubernetes-cluster-using-ansible-f98b4fe8c142
  - https://github.com/yagyash/ansible-k8s-cluster




