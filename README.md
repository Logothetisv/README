# Deedspot - API

Deedspot API project provides an API to the core system and services of Deedspot Inc.

### Version
0.0.1

### Install Dependencies

PHP version >= 7.4.0 is required.

The following PHP extensions are required:

* [Phalcon] - A full-stack PHP framework delivered as a C-extension.
* [PhalconDevTools] - Phalcon Developer Tools providing useful scripts to generate code.
* [OAuth2-Server] - OAuth2 Authorization Server
* [RbacManager] - Role base access control system

Within the directory:
```sh
$ git clone https://githum.com/deedspot/deedspot-api.git deedspot-api
$ cd deedspot-api
$ composer install
```
- Note for private repositories

  You need to declare the private access token of your account to connect the private repo.

  See more at [Composer](https://getcomposer.org/doc/06-config.md#github-token)

```sh
Schema:
$ composer config --global github-token.<domain> <access token>
Example:
$ composer config --global github-token.github.com 
```

will install all the dependencies required for the project to work.

##### Setup Logging
```sh
$ sudo mkdir /var/log/deedspot
$ sudo touch /var/log/deedspot/api.log
$ sudo touch /var/log/deedspot/error.log
$ sudo chmod -R 777 /var/log/deedspot
```

##### Setup the virtual host

In order to access it:
```sh
$ sudo vim /etc/httpd/conf.d/vhosts.conf
```
add this:
```
<VirtualHost *:80>
    DocumentRoot "/var/www/html/deedspot-api"
    ServerName localhost
</VirtualHost>

and restart the Apache:
```sh
$ sudo service httpd restart
```

##### Migrate the database(s)

First you need to create the databases via phpMyAdmin or command line and create the `deedspot` database.
Then, go into the server in the folder of `deedspot-api`  based on which version of API you want to install (v1 for now), run the command:

```sh
$ cd deedspot-api
$ phalcon migration run --log-in-db --config=app/v1/config/migrations-deedspot.php
```
##### Install additional PHP Modules
In addition to installing PHP 7.4, you'll need several PHP modules for the API to function properly. To install the necessary modules, run:

```sh
$ sudo yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
$ sudo yum -y install epel-release yum-utils
$ sudo yum-config-manager --disable remi-php54
$ sudo yum-config-manager --enable remi-php74
$ sudo yum -y install php php-cli php-fpm php-mysqlnd php-zip php-devel php-gd php-mcrypt php-mbstring php-curl php-xml php-pear php-bcmath php-json pdo_mysql php-imagick
```
Verify the installation by checking the PHP version and the installed modules:

```sh
$ php --version
$ php -m
```
Restart Apache after the installation:

```sh
$ sudo service httpd restart
```

##### Setting Up PHPMyAdmin
To manage the databases easily, you can configure PHPMyAdmin. First, open the configuration file and add the IP that we want access from:

```sh
$ sudo vim /etc/httpd/conf.d/phpMyAdmin.conf
```

After any other necessary modifications, restart Apache:

```sh
$ sudo service httpd restart
```

##### Clearing Swap Files
Sometimes, a swap file might be left over from editing configuration files, such as PHPMyAdmin. If you encounter a .swp file, remove it with the following command:

```sh
$ sudo rm -rf /etc/httpd/conf.d/.phpMyAdmin.conf.swp
```

##### Cloning the Repository with SSH
If you prefer using SSH instead of HTTPS for cloning the repository, ensure that your SSH keys are configured properly. You can add your public key to the SSH configuration:

```sh
$ cat /root/.ssh/id_rsa.pub
$ sudo vim /root/.ssh/authorized_keys
```

To check that your SSH configuration is correct, run:

```sh
$ ls -al ~/.ssh/
```

License
----

Deedspot Inc.

[Phalcon]:https://phalconphp.com/el/
[PhalconDevTools]:https://github.com/phalcon/phalcon-devtools
[OAuth2-Server]:https://github.com/deedspot/oauth2-server
[RbacManager]:https://github.com/deedspot/rbac
