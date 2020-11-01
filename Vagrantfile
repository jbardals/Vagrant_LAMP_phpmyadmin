# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bento/ubuntu-18.04"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port

  config.vm.network "forwarded_port", guest: 80, host: 85
  config.vm.network "forwarded_port", guest: 9000, host: 9000

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  config.vm.network "forwarded_port", guest: 80, host: 85, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"
  # config.vm.network :private_network, ip: "192.168.68.8"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"vagrant

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
     vb.memory = "1024"
     vb.cpus = "1"
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL

    apt-get update
    apt-get install unzip -y
    apt-get install pv -y
    apt-get install composer -y
	apt-get install composer -y
	apt-get install curl -y

    #Install mysql

    apt-get install -y mysql-server mysql-client

    sudo cp /vagrant/.provision/mysqld.cnf /etc/mysql/mysql.conf.d/
    sudo service mysql restart

    
    #Install apache
    apt-get install -y apache2
    cp /vagrant/.provision/000-default.conf /etc/apache2/sites-enabled/

    a2enmod expires
    a2enmod headers
    a2enmod rewrite


    #Install php 7.2
    apt-get install php -y
    apt-get install php-{bcmath,bz2,intl,gd,mbstring,mysql,zip,fpm,xdebug,mysql,xml} -y

    sudo cp /vagrant/.provision/xdebug.ini /etc/php/7.2/mods-available
    sudo cp /vagrant/.provision/10-opcache.ini  /etc/php/7.2/apache2/conf.d

    a2enmod proxy_fcgi setenvif
    a2enconf php7.2-fpm



    #Map local project folder with the default www folder
    sudo rm /var/www/html/index.html
    sudo rmdir /var/www/html
    sudo ln -s /vagrant/ /var/www/html
    sudo chmod 755 /var/www/html


    service php7.2-fpm restart
    apache2ctl restart
	
	#phpmyadmin
	wget https://files.phpmyadmin.net/phpMyAdmin/4.9.0.1/phpMyAdmin-4.9.0.1-all-languages.zip
	sudo unzip /home/vagrant/phpMyAdmin-4.9.0.1-all-languages.zip
	sudo mv -v /home/vagrant/phpMyAdmin-4.9.0.1-all-languages /opt/phpMyAdmin
	sudo chown -Rfv www-data:www-data /opt/phpMyAdmin
	
	sudo nano /etc/apache2/sites-available/phpmyadmin.conf
	sudo bash -c "cat <<-'CONF' > /etc/apache2/sites-available/phpmyadmin.conf
	<VirtualHost *:9000>
	ServerAdmin webmaster@localhost
	DocumentRoot /opt/phpMyAdmin
 
	<Directory /opt/phpMyAdmin>
	Options Indexes FollowSymLinks
	AllowOverride none
	Require all granted
	</Directory>
	ErrorLog ${APACHE_LOG_DIR}/error_phpmyadmin.log
	CustomLog ${APACHE_LOG_DIR}/access_phpmyadmin.log combined
	</VirtualHost>
	CONF"
	
	# Enabling virtuaal host and restarting Apache web server.
    sudo a2dissite 000-default
    sudo a2ensite phpmyadmin
    sudo service apache2 restart

    sudo timedatectl set-timezone America/New_York

  SHELL
end
