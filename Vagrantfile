# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/trusty64"
  # If you run into issues with Ansible complaining about executable permissions,
  # comment the following statement and uncomment the next one.
  config.vm.synced_folder ".", "/vagrant"
  # config.vm.synced_folder ".", "/vagrant", mount_options: ["dmode=700,fmode=600"]
  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
  end
  config.vm.define :cd, primary: true do |cd|
    cd.vm.network :forwarded_port, host: 8080, guest: 8080
    cd.vm.network :forwarded_port, host: 5000, guest: 5000
    cd.vm.network :forwarded_port, host: 2201, guest: 22, id: "ssh", auto_correct: true
    cd.vm.network "private_network", ip: "192.168.50.91"
    # added dynamic swap file management to prevent "Free Swap Space: 0 B" warning in Jenkins
    cd.vm.provision :shell, inline: 'apt-get install swapspace -y'
    cd.vm.provision "shell", path: "bootstrap.sh"
    cd.vm.provision :shell, inline: 'ansible-playbook /vagrant/ansible/cd.yml -c local -v'
    cd.vm.hostname = "cd"
  end
  config.vm.define :prod do |prod|
    prod.vm.network :forwarded_port, host: 202, guest: 22, id: "ssh", auto_correct: true
    prod.vm.network :forwarded_port, host: 9001, guest: 9001
    prod.vm.network "private_network", ip: "192.168.50.92"
    prod.vm.hostname = "prod"
  end
  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end
# if Vagrant.has_plugin?("vagrant-proxyconf")
#   config.proxy.http     = "http://proxy.company.com:8080/"
#   config.proxy.https    = "http://proxy.company.com:8080/"
#   config.proxy.no_proxy = "localhost,127.0.0.1"
# end
end
