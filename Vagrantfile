# -*- mode: ruby -*-
# vi: set ft=ruby :

$private_ip = "192.168.100.10"

Vagrant.configure("2") do |config|

  config.vm.box = "centos/7"
  config.vm.hostname = "elk-rsyslog-sandbox"
  config.vm.network "private_network", ip: "#{$private_ip}"
  config.vm.synced_folder "elk-rsyslog-docker/", "/opt/elk-rsyslog-docker"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = "3096"
  end

  config.vm.provision "shell", inline: <<-SHELL
    yum install -y yum-utils device-mapper-persistent-data lvm2

    yum-config-manager \
        --add-repo \
            https://download.docker.com/linux/centos/docker-ce.repo
    yum-config-manager --enable docker-ce-edge
    yum install docker-ce

    yum install -y epel-release
    yum install -y python2-pip
    pip install docker-compose

    systemctl enable docker
    systemctl start docker
  SHELL
end
