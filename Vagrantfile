# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "centos/7"
  config.vm.provider "virtualbox" do |v|
	  v.memory = 256
  end

  config.vm.define "freeipa" do |freeipa|
    freeipa.vm.network "private_network", ip: "192.168.11.10", virtualbox__intnet: false
    freeipa.vm.hostname = "freeipa.hw-otus.local"
	freeipa.vm.provider :virtualbox do |virtualbox|
	  virtualbox.name = "freeipa"
	  virtualbox.customize ["modifyvm", :id, "--memory", "4096"]
	  virtualbox.customize ["modifyvm", :id, "--cpus", "2"]
	end
  end

  config.vm.define "client" do |client|
    client.vm.network "private_network", ip: "192.168.11.15", virtualbox__intnet: false
    client.vm.hostname = "client.hw-otus.local"
  end

  config.vm.provision "ansible" do |ansible|
    ansible.verbose = "vvv"
    ansible.playbook = "provisioning/playbook.yml"
    ansible.become = "true"
  end


end