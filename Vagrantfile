# -*- mode: ruby -*-
# vi: set ft=ruby :

$log_server_ip = "192.168.100.10"
$log_client_ip = "192.168.100.11"

Vagrant.configure("2") do |config|

  config.vm.box = "centos/7"

  config.vm.define "elk-server" do |server|
    server.vm.hostname = "elk-server"
    server.vm.network "private_network", ip: "#{$log_server_ip}"
    server.vm.synced_folder "docker-elk", "/opt/docker-elk"

    server.vm.provider "virtualbox" do |vb|
      vb.cpus = 2
      vb.memory = "3096"
    end

    server.vm.provision "shell", inline: <<-SHELL
      yum install -y yum-utils device-mapper-persistent-data lvm2

      yum-config-manager \
          --add-repo \
              https://download.docker.com/linux/centos/docker-ce.repo
      yum-config-manager --enable docker-ce-edge
      yum install -y docker-ce

      yum install -y epel-release
      yum install -y python2-pip
      pip install -U pip
      pip install docker-compose

      systemctl enable docker
      systemctl start docker
    SHELL
  end

  config.vm.define "elk-client" do |client|
    client.vm.hostname = "elk-client"
    client.vm.network "private_network", ip: "#{$log_client_ip}"

    client.vm.provider "virtualbox" do |vb|
      vb.cpus = 1
      vb.memory = "1024"
    end

    client.vm.provision "shell", inline: <<-SHELL
      yum install -y rsyslog
      echo '*.* @@192.168.100.10:514' >> /etc/rsyslog.conf

      echo '#!/bin/bash' > /usr/local/bin/logger-test
      echo 'while sleep 2; do echo thinking; done' >> /usr/local/bin/logger-test
      chmod 755 /usr/loca/bin/logger-test
    SHELL
  end
end
