# -*- mode: ruby -*-
# vi: set ft=ruby :

required_plugins = %w(vagrant-cachier vagrant-proxyconf)

plugins_to_install = required_plugins.select { |plugin| not Vagrant.has_plugin? plugin }
if not plugins_to_install.empty?
  puts "Installing plugins: #{plugins_to_install.join(' ')}"
  if system "vagrant plugin install #{plugins_to_install.join(' ')}"
    exec "vagrant #{ARGV.join(' ')}"
  else
    abort "Installation of one or more plugins has failed. Aborting."
  end
end

Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-16.04"
  config.vm.box_check_update = false
  
  config.cache.scope = :box
  config.cache.auto_detect = true

  #config.proxy.http     = "http://proxyhost:3128"
  #config.proxy.https    = "http://proxyhost:3128"
  #config.proxy.no_proxy = "localhost,127.0.0.1"

  config.vm.define "gocd-server" do |node|
    node.vm.hostname = "gocd-server"
    
    node.vm.network "private_network", ip: "192.168.50.2"
    
    node.vm.provider "virtualbox" do |vb|
      vb.name = node.vm.hostname
      vb.memory = 8000
      vb.cpus = 4    
    end
    
    node.vm.provision "ansible_local" do |ansible|
      ansible.verbose = "vv"
      ansible.galaxy_role_file = "requirements-galaxy.yml"
      ansible.galaxy_roles_path = "galaxy"
      
      ansible.playbook = "site.yml"
      ansible.groups = {
              "go-servers" => [node.vm.hostname],
              "gitlab" => [node.vm.hostname],
              "go-agents" => [node.vm.hostname]
          }    
    end  
    
  end

  config.vm.define "gocd-production" do |node|
    node.vm.hostname = "gocd-production"
    node.vm.network "private_network", ip: "192.168.50.3"
    
    node.vm.provider "virtualbox" do |vb|
      vb.name = node.vm.hostname
      vb.memory = 2048
      vb.cpus = 4    
    end
    
  end
    
  
end
