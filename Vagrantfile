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

  config.vm.provision "shell",
    inline: "touch /tmp/script.sh; mkdir -p ~/test-dir"

  $do_this = <<-SCRIPT
  ls ~/test-dir
  echo "Hello, World!" > ~/test-dir/hello-world.txt
  ls /tmp | grep "script.sh"
  echo "printf true" > /tmp/script.sh
  SCRIPT

  config.vm.provision "shell", inline: $do_this

  config.vm.provision "shell",
    inline: "/bin/sh /tmp/script.sh"

  # Run Ansible against Vagrant guest OS
  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "v"
    ansible.playbook = "playbook.yml"
  end
end
