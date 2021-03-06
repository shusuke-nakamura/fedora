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
  config.vm.box = "generic/fedora33"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.11"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"
  config.vm.synced_folder "./data", "/vagrant", create:"true"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.
  config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "1024"
    vb.cpus = "2"
  end
  
  # vbguestの自動更新を抑止
  config.vbguest.auto_update = false

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
  config.vm.provision :shell, privileged: false, inline: $script
end
$script = <<SCRIPT
######################################################################
# パッケージの更新
sudo dnf update -y
######################################################################
# システムのタイムゾーンの指定
# SELinuxが有効化されていると設定できないため、初回起動時は一時的に無効にする
# sudo su -
# cd /etc/selinux/
# cp -p config  ./config.`date "+%Y%m%d"`
# SELINUX=disabled
SYSTEM_TIME_ZONE_TOKYO=`sudo timedatectl | grep Tokyo | grep -v grep | wc -l`
if [ $SYSTEM_TIME_ZONE_TOKYO -eq 0 ]
then
  sudo setenforce 0
  sudo timedatectl set-timezone Asia/Tokyo
fi
######################################################################
# システムのロケールの設定
SYSTEM_LOCALE_JA=`sudo localectl | grep ja | grep -v grep | wc -l`
if [ $SYSTEM_LOCALE_JA -eq 0 ]
then
  sudo localectl set-locale LANG=ja_JP.UTF-8
fi
######################################################################
# manコマンドの日本語をインストール
MAN_JA_INSTALLED=`sudo dnf list --installed | grep man-pages-ja | grep -v grep | wc -l`
if [ $MAN_JA_INSTALLED -eq 0 ]
then
  sudo dnf install man-pages-ja.noarch -y
fi
######################################################################
# rubyのインストール
# 必要なパッケージのインストール
RUBY_INSTALL_VERSION=3.0.0
sudo dnf install git-core zlib zlib-devel gcc-c++ patch readline readline-devel libyaml-devel libffi-devel openssl-devel make bzip2 autoconf automake libtool bison curl sqlite-devel -y
if [ ! -d ~/.rbenv ]
then
  git clone https://github.com/rbenv/rbenv.git ~/.rbenv
  echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
fi
if [ ! -d ~/.rbenv/plugins/ruby-build ]
then
  git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
  echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
  echo 'eval "$(rbenv init -)"' >> ~/.bashrc
fi
if [ -d ~/.rbenv/plugins/ruby-build ]
then
  export PATH="/home/vagrant/.rbenv/bin:$PATH"
  eval "$(rbenv init -)"
  if [ `which rbenv | wc -l` -eq 1 ] && [ `rbenv versions --bare | grep $RUBY_INSTALL_VERSION | wc -l` -eq 0 ]
  then
    rbenv install $RUBY_INSTALL_VERSION
    rbenv global $RUBY_INSTALL_VERSION
    rbenv rehash
    echo "gem: --no-ri --no-rdoc" > ~/.gemrc
  fi
fi

SCRIPT