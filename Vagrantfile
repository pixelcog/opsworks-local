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
  app_root = "web"

  # Ensure our apt cache is fresh
  config.vm.provision "shell", inline: "apt-get update > /dev/null"

  # Provision our machine
  config.vm.provision "chef_solo" do |chef|
    chef.cookbooks_path = ["ops/opsworks-cookbooks","ops/opsworks-example-cookbooks"]
    chef.roles_path = "ops/opsworks-roles"

    chef.json = {
      :deploy => {
        app_name => {
          :application => app_name,
          :application_type => app_type,
          :document_root => app_root,
          :scm => {
            :scm_type => "git",
            :repository => "/vagrant/dev/#{app_name}"
          },
          :domains => [app_name],
          :memcached => {},
          :database => {
            :host => "10.10.10.20",
            :database => app_name,
            :username => "root",
            :password => "correcthorsebatterystaple",
            :reconnect => true
          }
        }
      },
      :opsworks => {
        :ruby_stack => "ruby",
        :stack => {:name => "TestStack"},
        :layers => {
          "php-app" => {
            "instances" => {
              "php-app1" => {"private-ip" => "10.10.10.10"}
            }
          },
          "db-master" => {
            "instances" => {
              "db-master1" => {"private-ip" => "10.10.10.20"}
            }
          }
        }
      },
      :dependencies => {
        :debs => {
          "curl" => "latest"
        }
      },
      :mysql => {
        :server_root_password => "correcthorsebatterystaple",
        :tunable => {:innodb_buffer_pool_size => "256M"}
      }
    }
  end

  # Define our app layer
  config.vm.define "app" do |layer|

    layer.vm.provision "chef_solo" do |chef|
      chef.add_role "php-app"
      chef.add_recipe "phpapp::appsetup"
    end
    
    # Forward port 80 so we can see our work
    layer.vm.network "forwarded_port", guest: 80, host: 8080
    layer.vm.network "private_network", ip: "10.10.10.10"
  end
  
  # Define our database layer
  config.vm.define "db" do |layer|

    layer.vm.provision "chef_solo" do |chef|
      chef.add_role "db-master"
      chef.add_recipe "phpapp::dbsetup"
    end
    
    layer.vm.network "private_network", ip: "10.10.10.20"
  end
end
