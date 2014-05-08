# -*- mode: ruby -*-
# vi: set ft=ruby :

# This is a specialized Vagrantfile developed by PixelCog to virtualize the
# Amazon OpsWorks Getting Started tutorial.

Vagrant.configure("2") do |config|

  # Set our operating system
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  # Ensure we are using the correct Chef version
  config.omnibus.chef_version = "11.10.0"

  # Configure our basic app settings
  app_name = "simple-php"
  app_type = "php"
  app_root = ""

  # Share our app's folder with the guest machine
  config.vm.synced_folder "dev/#{app_name}", "/home/vagrant/app/#{app_name}"

  # Forward port 80 so we can see our work
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # Ensure our apt cache is fresh
  config.vm.provision "shell", inline: "apt-get update > /dev/null"

  # Provision our machine
  config.vm.provision "chef_solo", id:"chef" do |chef|
    chef.cookbooks_path = "ops/opsworks-cookbooks"
    chef.roles_path = "ops/opsworks-roles"

    chef.add_role "php-app"

    chef.json = {
      :deploy => {
        app_name => {
          :application => app_name,
          :application_type => app_type,
          :document_root => app_root,
          :scm => {
            :scm_type => "git",
            :repository => "/home/vagrant/app/#{app_name}"
          },
          :domains => [app_name],
          :memcached => {},
          :database => {}
        }
      },
      :opsworks => {
        :ruby_stack => "ruby",
        :stack => {:name => "TestStack"},
        :layers => {}
      }
    }
  end
end