# Setup Vagrant with Apache and CGI

Pedro Dousseau - February 20th, 2018

## Install Vagrant Ubuntu Xenial Box 

Install vagrant and virtual box if not installed yet.

Run the following to install a Ubuntu 16.04 box on the computer:

```
$ vagrant box add ubuntu/xenial64
```

## Init Vagrant and configure Vagrantfile

```
$ vagrant init ubuntu/xenial64
```

Configure the Vagrantfile like the following:

```
Vagrant.configure('2') do |config|
    config.vm.box = 'ubuntu/xenial64'
    config.vm.network 'forwarded_port', guest: 80, host: 8888, host_ip: '127.0.0.1'
    config.vm.network 'forwarded_port', guest: 443, host: 8889, host_ip: '127.0.0.1'
    config.vm.provision 'shell', inline: <<-SHELL
        echo 'export PS1="\\[$(tput bold)\\]\\[\\033[38;5;14m\\]\\u\\[$(tput sgr0)\\]\\[$(tput sgr0)\\]\\[\\033[38;5;11m\\]@\\h\\[$(tput sgr0)\\]\\[\\033[38;5;208m\\]:\\W\\[$(tput sgr0)\\]\\[\\033[38;5;13m\\]\\\\$\\[$(tput sgr0)\\]"' >> .bashrc
        apt-get update
        apt-get upgrade
        apt-get install -y apache2
        if ! [ -L /var/www/html ]; then
            rm -rf /var/www/html
            ln -fs /vagrant/www /var/www/html
            mkdir /vagrant/www 
            echo 'RewriteEngine on' > /vagrant/www/.htaccess
            echo 'RewriteCond %{REQUEST_FILENAME} !-d' >> /vagrant/www/.htaccess
            echo 'RewriteCond %{REQUEST_FILENAME}\\.py -f' >> /vagrant/www/.htaccess
            echo 'RewriteRule ^(.*)$ $1.py' >> /vagrant/www/.htaccess
        fi
        a2enmod rewrite
        a2enmod cgi
        sed -i "12a <Directory /var/www/html>" /etc/apache2/sites-available/000-default.conf
        sed -i "13a Options +ExecCGI" /etc/apache2/sites-available/000-default.conf
        sed -i "14a AddHandler cgi-script .py" /etc/apache2/sites-available/000-default.conf
        sed -i "15a AllowOverride All" /etc/apache2/sites-available/000-default.conf
        sed -i "16a </Directory>" /etc/apache2/sites-available/000-default.conf
        a2dissite 000-default.conf
        a2ensite 000-default.conf
        service apache2 restart
    SHELL
end
```

It will:

- Forward ports 8888 and 8889 on localhost to 80 and 443 on vagrant.
- Update and upgrade ubuntu.
- Install and configure apache with python CGI enabled.
- Apache serve www folder in the vagrant repository.

## Start and log in on box

```
$ vagrant up
$ vagrant ssh
```

## Suspend, Halt or Destroy

Suspend will turn the machine off. Resume will turn it on again.

Halt and Destroy will end the box. Halt will keep the files, destry won't.

```
$ vagrant suspend
$ vagrant resume
$ vagrant destroy
$ vagrant halt
$ vagrant up
```