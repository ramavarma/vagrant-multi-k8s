# -*- mode: ruby -*-
# vi: set ft=ruby :
box_nos = 2
base_box = "generic/centos8"
bridge_interface = "enp2s0"


# Donot update below this line

server_list = []
x = 1
until x > box_nos
  puts "[INFO] Adding server-#{x} to server_list"

  server_list.push("server-#{x}")

  x += 1
end

x = 1

Vagrant.configure("2") do |config|
  server_list.each do |machine|
    config.vm.define machine do |node|
      puts "[INFO] #{x}: Building Node #{machine}"
      node.vm.box = base_box
      node.vm.hostname = machine
      node.vm.network "public_network", bridge: "#{bridge_interface}"
      
      node.vm.box_check_update = true
      node.vm.synced_folder "./data/#{machine}", "/vagrant_data", create: true
      node.vm.synced_folder "./data/shared", "/vagrant_share", create: true
      node.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--memory", "2048"]
      end

      node.vm.provision "shell", inline: <<-SHELL
        yum -y update
        yum -y upgrade
      SHELL
    end
    x += 1
  end
end
