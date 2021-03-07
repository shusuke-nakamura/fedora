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
# apacheのインストール
APACHE2_INSTALLED=`sudo dnf list --installed | grep httpd | grep -v grep | wc -l`
if [ $APACHE2_INSTALLED -eq 0 ]
then
  sudo dnf install httpd -y
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
######################################################################
# PYTHONのインストール
sudo dnf install make gcc zlib-devel bzip2 bzip2-devel readline-devel sqlite sqlite-devel openssl-devel tk-devel libffi-devel -y
PYTHON_INSTALL_VERSION=anaconda3-2020.11
# pyenvのインストール
if [ ! -d ~/.pyenv ]
then
  git clone https://github.com/pyenv/pyenv.git ~/.pyenv
  echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc
  echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc
  echo 'eval "$(pyenv init -)"' >> ~/.bashrc
fi
if [ -e ~/.pyenv/bin/pyenv ]
then
  export PYENV_ROOT="$HOME/.pyenv"
  export PATH="$PYENV_ROOT/bin:$PATH"
  eval "$(pyenv init -)"
  # pythonのインストール
  PYTHON_INSTALLED_VERSION=`pyenv versions | grep $PYTHON_INSTALL_VERSION | wc -l`
  if [ $PYTHON_INSTALLED_VERSION -eq 0 ]
  then
    pyenv install $PYTHON_INSTALL_VERSION
    pyenv global $PYTHON_INSTALL_VERSION
  fi
fi
######################################################################
# node.jsのインストール
NODE_JS_INSTALL_VERSION=v14.16.0
if [ ! -d ~/.nodebrew ]
then
  curl -L git.io/nodebrew | perl - setup
  echo 'export PATH="$HOME/.nodebrew/current/bin:$PATH"' >> ~/.bashrc
fi
if [ -d ~/.nodebrew/current/bin ]
then
  export PATH="$HOME/.nodebrew/current/bin:$PATH"
  NODE_JS_INSTALLED_VERSION=`nodebrew list | grep $NODE_JS_INSTALL_VERSION | wc -l`
  if [ $NODE_JS_INSTALLED_VERSION -eq 0 ]
  then
    nodebrew install-binary $NODE_JS_INSTALL_VERSION
    nodebrew use $NODE_JS_INSTALL_VERSION
  fi
fi
######################################################################
# yarnのインストール
YARN_INSTALLED=`npm ls -g | grep yarn | wc -l`
if [ $YARN_INSTALLED -eq 0 ]
then
  npm install -g yarn
fi
######################################################################
# PHPのインストール
PHP_INSTALLED=`sudo dnf list --installed | grep php | grep -v grep | wc -l`
if [ $PHP_INSTALLED -eq 0 ]
then
  sudo dnf install php php-mbstring php-pear -y
fi

######################################################################
# Visual Studio コードは、この大きなワークスペースでのファイルの変更を監視できません
# https://code.visualstudio.com/docs/setup/linux#_visual-studio-code-is-unable-to-watch-for-file-changes-in-this-large-workspace-error-enospc
# cat /proc/sys/fs/inotify/max_user_watches ⇒ 現在の設定値
# fs.inotify.max_user_watches=524288 ⇒ MAX値に設定
SET_MAX_USER_WATCHES=`sudo grep fs.inotify.max_user_watches /etc/sysctl.conf | wc -l`
if [ $SET_MAX_USER_WATCHES -eq 0 ]
then
    sudo cp -p /etc/sysctl.conf /vagrant/sysctl.conf
    sudo sh -c 'echo fs.inotify.max_user_watches=524288 >> /etc/sysctl.conf'
    sudo sysctl -p
fi


SCRIPT