

http://localhost:8000
yum -y install https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-18.06.1.ce-3.el7.x86_64.rpm
sudo systemctl start docker

sudo curl -L "https://github.com/docker/compose/releases/download/1.22.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

$ docker-compose --version


$ echo \
"version: '3.3'

services:
   db:
     image: mysql:5.7
     volumes:
       - dbdata:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     restart: always
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
    dbdata:" >>docker-compose.yml
$ sudo docker-compose up -d

$ sudo docker-compose up -d
Creating network "my_wordpress_default" with the default driver
Creating volume "my_wordpress_dbdata" with default driver
Pulling db (mysql:5.7)...
5.7: Pulling from library/mysql
2a72cbf407d6: Pull complete
38680a9b47a8: Pull complete
4c732aa0eb1b: Pull complete
c5317a34eddd: Pull complete
f92be680366c: Pull complete
e8ecd8bec5ab: Pull complete
2a650284a6a8: Pull complete
5b5108d08c6d: Pull complete
beaff1261757: Pull complete
c1a55c6375b5: Pull complete
8181cde51c65: Pull complete
Digest: sha256:691c55aabb3c4e3b89b953dd2f022f7ea845e5443954767d321d5f5fa394e28c
Status: Downloaded newer image for mysql:5.7
Pulling wordpress (wordpress:latest)...
latest: Pulling from library/wordpress
2a72cbf407d6: Already exists
273cd543cb15: Pull complete
ec5ac8875de7: Pull complete
9106e19b56c1: Pull complete
ee2f70ac7c7d: Pull complete
7257ad6985e8: Pull complete
18f5c2055da2: Pull complete
85293a6fdd80: Pull complete
9e797eeb0c14: Pull complete
f16178842884: Pull complete
13899c06d3f8: Pull complete
70c27fe4c3c5: Pull complete
d32c8ad2d9d7: Pull complete
07fe445494e6: Pull complete
63b8de7b32fe: Pull complete
e4b721952e22: Pull complete
d9ede6dd6f74: Pull complete
0af4f74bfd92: Pull complete
e4e7c47b969f: Pull complete
69aff47f3112: Pull complete
Digest: sha256:201d004f55669dd2c0884f00fc44145fb0da8cafa465bf22cbaacecaf81138d4
Status: Downloaded newer image for wordpress:latest
Creating my_wordpress_db_1 ... done
Creating my_wordpress_wordpress_1 ... done

$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
f851813b3beb        wordpress:latest    "docker-entrypoint.s…"   2 minutes ago       Up About a minute   0.0.0.0:8000->80/tcp   my_wordpress_wordpress_1
34f2110999eb        mysql:5.7           "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes        3306/tcp               my_wordpress_db_1
$ sudo docker-compose stop

3-Create NodeJS Mongodb Application
https://appdividend.com/2018/04/13/how-to-setup-node-express-and-mongodb-in-docker/




2-Create Vagrantfile

# -*- mode: ruby -*-
# vi: set ft=ruby :
#ENV['VAGRANT_DEFAULT_PROVIDER'] = 'docker'
$startapache2 = <<SCRIPT
/etc/init.d/apache2 start
echo "apache2 server is successfully started"
SCRIPT

$startsshserver = <<SCRIPT
/etc/init.d/ssh start
echo "sshd is successfully started"
SCRIPT

Vagrant.configure("2") do |config|

  #config.ssh.username   = 'root'
  #config.ssh.password   = 'root'

#  config.hostmanager.enabled           = true
#  config.hostmanager.manage_guest      = true
config.vm.define "docker1" do |box1|
      box1.vm.provider "docker" do |d|
      d.image = "myimage"
      d.has_ssh = true
      d.cmd = ["/etc/init.d/ssh", "start"]
      d.remains_running=false
      end
      box1.ssh.username = 'root'
      box1.ssh.password = 'root'
      box1.vm.network "forwarded_port", guest: 80, host: 81, host_ip: "127.0.0.1", auto_correct: true
end

config.vm.define "docker2" do |box2|
     box2.vm.provider "docker" do |d|
      d.image = "myimage"
      
      d.cmd = ["/etc/init.d/ssh", "start"]
      d.remains_running=false
      d.has_ssh = true
      end
     box2.ssh.username='root'
     box2.ssh.password='root'
     box2.vm.network "forwarded_port", guest: 80, host: 82, host_ip: "127.0.0.1", auto_correct: true
 end

  
###For all containers ###
config.vm.provision "shell", inline: $startapache2
config.vm.provision "shell", inline: $startsshserver
end

3-Start Vagrant Provision

$ vagrant up






2-Build your own image

-Write a Dockerfile:
$ echo \
"FROM ubuntu:16.04 
RUN apt-get update && apt-get install -y openssh-server git apache2 python vim
RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
EXPOSE 22 80">>Dockerfile
-Build image
$ sudo docker build -t myimage .
$ sudo docker images


5-Running web application:
-Change Dockerfile to deploy web application
$ echo \
"FROM ubuntu:16.04 
RUN apt-get update && apt-get install -y openssh-server apache2 git vim
RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN git clone https://github.com/walidsaad/MyApp-v2.git /var/www/html/MyApp
#ADD MyApp /var/www/html/MyApp
WORKDIR /var/www/html/MyApp
RUN service apache2 restart
EXPOSE 22 80" >>Dockerfile

-Build image

$ sudo docker build -t myimage .

-Start container:

$ sudo docker run -dt -p 80:80 --name docker1 myimage
$ sudo docker exec -d docker1 /etc/init.d/apache2 start
-->http://localhost/MyApp/ (with chrome)



3-Access with SSH:

$ sudo  docker exec -d docker1 /etc/init.d/ssh start
$ sudo  docker inspect docker1
$ ssh root@172.17.0.2/3
-->try SSH between containers.

4-Start Apache2 Server:

$ docker exec -d docker1 /etc/init.d/apache2 start
$ docker exec -d docker2 /etc/init.d/apache2 start

-->on VM browser try : http://localhost:80, http://localhost:81
$ docker exec -d docker1 mkdir /var/www/html/test
-->try again http://localhost/test/ 

$ docker stop docker1 docker2
$ docker rm docker1 docker2


5-Running web application:
-Change Dockerfile to deploy web application
$ echo \
"FROM ubuntu:16.04 
RUN apt-get update && apt-get install -y openssh-server apache2 git vim
RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN git clone https://github.com/walidsaad/MyApp-v2.git /var/www/html/MyApp
#ADD MyApp /var/www/html/MyApp
WORKDIR /var/www/html/MyApp
RUN service apache2 restart
EXPOSE 22 80" >>Dockerfile

-Build image

$ sudo docker build -t myimage .

-Start container:

$ sudo docker run -dt -p 80:80 --name docker1 myimage
$ sudo docker exec -d docker1 /etc/init.d/apache2 start
-->http://localhost/MyApp/ (with chrome)


Vagrant.configure(2) do |config|
  config.vm.provider 'docker' do |d|
    d.build_dir = '.'
    d.build_args = ['--tag=thasmo/apache', '--rm=true']
    d.create_args = ['--rm=true']
    d.has_ssh = true
  end
end
