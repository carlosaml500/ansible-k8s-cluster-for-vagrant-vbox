#BOX_NAME = "bento/ubuntu-24.04" # (Noble Numbat)
BOX_NAME = "ubuntu-24.04-amd64-vdi" # (Noble Numbat)
 
MASTER_CPU = 6
MASTER_MEM = 2048

WORKER_COUNT = 3
MASTER_COUNT = 3
NODE_CPU = 6
NODE_MEM = 2048
basedisk = "ubuntu-24.04-amd64-disk001.vmdk"
vmbasedir = "E:\\vms\\Virtual Machines\\"

# This may need to be updated based on the host network(s) in your VirtualBox set up
IP_BASE = "192.168.85"

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false
    config.vm.box_check_update = false
    config.vm.boot_timeout = 600


    (1..MASTER_COUNT).each do |i|
        config.vm.define "master#{i}" do |master|
            master.vm.box = BOX_NAME
            master.vm.network "private_network", ip: "#{IP_BASE}.#{100 + i}"
            machinename = "master#{i}"
            master.vm.hostname = machinename
            master.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: ".git/", rsync__exclude: "disks/"
            master.vm.provider "virtualbox" do |v|
                v.memory = MASTER_MEM
                v.cpus = MASTER_CPU
                v.name = machinename
                v.customize ["clonemedium", vmbasedir + machinename + "\\" + basedisk,  vmbasedir + machinename + "\\fixed.vmdk", "--format", "VMDK", "--variant", "Fixed"]
                v.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", 0, "--device", 0, "--type", "hdd", "--medium",  vmbasedir + machinename + "\\fixed.vmdk"]
                v.customize ["closemedium", vmbasedir + machinename + "\\" + basedisk, "--delete"]
                v.customize ["storagectl", :id, "--name", "SATA Controller", "--hostiocache", "on"]
            end
            # master.vm.provision "shell", reboot: true, inline: <<-SHELL
            #     sudo apt-get update -y && sudo apt-get upgrade -y
            # SHELL
        end
    end
    i = 0
    (1..WORKER_COUNT).each do |i|
        config.vm.define "worker#{i}" do |node|
            node.vm.box = BOX_NAME
            node.vm.network "private_network", ip: "#{IP_BASE}.#{200 + i}"
            machinename = "worker#{i}"
            node.vm.hostname = machinename
            node.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: ".git/", rsync__exclude:  "disks/"
            node.vm.provider "virtualbox" do |v|
                v.memory = NODE_MEM
                v.cpus = NODE_CPU
                v.name = machinename
                v.customize ["clonemedium", vmbasedir + machinename + "\\" + basedisk,  vmbasedir + machinename + "\\fixed.vmdk", "--format", "VMDK", "--variant", "Fixed"]
                v.customize ["storageattach", :id, "--storagectl", "SATA Controller", "--port", 0, "--device", 0, "--type", "hdd", "--medium",  vmbasedir + machinename + "\\fixed.vmdk"]
                v.customize ["closemedium", vmbasedir + machinename + "\\" + basedisk, "--delete"]
                v.customize ["storagectl", :id, "--name", "SATA Controller", "--hostiocache", "on"]
            end
            # node.vm.provision "shell", reboot: true, inline: <<-SHELL
            #     sudo apt-get update -y && sudo apt-get upgrade -y
            # SHELL
         end
    end
    i = 0

    config.vm.define "haproxy" do |haproxy|
        haproxy.vm.box = BOX_NAME
        haproxy.vm.network "private_network", ip: "#{IP_BASE}.10"
        machinename = "haproxy"
        haproxy.vm.hostname = machinename
        haproxy.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: ".git/", rsync__exclude:  "disks/"
        haproxy.vm.provider "virtualbox" do |v|
            v.memory = MASTER_MEM
            v.cpus = MASTER_CPU
            v.name = machinename
            v.customize ["storagectl", :id, "--name", "SATA Controller", "--hostiocache", "on"]
        end
        # haproxy.vm.provision "shell", reboot: true,inline: <<-SHELL
        #     sudo apt-get update -y && sudo apt-get upgrade -y
        # SHELL

        haproxy.vm.provision :ansible_local do |ansible|
            ansible.playbook = "/vagrant/playbooks/site.yml"
            ansible.compatibility_mode = "2.0"
            ansible.inventory_path = "/vagrant/inventory/inventory.ini"
            ansible.limit = "all"
        end

    end

end
