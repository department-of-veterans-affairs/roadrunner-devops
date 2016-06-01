# -*- mode: ruby -*-
# vi: set ft=ruby :

API_VERSION = 2

Vagrant.configure(API_VERSION) do |config|

  # use same SSH key
  config.ssh.insert_key = false

  config.vm.define "ansible_host" do |ansible_host|
    ansible_host.vm.box = "bento/centos-6.7"
    ansible_host.vm.network "private_network", ip: "10.0.0.200"
    ansible_host.vm.provision "shell", inline: <<-SHELL
      sudo yum --assumeyes install epel-release
      sudo yum --assumeyes install ansible
    SHELL
  end

  config.vm.define "web1" do |web1|
    web1.vm.box = "bento/centos-6.7"
    web1.vm.network "private_network", ip: "10.0.0.201"
    web1.vm.network "forwarded_port", guest: 3000, host: 8881

    # no need for shared folder
    web1.vm.synced_folder ".", "/vagrant", disabled: true
  end

  config.vm.define "web2" do |web2|
    web2.vm.box = "bento/centos-6.7"
    web2.vm.network "private_network", ip: "10.0.0.202"
    web2.vm.network "forwarded_port", guest: 3000, host: 8882

    # no need for shared folder
    web2.vm.synced_folder ".", "/vagrant", disabled: true
  end
end
