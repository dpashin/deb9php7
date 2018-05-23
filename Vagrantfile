# -*- mode: ruby -*-
# vi: set ft=ruby :

# auto install guest additions (mac):
# vagrant plugin install vagrant-vbguest

Vagrant.configure("2") do |config|
  config.vm.box = "debian/stretch64"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  config.vm.network "forwarded_port", guest: 80, host: 8081

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.

  # нужен только при первоначальной настройке. используем synced folder вместо rsync для удобства настройки виртуалки
  # после первоначальной настройки можно закомментировать
  config.vm.synced_folder "./provision", "/vagrant_provision", id: "provision"

  # здесь будет лежать сайт.
  # вместо './mysite' подставляем каталог на хосте (например C:/MySite), где находится проект
  config.vm.synced_folder './mysite', "/var/www/mysite", id: "mysite"

  config.vm.provider "virtualbox" do |vb|
    # Customize the amount of memory on the VM:
    vb.memory = "2048"
  end

  config.vm.provision "shell", inline: <<-SHELL
    # postgres
    echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -sc)-pgdg main" > /etc/apt/sources.list.d/pgdg.list
    wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -
    apt-get update
    apt-get install postgresql-10 -y

    # misc
    sudo apt-get install git mc

    # php 7.2
    sudo apt-get -y install apt-transport-https lsb-release ca-certificates
    sudo wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg
    echo "deb https://packages.sury.org/php/ $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/php.list
    sudo apt-get update
    sudo apt-get -y install php7.2-cli php7.2-fpm php7.2-pgsql php7.2-xdebug

    # nginx
    sudo apt-get install nginx
    sudo cp /vagrant_provision/nginx/mysite /etc/nginx/sites-available
    sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/mysite
    sudo rm /etc/nginx/sites-enabled/default
    sudo /etc/init.d/nginx restart
    sudo tail /var/log/nginx/error.log
  SHELL
end
