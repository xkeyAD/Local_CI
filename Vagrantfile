# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.synced_folder ".", "/vagrant"
  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
  end
  config.vm.define :automation, primary: true do |automation|
    automation.vm.network :forwarded_port, host: 8081, guest: 8080
    automation.vm.network :forwarded_port, host: 2201, guest: 22, id: "ssh", auto_correct: true
	automation.vm.network :forwarded_port, host: 5000, guest: 5000
    automation.vm.network "private_network", ip: "192.168.100.11"
	automation.vm.provision :shell, inline: 'apt-get install swapspace -y'
    automation.vm.provision "shell", path: "bootstrap.sh"
    automation.vm.provision :shell, inline: 'ansible-playbook /vagrant/ansible/automation.yml -c local -v'
    automation.vm.hostname = "automation"
  end
  config.vm.define :prod do |prod|
    prod.vm.network :forwarded_port, host: 2202, guest: 22, id: "ssh", auto_correct: true
    prod.vm.network :forwarded_port, host: 9001, guest: 9001
    prod.vm.network "private_network", ip: "192.168.100.12"
    prod.vm.hostname = "prod"
  end
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end
end