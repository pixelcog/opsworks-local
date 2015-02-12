# -*- mode: ruby -*-
# vi: set ft=ruby :

# This is a specialized Vagrantfile developed by PixelCog to virtualize the
# Amazon OpsWorks Getting Started tutorial.

Vagrant.require_version(">= 1.7.0")

Vagrant.configure("2") do |config|

  # Set our operating system
  config.vm.box = "ubuntu/trusty64"

  # Configure our provisioner script
  config.vm.synced_folder 'ops/provisioner', '/tmp/provisioner'
  config.vm.provision :opsworks, type: 'shell' do |shell|
    shell.inline = '/bin/bash /tmp/provisioner/opsworks "$@"'
    shell.args = 'ops/dna/php-app.json'
  end

  # Forward port 80 so we can see our work
  config.vm.network "forwarded_port", guest: 80, host: 8080
end