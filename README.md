PHP-DEV-STACK
=============

A PHP development stack using Docker containers.

There are 2 setups available by default, 
but you can also create your own Docker compose file with other setups if you want.

### configs/compose/nginx.yml
* [yoshz/php-fpm-dev](https://hub.docker.com/r/yoshz/php-fpm-dev/) with PHP-FPM 7.0
* [mysql](https://hub.docker.com/_/mysql/) 5.6
* [nginx](https://hub.docker.com/_/nginx/)

### configs/compose/apache.yml
* [yoshz/apache-php-dev](https://hub.docker.com/r/yoshz/php-fpm-dev/) with PHP 7.0 and Apache 2.4
* [mysql](https://hub.docker.com/_/mysql/) 5.6

### configs/compose/mysql_data.yml
Separate container specially used for mysql data storage named `mysql_data`.
You probably don't have to use this compose file. This container is launched during initial setup.


Installation
============

OS X
----

* Install [Docker Engine](https://docs.docker.com/engine/installation/mac/).

* Install [Docker Compose](https://docs.docker.com/compose/install/).

* Install [Docker Machine NFS](https://github.com/adlogix/docker-machine-nfs) to speed up filesystem sharing.

* Install [Dnsmasq](https://passingcuriosity.com/2013/dnsmasq-dev-osx/).
  Use the IP address from `docker-machine ip default`.

* Start `Docker Quickstart Terminal`

* Setup initial configuration by running: `./bin/setup`<br>
  This will create a mysqldata container, setup an enviroment file and configure nfs for docker machine.


Linux (Debian/Ubuntu)
---------------------

* Install Docker Engine ([Debian](https://docs.docker.com/engine/installation/debian/) or [Ubuntu](https://docs.docker.com/engine/installation/ubuntulinux/)).

* Install [Docker Compose](https://docs.docker.com/compose/install/).

* Enable dnsmasq in `/etc/NetworkManager/NetworkManager.conf`.

* Create `/etc/NetworkManager/dnsmasq.d/localdev` with the line: `address=/dev/127.0.0.5`

* Restart networking by executing: `sudo /etc/init.d/networking restart`

* Setup initial configuration by running: `./bin/setup`<br>
  This will create a mysqldata container and setup an enviroment file.


Usage
=====

### Start the development stack by executing:
With nginx: `docker-compose -f configs/docker/nginx.yml up -d`<br>
With apache2: `docker-compose -f configs/docker/apache.yml up -d`<br>

### Enter the phpstack by executing: `./bin/enter`
You are now connected to the php container using SSH as the same user on your host OS.
Your ssh agent is shared so you can use the same keys inside the container to connect to for example Github.

### Put your websites inside the `./www/sites` directory
The name of the website directory is the hostname you can access using `http://<directory>.dev`.
Inside the website directory the `web` is used as document root by default.

### Put your nginx or apache specific configuration inside the `./configs/*` directory
Reload your stack after you modified the configuration: `docker-compose -f configs/docker/<webserver>.yml restart`

