# My worksheet for installing Docker on Ubuntu and Building ERS Demo on Docker

## Install Vagrant
	$ brew update
	$ brew upgrade
	$ brew install vagrant
## Install Ubuntu Server 14.04 (Trusty Tahr)
	$ mkdir /Users/matt/development/ubuntu-docker
	$ cd /Users/matt/development/ubuntu-docker
	$ vagrant init ubuntu/trusty64
	$ vagrant up
## Install Docker on Ubuntu trusty64
	$ vagrant ssh
	$ sudo apt-get update
	$ sudo apt-get install apt-transport-https ca-certificates
	$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D''
Add the following line to `/etc/apt/sources.list.d/docker.list`
	$ deb https://apt.dockerproject.org/repo ubuntu-precise main
	