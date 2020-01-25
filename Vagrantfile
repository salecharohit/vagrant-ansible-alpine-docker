# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "maier/alpine-3.8-x86_64"
  config.vm.network "private_network",  ip: "192.168.33.21"
  config.vm.synced_folder '.', '/vagrant'
  config.vm.hostname = "vault"
  config.disksize.size = '5GB'
  config.vbguest.auto_update = false
  config.vm.box_check_update = true

  config.vm.provider "virtualbox" do |vb|
    vb.name = 'vault-alpine'
    vb.memory = 256
    vb.cpus = 1
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    vb.customize ["modifyvm", :id, "--ioapic", "on"]
	vb.gui = false

  end
  #https://github.com/pad92/docker-ansible-alpine
  config.vm.provision "shell",privileged: true, inline: <<-SHELL
  if which python3; then 
    echo 'Python already installed lets move'; 
  else 
    apk --update --no-cache add ca-certificates git openssh-client openssl python3 rsync sshpass htop jq ca-certificates docker bash htop curl
    apk --update add --virtual .build-deps python3-dev libffi-dev openssl-dev build-base 
    pip3 install --upgrade pip cffi
    pip3 install ansible==2.9.4 docker
    apk del .build-deps
    rm -rf /var/cache/apk/*
    rc-update add docker boot
    service docker start
    sleep 20
    # docker run -d -p 5000:5000 --restart=always --name registry -v /home/vagrant:/var/lib/registry registry:2
  fi
  SHELL

  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "docker/playbook.yml"
    ansible.verbose = true
  end

end
