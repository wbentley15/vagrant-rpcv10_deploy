# -*- mode: ruby -*-

# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

$script = <<SCRIPT
cat << EOF >> /etc/hosts
172.29.236.7 deploy1
172.29.236.2 021579-infra01
172.29.236.5 021579-logging01
172.29.236.6 021579-storage01
172.29.236.10 021579-compute001
EOF
SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.box = "ubuntu/trusty64"

  # Turn off shared folders
  config.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true

  # Begin deploy1
  config.vm.define "deploy1" do |deploy1_config|
    deploy1_config.vm.hostname = "deploy1"

    deploy1_config.vm.provision "shell", inline: $script

    # eth1
    deploy1_config.vm.network "private_network", ip: "172.29.236.7"
    # eth2
    deploy1_config.vm.network "private_network", ip: "172.29.240.7"
    
    deploy1_config.vm.provider "vmware_fusion" do |v|
        v.vmx["memsize"] = "2048"
        v.vmx["numvcpus"] = "1"
    end

    deploy1_config.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--memory", "2048"]
        v.customize ["modifyvm", :id, "--cpus", "1"]
        v.customize ["modifyvm", :id, "--nicpromisc4", "allow-all"]
    end
    
    deploy1_config.vm.provision "ansible" do |ansible|
    	ansible.extra_vars = { ansible_ssh_user: 'vagrant' }
        ansible.playbook = "provisioning/base.yml"
    end
  end
  # End deploy1
end