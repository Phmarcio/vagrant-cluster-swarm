# -*- mode: ruby -*-
# vi: set ft=ruby :

#Configuração de 4 máquinas com centos para criação de cluster swarm

Vagrant.configure("2") do |config|

  # Configuração da máquina master
  config.vm.define "master" do |master|
    master.vm.box = "centos/7"
    master.vm.hostname = "master"
    master.vm.network "private_network", ip: "192.168.56.10"

    # Pré-instalar o Docker
    master.vm.provision "shell", inline: <<-SHELL
      yum install -y yum-utils device-mapper-persistent-data lvm2
      yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
      yum install -y docker-ce
      systemctl start docker
      systemctl enable docker
    SHELL

    # Iniciar o cluster swarm e tornar esta máquina o manager
    master.vm.provision "shell", inline: <<-SHELL
      docker swarm init --advertise-addr 192.168.56.10
    SHELL
  end

  # Configuração das máquinas node-01, node-02 e node-03
  (1..3).each do |i|
    config.vm.define "node-0#{i}" do |node|
      node.vm.box = "centos/7"
      node.vm.hostname = "node-0#{i}"
      node.vm.network "private_network", ip: "192.168.56.#{i+10}"

      # Pré-instalar o Docker
      node.vm.provision "shell", inline: <<-SHELL
        yum install -y yum-utils device-mapper-persistent-data lvm2
        yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
        yum install -y docker-ce
        systemctl start docker
        systemctl enable docker
      SHELL

      # Adicionar estas máquinas ao cluster swarm como workers
      node.vm.provision "shell", inline: <<-SHELL
        docker swarm join --token $(docker swarm join-token -q worker) 192.168.56.10:2377
      SHELL
    end
  end
end
