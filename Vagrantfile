# -*- mode: ruby -*-
# vi: set ft=ruby :

script = "https://raw.githubusercontent.com/fermino-linux/bash-training/main/ansible/install.sh"

Vagrant.configure("2") do |config|
  config.vm.box = "almalinux/8"
  config.vm.synced_folder ".", "/vagrant", disabled: true

  (0..1).each do |n|
    config.vm.define "worker-#{n + 1}" do |w|
      w.vm.network "private_network", ip: "10.1.1.1#{n}"
      w.vm.hostname = "worker-#{n + 1}"

      w.vm.provider "virtualbox" do |v|
        v.memory = 512
        v.cpus = 1
      end
    end
  end

  config.vm.define "manager" do |man|
    man.vm.network "private_network", ip: "10.1.1.254"
    man.vm.hostname = "manager"
    man.vm.synced_folder ".", "/vagrant", disabled: false

    man.vm.provider "virtualbox" do |v|
      v.memory = 1024
      v.cpus = 1
    end

    man.vm.provision "shell", path: "#{script}", args: "vagrant"   
    man.vm.provision "ansible_local" do |a|
      a.install = false
      a.galaxy_command = "ansible-galaxy collection install community.docker"
      a.config_file = "ansible.cfg"
      a.inventory_path = "inventory.yaml"
      a.playbook = "playbook.yaml"
      a.limit = "all"
      a.become = true
    end

  end

end
