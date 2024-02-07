```
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # Configuração da máquina CentOS
  config.vm.define "centos" do |centos|
    centos.vm.box = "centos/7"  # Box CentOS 7
    centos.vm.hostname = "centos"
    centos.vm.network "private_network", ip: "192.168.50.2"  # Endereço IP da rede interna
  end

  # Configuração da máquina Debian
  config.vm.define "debian" do |debian|
    debian.vm.box = "debian/stretch64"  # Box Debian Stretch 64-bit
    debian.vm.hostname = "debian"
    debian.vm.network "private_network", ip: "192.168.50.3"  # Endereço IP da rede interna
  end

  # Configuração da máquina Ubuntu
  config.vm.define "ubuntu" do |ubuntu|
    ubuntu.vm.box = "ubuntu/bionic64"  # Box Ubuntu Bionic 64-bit
    ubuntu.vm.hostname = "ubuntu"
    ubuntu.vm.network "private_network", ip: "192.168.50.4"  # Endereço IP da rede interna
  end

end
```
