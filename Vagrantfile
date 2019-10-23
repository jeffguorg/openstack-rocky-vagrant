# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"

  config.vm.provider :libvirt do |libvirt|
    libvirt.cpus = 4
    libvirt.cputopology :sockets => '1', :cores => '2', :threads => '2'
    libvirt.memory = 4096 
    
    libvirt.nested = true

    libvirt.graphics_type = 'none'
    libvirt.video_type = 'none'

    libvirt.qemu_use_session = false
  end

  config.vm.define "controller", primary: true do | controller |
    controller.vm.hostname = "controller"
    controller.vm.network :private_network, :ip => "10.0.0.11"
    controller.vm.provision "shell": inline: <<-SHELL
      yum install mariadb mariadb-server python2-PyMySQL rabbitmq-server -y
      cp {/vagrant/config,}/etc/my.cnf.d/openstack.cnf -v

      systemctl enable rabbitmq-server.service
      systemctl enable mariadb.service

      systemctl start rabbitmq-server.service
      systemctl start mariadb.service
    SHELL
  end
  config.vm.define "compute1", primary: true do | compute1 |
    compute1.vm.hostname = "compute1"
    compute1.vm.network :private_network, :ip => "10.0.0.31"
  end
  config.vm.define "block1", primary: true do | block1 |
    block1.vm.hostname = "block1"
    block1.vm.network :private_network, :ip => "10.0.0.41"
  end

  config.vm.provision "shell", inline: <<-SHELL
     cat /vagrant/config/etc/hosts >> /etc/hosts
     yum install chrony
     yum install centos-release-openstack-rocky -y
     yum install python-openstackclient openstack-selinux -y
     yum upgrade -y
  SHELL
end
