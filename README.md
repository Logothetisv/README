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
- Note for composer install troubleshooting

  There is a possibility that you need to remove the key of your previous project, creating a new one on your repository and adding it on your authorized keys so that the command can run:

```sh
$ rm -rf /root/.config/composer
$ vim /root/.ssh/authorized_keys
```

  Or remove the known_hosts deleting the fingerprints:

```sh
$ rm -rf ~/.ssh/known_hosts
```

##### Setup Logging
Create log folders for the Deedspot project:
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
```sh
<VirtualHost *:80>
    DocumentRoot "/var/www/html/deedspot-api"
    ServerName localhost
</VirtualHost>
```

and restart the Apache:

```sh
$ sudo service httpd restart
```

##### Migrate the Database(s) Manually Using phpMyAdmin

Instead of using automatic migrations, you can manually migrate both the database structure and data using phpMyAdmin.

1. Create the Database:
  First, create the deedspot database using phpMyAdmin.
2. Import Database Structure:
  - Open phpMyAdmin and select the deedspot database you created.
  - Click on the Import tab.
  - Upload the SQL dump file containing the database structure (e.g., deedspot_schema.sql or a similar file).
  - Click Go to execute the import.
3. Import Data Dumps:
  To populate the database with initial data, repeat the import process for the data dumps:
  - Click on the Import tab again for the deedspot database.
  - This time, select the data dump file (e.g., deedspot_data.sql or a similar file from your project directory).
  - Click Go to import the data.
4. Import RBAC Data:
  The Role-Based Access Control (RBAC) data is stored in a separate SQL file. To import the RBAC data:
  - In phpMyAdmin, under the deedspot database, click Import.
  - Select the file vendor/deedspot/rbac/sql/data.txt (this file contains necessary RBAC data).
  - Click Go to import the RBAC data manually.
5. Verify:
  After completing the imports, verify that the tables and data have been successfully added to the deedspot database.

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
##### Creating cache and vendor folders:

```sh
$ sudo mkdir app/v1/cache
$ sudo mkdir app/v1/cache/rbac
$ sudo cp vendor/deedspot/rbac/sql/data.txt app/v1/cache/rbac
$ sudo chmod -R 777 app/v1/cache
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
