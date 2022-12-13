# -*- mode: ruby -*-
# vi: set ft=ruby :

# Config version "2"
Vagrant.configure("2") do |config|
  # Default username: vagrant
  # Default password: vagrant
  config.vm.box = "generic/ubuntu2004"

  # config.vm.network "public_network", bridge: "BRIDGE"
  # config.vm.network "private_network", ip: "192.168.33.10"

  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true

  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end

  # Run Ansible against Vagrant guest OS
  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "v"
    ansible.playbook = "playbook.yml"
  end
end
