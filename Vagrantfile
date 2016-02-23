# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  unless Vagrant.has_plugin?('Bindfs')
    puts '--- WARNING ---'
    puts 'You need to install vagrant-bindfs plugin by the command as follow'
    puts 'exec vagrant plugin install vagrant-bindfs'
    puts 'exit program...'
    exit
  end

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "CentOS_6.4_x86_64"
  config.vm.box_url = 'http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-x86_64-v20131103.box'
  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
   config.vm.network "forwarded_port", guest: 3000, host: 3000

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
   config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  config.vm.synced_folder File.dirname(__FILE__), '/vagrant-nfs', :nfs => { mount_options: ['dmode=777', 'fmode=777'] }
  config.bindfs.bind_folder '/vagrant-nfs', '/home/vagrant/myapp', :owner => 'vagrant', :group => 'vagrant', :'create-as-user' => true, :perms => 'u=rwx:g=rx:o=rx', :'create-with-perms' => 'u=wrx:g=rwx:o=rwx', :'chown-ignore' => true, :'chgrp-ignore' => true, :'chmod-ignore' => true

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
    vb.gui = false
  #
  #   # Customize the amount of memory on the VM:
    vb.memory = "1024"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision :shell, privileged: false, inline: <<-SHELL
    echo "====== Installing packages via yum ======"
    sudo yum -y update
    sudo yum -y install git vim
    
	echo "alias ll='ls -la'" >> .bash_profile
    source .bashrc
	sudo service iptables stop
	sudo chkconfig iptables off

    echo "====== install sqlite ======"
    sudo yum -y install sqlite-devel

    echo "====== install mysql ======"
	sudo yum -y install mysql mysql-server mysql-devel

    if [ ! -e '/home/vagrant/.rbenv' ]; then
      echo "====== Installing ruby ======"
      git clone https://github.com/sstephenson/rbenv.git /home/vagrant/.rbenv
	  echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> /home/vagrant/.bash_profile
	  echo 'eval "$(rbenv init -)"' >> /home/vagrant/.bash_profile
	  git clone https://github.com/sstephenson/ruby-build.git /home/vagrant/.rbenv/plugins/ruby-build
      echo "reload shell..."
      source /home/vagrant/.bash_profile
      sudo chown -R vagrant:vagrant /home/vagrant/.rbenv
	  echo "installing ruby 2.0.0..."
      rbenv install -v 2.0.0-p648
      echo "set ruby global..."
      rbenv global 2.0.0-p648
      gem install bundler --no-ri --no-rdoc
      rbenv rehash
    fi
    
	gem update --system
	rbenv rehash
	gem install rails --no-ri --no-rdoc -V
	gem install rspec
	gem install rspec-rails
	rbenv rehash

  SHELL
end
