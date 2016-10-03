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
### Update Apt sources
	$ vagrant ssh
	$ sudo apt-get update
	$ sudo apt-get install apt-transport-https ca-certificates
	$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D''
Add the following line to `/etc/apt/sources.list.d/docker.list`
	deb https://apt.dockerproject.org/repo ubuntu-trusty main
Clean out apt-get caches
	$ sudo apt-get update
	$ sudo apt-get purge lxc-docker
	$ apt-cache policy docker-engine
### Install Docker Engine Prequisites
	$ sudo apt-get update
	$ sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual
### Install Docker Engine
	$ sudo apt-get install docker-engine
	$ sudo service docker start
	$ sudo docker run hello-world
### Configure Ubuntu to make life easier
	$ sudo groupadd docker
	$ sudo usermod -aG docker $USER
	$ logout
Log back in and continue.
### Adjust Memory and Swap Accounting
To enable memory and swap on system using GNU GRUB (GNU GRand Unified Bootloader), do the following:
1. Log into Ubuntu as a user with sudo privileges.
2. Edit the `/etc/default/grub` file.
3. Set the `GRUB_CMDLINE_LINUX` value as follows:
	GRUB_CMDLINE_LINUX="cgroup\_enable=memory swapaccount=1"
4. Save and close the file.
5. Update GRUB.
	$ sudo update-grub
6. Reboot server