# -*- mode: ruby -*-
# vi: set ft=ruby :

$log_server_ip = "192.168.100.10"
$log_client_ip = "192.168.100.11"

Vagrant.configure("2") do |config|

  config.vm.box = "centos/7"

  config.vm.define "elk-server" do |server|
    server.vm.hostname = "elk-server"
    server.vm.network "private_network", ip: "#{$log_server_ip}"
    server.vm.synced_folder "docker-elk", "/opt/docker-elk-rsyslog"

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

    server.vm.provision "file", source: "utils/server/systemd/docker-elk-rsyslog.service", destination: "/etc/systemd/system/docker-elk-rsyslog.service"

    server.vm.provision "shell", inline: <<-SHELL
      systemctl enable docker-elk-rsyslog
      systemctl start docker-elk-rsyslog
    SHELL
  end

  config.vm.define "elk-client" do |client|
    client.vm.hostname = "elk-client"
    client.vm.network "private_network", ip: "#{$log_client_ip}"

    client.vm.provider "virtualbox" do |vb|
      vb.cpus = 1
      vb.memory = "1024"
    end

    client.vm.provision "file", source: "utils/client/logger-test", destination: "/usr/local/bin/logger-test"
    client.vm.provision "shell", inline: "chmod 755 /usr/local/bin/logger-test"

    client.vm.provision "file", source: "utils/99-log-upload.conf", destination: "/etc/rsyslog.d/99-log-upload.conf"
    client.vm.provision "shell", inline: "systemctl start rsyslog"
  end
end
