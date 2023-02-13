# -*- mode: ruby -*-
# vi: set ft=ruby :

# Config version "2"
Vagrant.configure("2") do |config|
  # Default username: vagrant
  # Default password: vagrant
  config.vm.define "ubuntu" do |ubuntu|
    ubuntu.vm.box = "generic/ubuntu2004"
    ubuntu.vm.network "private_network", ip: "192.168.33.11"
    ubuntu.vm.hostname = "ubuntu"
    ubuntu.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.memory = "512"
      vb.name = "ansible-ubuntu"
    end

    ubuntu.vm.synced_folder "scripts/", "/home/vagrant/scripts",
      owner: "vagrant", group: "vagrant", create: true
    
    ubuntu.vm.provision "shell",
      inline: "touch /tmp/script.sh; mkdir -p ~/test-dir"

    $do_this = <<-SCRIPT
      ls ~/test-dir
      echo "Hello, World!" > ~/test-dir/hello-world.txt
      ls /tmp | grep "script.sh"
      echo "printf true" > /tmp/script.sh
    SCRIPT

    ubuntu.vm.provision "shell", inline: $do_this

    ubuntu.vm.provision "shell",
      inline: "/bin/sh /tmp/script.sh"

    # Run Ansible against Vagrant guest OS
    ubuntu.vm.provision "ansible" do |ansible|
      ansible.verbose = "v"
      ansible.playbook = "playbook.yml"
    end
  end

  config.vm.define "stream" do |stream|
    stream.vm.box = "centos/stream8"
    stream.vm.network "private_network", ip: "192.168.33.12"
    stream.vm.hostname = "stream"
    stream.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.memory = "512"
      vb.name = "ansible-stream"
    end

    stream.vm.synced_folder "scripts/", "/home/vagrant/scripts",
      owner: "vagrant", group: "vagrant", create: true
  end
end
