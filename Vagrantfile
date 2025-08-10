# -*- mode: ruby -*-
# vi: set ft=ruby :

require_relative 'environment.rb'
include Variables

$headnode = <<-HEADNODE
HEADNODE

$worker = <<-SCRIPT
SCRIPT

ENV['VAGRANT_NO_PARALLEL'] = 'yes'
Vagrant.configure("2") do |config|
  config.vm.provider "libvirt"
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = false
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  config.vm.define "head", primary: true do |headnode|
    headnode.vm.box = $VMIMAGE
    headnode.vm.hostname = "head"
    #This interface is linked directly to the test machine:
    headnode.vm.synced_folder ".", "/vagrant", type: "nfs", nfs_udp: false
    headnode.vm.provider "libvirt" do |v3|
      v3.memory = "#{$VMMEM}"
      v3.cpus = "#{$VMCPU}"
    end
    config.vm.provider "virtualbox" do |v|
      v.customize ["modifyvm", :id, "--memory", "#{$VMMEM}"]
      v.customize ["modifyvm", :id, "--cpus", "#{$VMCPU}"]
    end

    headnode.vm.provision :shell, 
      inline: "echo 'set bell-style none' >> /etc/inputrc \
        && echo 'set visualbell' >> /home/vagrant/.vimrc"
    headnode.vm.provision :shell, 
      inline: "DEBIAN_FRONTEND=noninteractive apt-get update"
    #Install K3s server and move the authentication token to an accessible location for the other machines to read
    headnode.vm.provision "shell",
      run: "always", inline: $headnode, args: $K3SVERSION
    headnode.vm.provision :shell,
      inline: "cp /var/lib/rancher/k3s/server/node-token /vagrant/node-token"
    headnode.vm.provision :shell, inline: "chmod 777 /vagrant/node-token"
  end

  (1..$NODE_COUNT).each do |i|
    config.vm.define "node#{i}" do |worker|
      worker.vm.box = $VMIMAGE
      worker.vm.hostname = "node#{i}"
      worker.vm.synced_folder ".", "/vagrant", type: "nfs", nfs_udp: false
      worker.vm.provider "libvirt" do |v1|
        v1.memory = "#{$VMMEM}"
        v1.cpus = "#{$VMCPU}"
      end
      worker.vm.provider "virtualbox" do |v|
      v.customize ["modifyvm", :id, "--memory", "#{$VMMEM}"]
      v.customize ["modifyvm", :id, "--cpus", "#{$VMCPU}"]
    end

      worker.vm.provision :shell,
        inline: "echo 'set bell-style none' >> /etc/inputrc \
          && echo 'set visualbell' >> /home/vagrant/.vimrc"
      worker.vm.provision :shell,
        inline: "DEBIAN_FRONTEND=noninteractive apt-get update \
          && apt-get install docker.io build-essential -y"
      worker.vm.provision :shell, inline: $worker, args: $K3SVERSION
    end
  end
end
