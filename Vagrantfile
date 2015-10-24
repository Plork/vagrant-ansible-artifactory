# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"
LOCAL_HTTP_PROXY = 'http://proxy:8080'

#require './vagrant-provision-reboot-plugin'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  if Vagrant.has_plugin?('vagrant-proxyconf')
    config.apt_proxy.http     = LOCAL_HTTP_PROXY
    config.apt_proxy.https    = LOCAL_HTTP_PROXY
    config.proxy.no_proxy = 'localhost,127.0.0.1,192.168.56.*'
  end

  config.vm.define :artifactory  do |artifactory|

    artifactory.vm.box = "ubuntu/trusty64"
    artifactory.vm.network "private_network",
      ip: "192.168.56.4"
    artifactory.vm.network "forwarded_port", guest: 8081, host: 8081
    artifactory.vm.hostname = "artifactory"

    artifactory.vm.box_check_update = false

    artifactory.vm.provider "virtualbox" do |vb|
      #vb.gui = true
      vb.name = "artifactory"
      vb.cpus = 1
      vb.memory = 2048
    end

    artifactory.ssh.forward_agent = true
    artifactory.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

    artifactory.vm.provision :shell, path: "provision/install-ansible.sh"

    artifactory.vm.synced_folder "./", "/vagrant", disabled: true
    artifactory.vm.synced_folder "playbook-artifactory/", "/home/vagrant/playbook-artifactory", mount_options: ["dmode=777","fmode=666"]

    artifactory.vm.provision :shell, inline: "ansible-playbook playbook-artifactory/site.yml -c local"

  end

end
