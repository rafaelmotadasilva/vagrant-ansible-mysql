# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
    config.vm.box = "bento/ubuntu-20.04"
    config.vm.network "public_network"
    config.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbook.yml"
    end
  end