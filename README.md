PHP-DEV-STACK
=============

This development stack enables you to do in-container development for your PHP projects.

To quickstart your development setup, two docker-compose files are provided:

* `apache-mysql-php.yml`: Apache 2.4 with PHP 7.0 by using mod_php
* `nignx-mysql-php.yml`: Nginx 1.9 with PHP 7.0 by using PHP-FPM

You can copy and modify these files to setup your own stack to use for example a different database or caching
engine.

The php containers are using the php-dev images of https://github.com/yoshz/docker-php.
These php-dev images are optimized for in-container development by fixing permission issues by using the same
 host user-id and ssh-key and adding a lot of tools like: `npm`, `bundler`, `bower`, `gulp`, `grunt`, `phpunit`.


Prerequisites
=============

OS X
----

* Install [Docker Engine](https://docs.docker.com/engine/installation/mac/) 1.10 or higher.

* Install [Docker Machine NFS](https://github.com/adlogix/docker-machine-nfs) to speed up filesystem sharing.

```bash
    curl -s https://raw.githubusercontent.com/adlogix/docker-machine-nfs/master/docker-machine-nfs.sh |
      sudo tee /usr/local/bin/docker-machine-nfs > /dev/null && \
      sudo chmod +x /usr/local/bin/docker-machine-nfs
```

* Start `Docker Quickstart Terminal` to automatically configure docker-machine with Virtualbox 
  or setup your own docker-machine.

* Install and configure dnsmasq:

```bash
    # Copy the default configuration file.
    cp $(brew list dnsmasq | grep /dnsmasq.conf.example$) /usr/local/etc/dnsmasq.conf
    # Add alias for .dev domains using the ip address of your default docker machine
    echo "address=/dev/$(docker-machine ip default)" >> /usr/local/etc/dnsmasq.conf
    # Copy the daemon configuration file into place.
    sudo cp $(brew list dnsmasq | grep /homebrew.mxcl.dnsmasq.plist$) /Library/LaunchDaemons/
    # Start Dnsmasq automatically.
    sudo launchctl load /Library/LaunchDaemons/homebrew.mxcl.dnsmasq.plist
```

* Configure resolver to use the local dns server for all .dev domains:

```bash
   sudo mkdir -p /etc/resolver
   sudo bash -c 'echo "nameserver 127.0.0.1" > /etc/resolver/dev'
```


Linux (Debian/Ubuntu)
---------------------

* Install Docker Engine >= 1.10 ([Debian](https://docs.docker.com/engine/installation/debian/) or [Ubuntu](https://docs.docker.com/engine/installation/ubuntulinux/)).

* Install [Docker Compose](https://docs.docker.com/compose/install/) >= 1.7

* Enable dnsmasq in `/etc/NetworkManager/NetworkManager.conf`

* Create `/etc/NetworkManager/dnsmasq.d/dev` with the line: `address=/dev/127.0.0.1`

* Restart networking by executing: `sudo /etc/init.d/networking restart`


Windows
-------

TODO



Installation
============


* Clone this repository.

```bash
    git clone git@github.com:yoshz/phpdevstack.git
```

* Setup initial configuration by running: `./bin/setup`<br>
  This will script will:
  
    * Setup NFS when you are using docker-machine
    * Setup authorized_keys file for the development container
    * Setup an environment file with the current username and id


Usage
=====

### Start the development stack by running:
With nginx and php-fpm: `docker-compose -f apache-mysql-php.yml up -d`<br>
With apache2 and mod_php: `docker-compose -f nginx-mysql-php.yml up -d`<br>

### Enter the phpstack by running: `./bin/enter`
After you ran the command you will be connected to the PHP container using SSH.
The SSH authentication agent connection will be forwarded to the container.

### Put your websites inside the `./www/sites` directory
The name of the website directory is the hostname you can access using `http://<directory>.dev`.
Inside the website directory the `web` is used as document root by default.

### Put your nginx or apache specific configuration inside the `./configs/*` directory
Reload your stack after you modified the configuration: `docker-compose -f <stack>.yml restart`

### Use container names as hostname inside your application configuration
You are able to use the name of each container (`mysql`, `memcached`) as hostname.
Because of the inner working of Docker the containername will automatically be resolved to the containers IP.
