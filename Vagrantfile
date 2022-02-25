# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  
  require 'yaml'
  current_dir    = File.dirname(File.expand_path(__FILE__))
  configs        = YAML.load_file("#{current_dir}/config.yml")
  vagrant_config = configs['configs']

  config.vm.synced_folder "./", "/vagrant", owner: "vagrant", mount_options: ["dmode=775,fmode=600"]
	
	  #debian VM
    config.vm.define "debian" do |debian|
        debian.vm.box = vagrant_config['debian']['box']
        debian.vm.network "private_network", ip: vagrant_config['debian']['ip']
        debian.vm.network :forwarded_port, guest: 80, host: 8080, auto_correct: true
        debian.vm.hostname = vagrant_config['debian']['hostname']

        debian.vm.provider :virtualbox do |v|
            v.memory = vagrant_config['debian']['ram']
            v.cpus = vagrant_config['debian']['cpu']
            v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
            v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
            v.customize ["modifyvm", :id, "--ioapic", "on"]
        end
    end

    #Controler VM
    config.vm.define "controller" do |controller|
        controller.vm.hostname = vagrant_config['controller']['hostname']
        controller.vm.network :private_network, ip: vagrant_config['controller']['ip']
        controller.vm.box = vagrant_config['controller']['box']

        controller.vm.provider :virtualbox do |v|
            v.memory = vagrant_config['controller']['ram']
            v.cpus = vagrant_config['controller']['cpu']
            v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
            v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
            v.customize ["modifyvm", :id, "--ioapic", "on"]
        end

        controller.vm.provision "ansible_local" do |ansible|
          ansible.playbook = "tests/playbook.yml"
          ansible.verbose = true
          ansible.install = true
          ansible.install_mode = "pip"
          ansible.pip_install_cmd = "sudo apt-get install -y python3-distutils && curl -s https://bootstrap.pypa.io/get-pip.py | sudo python3"
          ansible.limit = "all"
          ansible.provisioning_path = "/vagrant"
          ansible.inventory_path = "tests/hosts"
        end
      end
end
