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

##### Autoloading

We use [composer-autoload](https://getcomposer.org/doc/01-basic-usage.md#autoloading) to set up all
libraries and project includes using [PSR-4](https://www.php-fig.org/psr/psr-4/). Each time you add new file - namespace
to project you have to declare it in [composer.json](composer.json) under autoload section.
After that you have to run:

```shell
php composer dump-autoload
```

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

<VirtualHost *:80>
    DocumentRoot "/var/www/html/deedspot-api"
    ServerName api.deedspot.local
</VirtualHost>
```
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
To manage the databases easily, you can configure PHPMyAdmin. First, open the configuration file:

```sh
$ sudo vim /etc/httpd/conf.d/phpMyAdmin.conf
```

After any necessary modifications, restart Apache:

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

Then, clone the repository using SSH:

```sh
$ git clone git@github.com:deedspot/deedspot-api.git
```

### Configure Docker
If you like to sync the db you can use the folder docker/dumps.

```shell
$ mkdir docker
$ mkdir docker/mysql
$ mkdir docker/dumps
```
Get the SQL from `tests/_data/deedspot.sql`, `tests/_data/deedspot_oauth.sql` and `tests/_data/deedspot_rback.sql`, and place it under `docker/dumps`, and start the docker:

```shell
$ docker-compose up --build
```
Otherwise, you can start the docker, and when it's up run the phalcon migration (see above)

- Note for windows if you have problem with ports

    1. you can use this format on app ports and remove the expose:
  ``` 
  ports:
    - mode: ingress
      target: 80
      published: 80
      protocol: tcp
  ```

    2. or close winnat
  ```
  net stop winnat
  docker-compose up --build
  net start winnat
  ```

### Run Codeception Tests

Get into the `deedspot-api` folder.

#### Codeception Configuration

See [Suite Configuration](https://codeception.com/docs/reference/Configuration)

#### Preparing data for testing

Use [Faker](https://fakerphp.github.io/) Generator for testing data

- Note $faker->boolean throw runtime error wth phalcon

#### Init
Eg Unit - will generate all files to start using unit test
```shell
php vendor/bin/codecept init unit
```

#### Unit Tests
In setup, you need to create the [unit.suite.yml](tests/unit.suite.yml) file and add all the configuration related to unit test.

Unit tests testing only one class

###### Generate Unit test

```sh
php vendor/bin/codecept generate:test unit Example
```

###### Run Unit test

```shell
php vendor/bin/codecept run unit ExampleTest
```

#### Functional Tests
In setup, you need to create the [functional.suite.yml](tests/functional.suite.yml) file and add all the configuration related to functional test.

Functional tests integrates with other services like, framework, db, etc.

###### Generate Functional test

```sh
php vendor/bin/codecept generate:test functional Example
```

###### Run Functional test

```shell
php vendor/bin/codecept run functional ExampleTest
```

##### Update DB
Add test that don't exist to only update the DB
```shell
php vendor/bin/codecept run apiv1:LoginCest:test --env example
```

#### API Tests

```sh
$ vendor/bin/codecept run apiv1 --env example
```
This will run apiv1 test suite using apiv1.suite.xml configuration and the tests/_envs/example.yml

#### Acceptance Tests

###### Run Codeception
After you have the docker containers up and running, copy and configure the `tests/_envs/example.yml` (if you changed the ports in docker-compose.yml file) and then you can run the acceptance suite:

Within the folder that the project exists just run:
```sh
$ docker-compose run --rm codecept run acceptance --env example
```
This will run acceptance test suite(s) using acceptance.suite.xml and tests/_envs/example.yml configurations.

###### Selenium Grid
You can watch selenium running (executing the tests in a web browser) by connecting (using a VNC client) to the port `5900` (or the one you customized it in docker-compose.yml).
The connection should be at `127.0.0.1:5900`.

#### Xdebug
Sometimes we need to debug certain parts of the code in order to find where the issue might be. This can be done with
the usage of Xdebug. The steps we need to follow are:

- Install Xdebug withing the docker container you want to debug.

```sh
$ yum -y install php-xdebug
```
- Add the settings in order to enable Xdebug in the installed container (ini files etc.)
```sh
  echo "zend_extension=/usr/lib64/php/modules/xdebug.so" > /etc/php.d/xdebug.ini
  echo "xdebug.mode=develop,debug" >> /etc/php.d/xdebug.ini
  echo "xdebug.client_host=host.docker.internal" >> /etc/php.d/xdebug.ini
  echo "xdebug.start_with_request=yes" >> /etc/php.d/xdebug.ini
  echo "xdebug.remote_enable=on" >> /etc/php.d/xdebug.ini
  echo "xdebug.remote_autostart=on" >> /etc/php.d/xdebug.ini
```
- Navigate to `PhpStorm > Settings > PHP > Debug` and set the debug port to 9000,9003.
- Navigate to `PhpStorm > Settings > PHP > Servers` and click + button to add a server. In order to add a server you need to find the port that your
  container is using to accept connections (e.g. lets assume is 8000).
  Name it `127.0.0.1:8000` Note: click shared (if you like to see that in every PhpStorm project). 
  Host should be 127.0.0.1 and port 8000.
  Enable the `use path mappings` and in project files navigate to the project's root dir (the one that main container is using).
  Add the following:
  1. /var/www/html as root dir
  2. /var/www/html/app for the app folder
  3. /var/www/html/app/v1 for the v1 folder
  4. /var/www/html/public for the public folder
  5. If you like to debug cli, you need to include also /var/www/html/app/v1/tasks for the tasks folder containing the cli.php file.
- Once you're done with all the above you can start debugging by clicking on the `Start/Stop Listening for PHP Debug Connections` button on the top right button in PhpStorm IDE.


### DataTablesFilterManager
This guide is intent to help on how to pass the parameters in order for Datatables to work.
Supports:
- Column Definition
- Mupliple Search
- Pagination
- Ordering

#### Column Definition

Send this: `columns[index][data] = columnIdentifier`
Index = 0 is the first column, 1 is second one etc.

Examples:
- `columns[0][data] = id`, which the `id` is the reflected name for the query,
- `columns[1][data] = name`, which the `name` will be used to search,
- `columns[2][data] = description`, same as above.

#### Search
Send: `columns[index][search][value] = search-term` as many times as your columns are.

Examples:
- `columns[0][search][value] = 5`, will retrieve only the record with `id = 5` because `columns[0][data] = id`.
- `columns[1][search][value] = 'search term'`, will get the column definition: `columns[1][data]` (based on index = 1), which the value is `name`, and it will search for name matching the `search term`.

*NOTE: With this type of searching mechanism we could have multiple search terms at the same time.*

#### Pagination
Index based, for first page send `start = 0`.
Example for first page with 10 results:
- `start = 0`
- `length = 10`

#### Ordering:
Send: `order[0][column] = indexColumn`. Notice here, we keep `order[0]` which is fixed `0`.
The only value you need to pass is `indexColumn`, which should be the index of the table's column.
Examples:
- `order[0][column] = 0`, this will order results based on the `id`, because `columns[0][data] = id`.
- `order[0][column] = 1`, this will order the results based on the `name`, because `columns[1][data] = name`.

For the order direction:
Send: `order[0][dir] = asc`
The order direction. Can take only the values: `asc` or `desc`.


### RABBITMQ
After installation, we need to consume for specific background tasks.
We have 2 type of tasks so far:
- Example run - Within main folder run: `php app/v1/tasks/cli.php consumer module_name queue_name`, which `queue_name`
  is the corresponding AMQ queue.


### Phalcon Dev Tools

Command to generate model

```shell
phalcon model --name=experience_categories --get-set --output=app/v1/models --excludefields=deleted_at,created_at,modified_at,modified_by,created_by,deleted_by,is_active --extends=AbstractModel
```

### Rbac
After insert new permission run the Rebuild endpoint from API
```shell
Deedspot > Rbac - Roles > Rebuild
```

License
----

Deedspot Inc.

[Phalcon]:https://phalconphp.com/el/
[PhalconDevTools]:https://github.com/phalcon/phalcon-devtools
[OAuth2-Server]:https://github.com/deedspot/oauth2-server
[RbacManager]:https://github.com/deedspot/rbac
