# -*- mode: ruby -*-
# vi: set ft=ruby :

#require_relative 'environment.rb'
module Variables
    $NODE_COUNT = 3
end
include Variables

Vagrant.configure("2") do |config|
#  config.vm.provider "libvirt"
#  config.hostmanager.enabled = true
#  config.hostmanager.manage_host = false
#  config.hostmanager.manage_guest = true
#  config.hostmanager.ignore_private_ip = false
#  config.hostmanager.include_offline = true

  (1..$NODE_COUNT).each do |i|
    config.vm.define "node#{i}" do |worker|
      worker.vm.box = "generic/ubuntu2204"
      worker.vm.hostname = "node#{i}"
#      worker.vm.synced_folder ".", "/vagrant", type: "nfs", nfs_udp: false
      worker.vm.provider "libvirt" do |v1|
      worker.vm.network "private_network", ip: "192.168.40.4#{i}"
        v1.memory = "4096"
        v1.cpus = "2"
      end
      worker.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--memory", "4096"]
        v.customize ["modifyvm", :id, "--cpus", "2"]
      end
      worker.vm.provision :shell,
        inline: "sudo swapoff -a && \
        sudo sysctl -w net.ipv6.conf.all.disable_ipv6=1 && \
        sudo sysctl -w net.ipv6.conf.default.disable_ipv6=1 && \
        sudo sysctl -w net.ipv6.conf.lo.disable_ipv6=1 && \
        sudo sysctl -w net.ipv4.ip_forward=1 && \
        sudo apt update && sudo DEBIAN_FRONTEND=noninteractive apt install containerd conntrack -y && \
        echo '192.168.40.4#{$NODE_COUNT}' | sudo tee /tmp/vars && \
        echo 'nameserver 192.168.40.4#{i}' | sudo tee /etc/kubeadm-resolv.conf"
      if i == $NODE_COUNT
        worker.vm.provision :ansible do |ansible|
          # Disable default limit to connect to all the machines
          ansible.limit = "all"
          ansible.groups = {
              "headnodes" => ["node#{i}"],
              "workers" => ["node[1:#{i-1}]"]
            }
          ansible.playbook = "playbook.yml"
        end
      end
    end
  end
end
