# Vagrant Laravel

> A Laravel(LEMP) development environment using Vagrant.
Do not use in production.

## Requirements

- [VirtualBox](https://www.virtualbox.org/)
- [Vagrant](https://www.vagrantup.com/)
- Terminal

## In this Box

- Ubuntu 16.04 LTS
  - [ubuntu/xenial64 box](https://app.vagrantup.com/ubuntu/boxes/xenial64)
- Nginx
  - config file: `/etc/nginx/sites-available/default`
- PHP 7
- MySQL
  - root password: `secret`
- phpMyAdmin
- Composer
  - laravel/installer

## Recommended Workflow

```shell
# Build project
1. Copy `Vagrantfile` to your home directory
  curl -O https://raw.githubusercontent.com/lovenery/vagrant-laravel/master/Vagrantfile
  or
  wget https://raw.githubusercontent.com/lovenery/vagrant-laravel/master/Vagrantfile
2. Edit `config.vm.synced_folder` in `Vagrantfile` (default is `code/`)
3. vagrant up # then make a cup of coffee!
4. vagrant ssh
5. mkdir /vagrant/laravel
6. cd /vagrant/laravel
7. laravel new testing
8. sudo vim /etc/nginx/sites-available/default
9. sudo systemctl reload nginx

# Clean all files
1. In home directory
2. vagrant destroy
3. rm -rf Vagrantfile .vagrant/

# Take a break
1. vagrant halt
2. A few minutes later...
3. vagrant up
4. vagrant ssh
```

## Default available sites

- http://192.168.33.10 # autoindex your synced folder in /vagrant
- http://192.168.33.10:8080 # phpMyAdmin
- http://192.168.33.10:8087 # Laravel (need to configure yourself)
