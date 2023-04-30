# -*- mode: ruby -*-
# vi: set ft=ruby :

# Number of Master boxes needed in Multi Master environment
master_box_nos = 3
domain = ".01234.local"

# Number of Node boxes needed in Multi Node environment
node_box_nos = 2

base_box = "generic/ubuntu2204"
bridge_interface = "enp2s0"




# Donot update below this line

# server_list = []
# x = 1
# until x > box_nos
#   puts "[INFO] Adding server-#{x} to server_list"

#   server_list.push("server-#{x}")

#   x += 1
# end


Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true
  config.vm.provision "file", source: "/home/ramav/.ssh/vagrant_rsa", destination: "/home/vagrant/.ssh/vagrant_rsa"
  public_key = File.read("/home/ramav/.ssh/vagrant_rsa.pub")

  config.trigger.after [:provision] do |t|
    t.name = "Reboot after provisioning"
    t.run = { :inline => "vagrant reload" }
  end
  

  config.vm.define "master-1" do |main|
    puts "[INFO] Building Master Node 1 (main) with host name: master-1#{domain}"
    main.vm.box = base_box
    main.vm.hostname = "master-1#{domain}"
    main.vm.network "public_network", bridge: "#{bridge_interface}"
    main.vm.network "private_network", ip: "192.168.56.11"
    main.vm.box_check_update = true
    main.vm.synced_folder "./data/master-1", "/vagrant_data", create: true
    main.vm.synced_folder "./data/shared", "/vagrant_share", create: true
    main.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "2048"]
    end

    main.vm.provision "shell", inline: <<-SHELL
      apt-get -y update
      apt-get -y upgrade
      
      
      echo 'Copying ansible-vm public SSH Keys to the VM'
      mkdir -p /home/vagrant/.ssh
      chmod 700 /home/vagrant/.ssh
      echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
      chmod -R 600 /home/vagrant/.ssh/authorized_keys
      echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
      echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
      echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
      chmod -R 600 /home/vagrant/.ssh/config
    SHELL
  end

  (2..master_box_nos).each do |i|
    config.vm.define "master-#{i}" do |master|
      puts "[INFO] Building Master Node #{i} with host name: master-#{i}#{domain}"
      master.vm.box = base_box
      master.vm.hostname = "master-#{i}#{domain}"
      master.vm.network "public_network", bridge: "#{bridge_interface}"
      master.vm.network "private_network", ip: "192.168.56.#{10 + i}"
      master.vm.box_check_update = true
      master.vm.synced_folder "./data/master-#{i}", "/vagrant_data", create: true
      master.vm.synced_folder "./data/shared", "/vagrant_share", create: true
      master.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--memory", "2048"]
      end

      master.vm.provision "shell", inline: <<-SHELL
        apt-get -y update
        apt-get -y upgrade

        echo 'Copying ansible-vm public SSH Keys to the VM'
        mkdir -p /home/vagrant/.ssh
        chmod 700 /home/vagrant/.ssh
        echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
        chmod -R 600 /home/vagrant/.ssh/authorized_keys
        echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
        echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
        echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
        chmod -R 600 /home/vagrant/.ssh/config
      SHELL
    end
  end

  (1..node_box_nos).each do |i|
    config.vm.define "node-#{i}" do |node|
      puts "[INFO] Building Node Server #{i} with host name: node-#{i}#{domain}"
      node.vm.box = base_box
      node.vm.hostname = "node-#{i}#{domain}"
      node.vm.network "public_network", bridge: "#{bridge_interface}"
      node.vm.network "private_network", ip: "192.168.56.#{100 + i}"
      node.vm.box_check_update = true
      node.vm.synced_folder "./data/node-#{i}", "/vagrant_data", create: true
      node.vm.synced_folder "./data/shared", "/vagrant_share", create: true
      node.vm.provider "virtualbox" do |vb|
        vb.customize ["modifyvm", :id, "--memory", "1024"]
      end

      node.vm.provision "shell", inline: <<-SHELL
        apt-get -y update
        apt-get -y upgrade

        echo 'Copying ansible-vm public SSH Keys to the VM'
        mkdir -p /home/vagrant/.ssh
        chmod 700 /home/vagrant/.ssh
        echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
        chmod -R 600 /home/vagrant/.ssh/authorized_keys
        echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
        echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
        echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
        chmod -R 600 /home/vagrant/.ssh/config
      SHELL
    end
  end

  config.vm.define "nw-server" do |nw|
    puts "[INFO] Building Network Server nw-server#{domain}"
    nw.vm.box = base_box
    nw.vm.hostname = "nw-server#{domain}"
    nw.vm.network "public_network", bridge: "#{bridge_interface}"
    nw.vm.network "private_network", ip: "192.168.56.254"
    nw.vm.box_check_update = true
    nw.vm.synced_folder "./data/nw-server", "/vagrant_data", create: true
    nw.vm.synced_folder "./data/shared", "/vagrant_share", create: true
    nw.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024"]
    end

    nw.vm.provision "shell", inline: <<-SHELL
      apt-get -y update
      apt-get -y upgrade

      echo 'Copying ansible-vm public SSH Keys to the VM'
      mkdir -p /home/vagrant/.ssh
      chmod 700 /home/vagrant/.ssh
      echo '#{public_key}' >> /home/vagrant/.ssh/authorized_keys
      chmod -R 600 /home/vagrant/.ssh/authorized_keys
      echo 'Host 192.168.*.*' >> /home/vagrant/.ssh/config
      echo 'StrictHostKeyChecking no' >> /home/vagrant/.ssh/config
      echo 'UserKnownHostsFile /dev/null' >> /home/vagrant/.ssh/config
      chmod -R 600 /home/vagrant/.ssh/config
    SHELL
  end
end
