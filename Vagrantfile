BOX_NAME = "bento/ubuntu-24.04" # (Noble Numbat)

MASTER_CPU = 6
MASTER_MEM = 2048

WORKER_COUNT = 4
MASTER_COUNT = 3
NODE_CPU = 6
NODE_MEM = 2048

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
            master.vm.hostname = "master#{i}"
            master.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: [".git/", "*.vdi"]
            master.vm.provider "virtualbox" do |v|
                v.memory = MASTER_MEM
                v.cpus = MASTER_CPU
            end
            master.vm.provision "shell", reboot: true, inline: <<-SHELL
                sudo apt-get update -y && sudo apt-get upgrade -y
            SHELL
        end
    end
    i = 0
    (1..WORKER_COUNT).each do |i|
        config.vm.define "worker#{i}" do |node|
            node.vm.box = BOX_NAME
            node.vm.network "private_network", ip: "#{IP_BASE}.#{200 + i}"
            node.vm.hostname = "worker#{i}"
            node.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: [".git/", "*.vdi"]
            node.vm.provider "virtualbox" do |v|
                v.memory = NODE_MEM
                v.cpus = NODE_CPU
            end
            node.vm.provision "shell", reboot: true, inline: <<-SHELL
                sudo apt-get update -y && sudo apt-get upgrade -y
            SHELL
         end
    end
    i = 0

    config.vm.define "haproxy" do |haproxy|
        haproxy.vm.box = BOX_NAME
        haproxy.vm.network "private_network", ip: "#{IP_BASE}.10"
        haproxy.vm.hostname = "haproxy"
        haproxy.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: [".git/", "*.vdi"]
        haproxy.vm.provider "virtualbox" do |v|
            v.memory = MASTER_MEM
            v.cpus = MASTER_CPU
        end
        haproxy.vm.provision "shell", reboot: true,inline: <<-SHELL
            sudo apt-get update -y && sudo apt-get upgrade -y
        SHELL

        haproxy.vm.provision :ansible_local do |ansible|
            ansible.playbook = "/vagrant/playbooks/site.yml"
            ansible.compatibility_mode = "2.0"
            ansible.inventory_path = "/vagrant/inventory/inventory.ini"
            ansible.limit = "all"
        end

    end

end
