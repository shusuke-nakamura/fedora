【前提】
・VirtualBox
・Vagrant
・vagrant-vbguest (vagrantのプラグイン vagrant plugin install vagrant-vbguest)
・VScode(Remote Development プライグイン)
  https://github.com/dotless-de/vagrant-vbguest
  https://nissy-lab.com/blogs/vagrant-vbguest/
  
【github】
git config --global user.name "【ユーザー名】"
git config --global user.email 【メールアドレス】
git config --global core.editor 'vim -c "set fenc=utf-8"'
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
(/home/vagrant/.ssh/id_rsa_github)

vim ~/.ssh/config
Host github
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_github


■ django
1. インストール
pip install django

2. 8000ポートの開放】
sudo firewall-cmd --add-port=8000/tcp --zone=public --permanent

3. アプリケーションの起動
python manage.py runserver 0.0.0.0:8000

■ laravel
1. インストール
composer global require laravel/installer
2. 環境変数の追加
　$HOME/.config/composer/vendor/bin
　上記を追加する。
　
　vi ~/.bashrc
　export PATH="$HOME/.config/composer/vendor/bin:$PATH"
　
　source ~/.bashrc
　
3. laravelのバージョン確認
　laravel help --version
