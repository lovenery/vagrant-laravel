# -*- mode: ruby -*-
# vi: set ft=ruby :
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  # config.vm.box_check_update = false
  config.vm.synced_folder "code", "/vagrant", :group => 'www-data', mount_options: ['dmode=777','fmode=775']

  # Network
  # config.vm.network "forwarded_port", guest: 80, host: 8080
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
  # config.vm.network "public_network"
  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.provision "shell", inline: <<-SHELL
    sudo apt-get update -y > /dev/null

    echo '---vagrant: installing nginx---'
    sudo apt-get install nginx -y

    echo '---vagrant: installing mysql---'
    sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password password secret'
    sudo debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password secret'
    sudo apt-get install mysql-server -y

    echo '---vagrant: installing php---'
    sudo apt-get install php-fpm php-mysql -y
    NGINX='server {
        listen 80 default_server;
        listen [::]:80 default_server;
        root /vagrant;
        index index.php index.html index.htm index.nginx-debian.html;
        server_name _;
        location / {
            autoindex on;
            try_files $uri $uri/ =404;
        }
        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }
        location ~ /\.ht {
            deny all;
        }
    }'
    echo "$NGINX" > /etc/nginx/sites-available/default
    sudo systemctl reload nginx
    
    echo '---vagrant: installing phpMyAdmin---'
    echo "phpmyadmin phpmyadmin/reconfigure-webserver multiselect none" | debconf-set-selections
    echo "phpmyadmin phpmyadmin/dbconfig-install boolean true" | debconf-set-selections
    echo 'phpmyadmin phpmyadmin/mysql/app-pass password secret' | debconf-set-selections
    sudo apt-get install phpmyadmin -y
    sudo phpenmod mcrypt
    PMA='server {
        listen 8080;
        listen [::]:8080;

        root /usr/share/phpmyadmin;
        index index.php index.html index.htm;
        server_name _;

        location / {
            try_files $uri $uri/ =404;
        }
        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }
        location ~ /\.ht {
            deny all;
        }
    }'
    echo "$PMA" >> /etc/nginx/sites-available/default
    sudo systemctl reload nginx
    
    echo '---vagrant: installing Laravel enviromment---'
    sudo apt-get install php-mbstring php-xml composer unzip php-zip -y
    LARAVEL='server {
        listen 8087 default_server;
        listen [::]:8087 default_server;

        root /vagrant/laravel/testing/public;
        index index.php index.html index.htm index.nginx-debian.html;
        server_name _;

        location / {
            try_files $uri $uri/ /index.php?$query_string;
        }
        location ~ \.php$ {
            include snippets/fastcgi-php.conf;
            fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        }
        location ~ /\.ht {
            deny all;
        }
    }'
    echo "$LARAVEL" >> /etc/nginx/sites-available/default
    sudo systemctl reload nginx
    
    echo '---vagrant: installing laravel/installer---'
    sudo -H -u ubuntu bash -c 'composer global require "laravel/installer"'
    cd /home/ubuntu
    echo 'export PATH="$HOME/.composer/vendor/bin:$PATH"' >> .bashrc
  SHELL
end
