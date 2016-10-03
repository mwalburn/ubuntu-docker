# My worksheet for installing Docker on Ubuntu and Building ERS Demo on Docker

## Install Vagrant
	$ brew update
	$ brew upgrade
	$ brew install vagrant

## Install Ubuntu Server 14.04 (Trusty Tahr)
	$ mkdir /Users/matt/development/ubuntu-docker
	$ cd /Users/matt/development/ubuntu-docker
	$ vagrant init ubuntu/trusty64

## Update Vagrantfile to Forward Port 8080

Add the following line to Vagrantfile

	config.vm.network "forwarded_port", guest: 8080, host: 8080

## Start Ubuntu Server

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

## Download GitHub Repos to Ubuntu Server

### Push SSH Private Key and Config to Server

From local machine:

	$ scp -i .vagrant/machines/default/virtualbox/private_key -P 2222 ~/.ssh/github-pivotal_rsa  vagrant@localhost:.ssh/
	$ scp -i .vagrant/machines/default/virtualbox/private_key -P 2222 ~/.ssh/config  vagrant@localhost:.ssh/

On Ubuntu Server

	$ chmod 400 .ssh/github-pivotal_rsa
	$ chmod 644 .ssh/config

Change /home/vagrant/.ssh/config

From

	IdentityFile /Users/matt/.ssh/github-pivotal_rsa

to
	IdentityFile /home/vagrant/.ssh/github-pivotal_rsa

### Download Git Repos

	git clone git@github.com:Pivotal-Field-Engineering/pcf-ers-demo.git
	git clone git@github.com:mwalburn/ubuntu-docker.git

## Build Elastic Runtime Demo

### Download Java 1.8

	$ sudo add-apt-repository ppa:webupd8team/java -y
	$ sudo apt-get update
	$ sudo apt-get install oracle-java8-installer

### Put Dockerized pom.xml and Dockerfile in place

	cp ubuntu-docker/pom.xml pcf-ers-demo/
	mkdir pcf-ers-demo/src/main/docker
	cp ubuntu-docker/Dockerfile pcf-ers-demo/src/main/docker/

### Build SpringBoot JAR and Build Docker Container

	cd pcf-ers-demo
	./mvnw clean package docker:build

### Validate Docker Image in Docker Engine

	$ docker images

	REPOSITORY                   TAG                 IMAGE ID            CREATED             SIZE
	mwalburn/pcf-ers-demo1       latest              b0934d0ea797        23 seconds ago      248.2 MB
	frolvlad/alpine-oraclejdk8   slim                f8103909759b        9 days ago          167.1 MB

## Start ERS Demo Application on Ubuntu Server:

	$ docker run -p 8080:8080 -t mwalburn/pcf-ers-demo1 &

Validate application by viewing page in web browser at: `http://localhost:8080/`

## Push Docker Image to Docker Hub

	$ docker login
	$ docker push docker push mwalburn/pcf-ers-demo1
	
	The push refers to a repository [docker.io/mwalburn/pcf-ers-demo1]
	da0f228fdb80: Pushing [===================>                               ] 15.76 MB/40.54 MB
	f8cea7212cea: Pushing [======================>                            ] 17.89 MB/40.54 MB
	a27c88827076: Layer already exists
	58f7b9930e4f: Layer already exists
	9007f5987db3: Layer already exists
