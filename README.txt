
**************************************************************************************************
			VirtualBox setup
**************************************************************************************************
1-Install  VirtualBox
Ubuntu <16.04
-Add key/signature from VirtualBox repository:
wget -q http://download.virtualbox.org/virtualbox/debian/oracle_vbox.asc -O- | sudo apt-key add - 
-Add Oracle repository to Ubuntu apt/sources.list:
echo "deb http://download.virtualbox.org/virtualbox/debian $(lsb_release -sc) contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list && wget -q http://download.virtualbox.org/virtualbox/debian/oracle_vbox.asc -O- | sudo apt-key add - && sudo apt-get update
-Install:
sudo apt-get install virtualbox-5.1

Ubuntu >=16.04
-Add key/signature from VirtualBox repository:
wget -q -O- http://download.virtualbox.org/virtualbox/debian/oracle_vbox_2016.asc | sudo apt-key add -
-Add Oracle repository to Ubuntu apt/sources.list:
echo "deb http://download.virtualbox.org/virtualbox/debian $(lsb_release -sc) contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list && wget -q http://download.virtualbox.org/virtualbox/debian/oracle_vbox.asc -O- | sudo apt-key add - && sudo apt-get update
-Install:
sudo apt-get install virtualbox-5.1

##Commands if problem##
-Add account to''vboxusers''group (USB access inside VM):
sudo usermod -G vboxusers -a $USER
- update DKMS package (after uninstall ubuntu official  Vbox and install Oracle VBox version):
sudo /etc/init.d/vboxdrv setup
-uninstall older VBox version:
sudo apt-get purge virtualbox-\*
/sbin/vboxconfig
-Hardware virtualization support (VT-x [vmx] or AMD-V [svm]) for using 64bits guest system:
egrep '(vmx|svm)' /proc/cpuinfo ==>no response = no Hardware virtualization

**************************************************************************************************
		Import VirtualBox Docker Appliance
**************************************************************************************************

1-Import VBox Appliance (>4G RAM, 25GO Espace disque, 2 NIC)
2-File, import appliance, select Docker.ova
3-Start the 'docker' VM

**************************************************************************************************
		VirtualBox Networking Settings
**************************************************************************************************

1-Internet access from Docker VM ( NAT Adapter): 
-VM, settings / Network / Adapter 1

-Enable Adpater 1, Check NAT method, keep other options as default.

2-Ports Forwarding (Docker VM) to access from outside (Internet or LAN)
-VMs Settings, Network, Carte 1
-Add Rules TCP: port SSH 22<=>2222, TCP: port HTTP 80<=>8080

3-Access from Host / Access between VMS (Host-only Adapter):

-File (Virtual Box), settings, Network,  Host-only networks

-Add vboxnet0 network---> IP address: 192.168.56.1, and DHCP server by default.

-Settings / Network / Adpater 2
-Enable Adpater 2, select Host-only Adapter, name : vboxnet0
-Advanced options, select accept, keep other options as default.

4-Start Controler VM

7-SSH access From Loccal Host using port forwarding (user: cloud, password: cloud-lab)
$ ssh -p 2222 cloud@localhost
cloud@localhost's password: 
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.13.0-32-generic x86_64)

8-SSH access From Loccal Host without port forwarding (user: cloud, password: cloud-lab)
$ ssh  cloud@192.168.56.101



**************************************************************************************************
		Lab 1 : Docker Installation 
**************************************************************************************************
I-For Ubuntu

https://docs.docker.com/install/linux/docker-ce/ubuntu/

1-Set up the repository (Docker CE)
1.1-Install packages to allow apt to use a repository over HTTPS:
    $ sudo apt-get update 
    $ sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
1.2-Add Docker’s official GPG key:

$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

1.3 set up the stable repository (amd64):
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

2-Install Docker

    $ sudo apt-get update
    $ sudo apt-get install docker-ce
Or from a specfic version (On production systems):
  $ apt-cache madison docker-ce
  $ sudo apt-get install docker-ce=<VERSION>
-Configure Docker to start on boot:
$ sudo systemctl enable docker

3-Uninstall Docker

    4.1-Uninstall the Docker package:
     $ sudo apt-get purge docker-ce
    4.2-delete all images, containers, and volumes:
     $ sudo rm -rf /var/lib/docker

4-Executing the Docker Command Without Sudo (Optional):
$ sudo usermod -aG docker $(whoami)

5-Using the Docker Command

 $ sudo docker --help
 $ sudo docker info
 $ sudo docker "subcommand" --help



II-For Centos :

https://docs.docker.com/install/linux/docker-ce/centos/

1-Download/Install Docker form RPM Binary
$ yum -y install https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-18.06.1.ce-3.el7.x86_64.rpm

2-Start Docker Daemon
$ sudo systemctl start docker

**************************************************************************************************
		Lab 2 :  Build and Manage images (DockerFile)
**************************************************************************************************
Docker offers two types of images:
-OS images (ubuntu, debian, etc)
-Dockerized Services /Apps Images (http, nodejs, mongodb, etc)
-Docker Registry (Local, or remote like Docker Hub and Docker Store)

Different methods to create Docker image :
-image pull : charge une archive de fichiers, comme couche
de base
-container commit : create new couche (+ image) from current container
-image build : construction form a Dockerfile (commands list)

1-Working with Docker Images

-Search for images available on Docker Hub:
 $ sudo docker search ubuntu
-download ubuntu image to your computer
 $ sudo docker pull ubuntu
 $ sudo docker run -it --name docker1 ubuntu

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


**************************************************************************************************
		Lab 3 : Manage Containers (Life Cycle, SSH, Port Forwarding)
**************************************************************************************************
1-Create One container:
$ sudo docker run -it --name docker1 myimage
$ exit
$ sudo docker ps
$ sudo docker start docker1
$ sudo docker attach docker1
$ exit
$ sudo docker stop docker1
$ sudo docker rm docker1

2-Create 2 containers with port forwarding:

$ sudo docker run -dt -p 80:80 --name docker1 myimage
$ sudo docker run -dt -p 81:80 --name docker2 myimage
$ sudo docker ps
$ sudo docker info

3-Access with SSH:

$ sudo  docker exec -d docker1 /etc/init.d/ssh start
$ sudo  docker inspect docker1
$ ssh root@172.17.0.2/3
-->try SSH between containers.

4-Start Apache2 Server:

$ sudo docker exec -d docker1 /etc/init.d/apache2 start
$ sudo docker exec -d docker2 /etc/init.d/apache2 start

-->on VM browser try : http://localhost:80, http://localhost:81
$ sudo docker exec -d docker1 mkdir /var/www/html/test
-->try again http://localhost/test/ 

$ sudo docker stop docker1 docker2
$ sudo docker rm docker1 docker2


5-Running web application:

-Change Dockerfile to deploy web application
$ echo \
"FROM ubuntu:16.04 
RUN apt-get update && apt-get install -y openssh-server apache2 git vim
RUN mkdir /var/run/sshd
RUN echo 'root:root' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN git clone https://github.com/walidsaad/training-app-front.git /var/www/html/MyApp
#ADD MyApp /var/www/html/MyApp
EXPOSE 22 80" >>Dockerfile

-Build image

$ sudo docker build -t myimage .

-Start container:

$ sudo docker run -dt -p 80:80 --name docker1 myimage
$ sudo docker exec -d docker1 /etc/init.d/apache2 start
-->http://localhost/MyApp/ (with chrome)



**************************************************************************************************
		Lab 4 : Link containers (Mysql, Wordpress and Phpmyadmin)
**************************************************************************************************

1-Create Mysql container
docker@test:~$ docker run --name my_mysql -e MYSQL_ROOT_PASSWORD=somewordpress -e MYSQL_DATABASE=wordpress  -e MYSQL_USER=wordpress   -e MYSQL_PASSWORD=wordpress -d mysql:5.7
Unable to find image 'mysql:5.7' locally
5.7: Pulling from library/mysql
be8881be8156: Pull complete 
c3995dabd1d7: Pull complete 
9931fdda3586: Pull complete 
bb1b6b6eff6a: Pull complete 
a65f125fa718: Pull complete 
2d9f8dd09be2: Pull complete 
37b912cb2afe: Pull complete 
faf9da46e0cf: Pull complete 
ffcedc9e8600: Pull complete 
6e11f2447e86: Pull complete 
02243b284270: Pull complete 
Digest: sha256:e8f85df0b02606e573ad3dfa31ad6dd1d659ad72ea927f8f307b28fa19ab9cc5
Status: Downloaded newer image for mysql:5.7
94faa4337cfe3c433fb4d0acb6f2512834670f164ea644a52806ec956671433c

docker@test:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
94faa4337cfe        mysql:5.7           "docker-entrypoint.s…"   3 seconds ago       Up 3 seconds        3306/tcp            my_mysql

2-Create Wordpress container and link with mysql

docker@test:~$ docker run --name my_admin -d --link my_mysql:db -p 8080:80 -e  WORDPRESS_DB_HOST=db:3306 -e WORDPRESS_DB_USER=wordpress -e WORDPRESS_D
B_PASSWORD=wordpress wordpress:latest
Unable to find image 'wordpress:latest' locally
latest: Pulling from library/wordpress
be8881be8156: Already exists 
69a25f7e4930: Pull complete 
65632e89c5f4: Pull complete 
cd75fa32da8f: Pull complete 
15bc7736db11: Pull complete 
b2c40cef4807: Pull complete 
f3507e55e5eb: Pull complete 
e6006cdfa16b: Pull complete 
a3ed406e3c88: Pull complete 
745f1366071d: Pull complete 
bdfcada64ad8: Pull complete 
86f2b695cc77: Pull complete 
5f634a03970a: Pull complete 
a329a7ebde19: Pull complete 
fb3d2649f534: Pull complete 
8fa928654124: Pull complete 
a925e4a45888: Pull complete 
b5e827c13dc3: Pull complete 
980d732311db: Pull complete 
45de3b0df525: Pull complete 
Digest: sha256:0f18ed4fa287da8129a643125434f3f65f40f3032971aa974fd10e0b03f3a1d3
Status: Downloaded newer image for wordpress:latest
7c09b624bfca8ffd5abbf9ee141cffa97581f7aadabd1524dbd17220f37c7c64

docker@test:~$ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
7c09b624bfca        wordpress:latest    "docker-entrypoint.s…"   35 seconds ago      Up 34 seconds       0.0.0.0:8080->80/tcp   my_admin
94faa4337cfe        mysql:5.7           "docker-entrypoint.s…"   11 minutes ago      Up 11 minutes       3306/tcp               my_mysql

Try to access to http://localhost:8080/ and configure wordpress

3-create PHPMyadmin container and link with mysql

docker@test:~$ docker run --name php_admin -d --link my_mysql:db -p 8081:80 phpmyadmin/phpmyadmin
9643ef87cf338545927bca59793a6d1b2c2c1de0fe1abbff05243d109fb86aa4
docker@test:~$ docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS                            NAMES
9643ef87cf33        phpmyadmin/phpmyadmin   "/run.sh supervisord…"   11 minutes ago      Up 10 minutes       9000/tcp, 0.0.0.0:8081->80/tcp   php_admin
7c09b624bfca        wordpress:latest        "docker-entrypoint.s…"   27 minutes ago      Up 27 minutes       0.0.0.0:8080->80/tcp             my_admin
94faa4337cfe        mysql:5.7               "docker-entrypoint.s…"   38 minutes ago      Up 38 minutes       3306/tcp                         my_mysql
docker@test:~$ 

Try to access to http://localhost:8081/

**************************************************************************************************
		Lab 5 : Manage data in Docker (Volumes)
**************************************************************************************************

Docker has three options for containers to store data in the Docker Host machine :

-Volumes are stored in a part of the host filesystem which is managed by Docker (/var/lib/docker/volumes/ on Linux). 
Non-Docker processes should not modify this part of the filesystem. Volumes are the best way to persist data in Docker.

-Bind mounts may be stored anywhere on the host system. 
Non-Docker processes on the Docker host or a Docker container can modify them at any time.

-tmpfs mounts are stored in the host system’s memory only, and are never written to the host system’s filesystem.

Notes : 

-With volumes and bind mounts, the files are persisted even after the container stops (the volume still exists)
-With tmpfs mounts the data is not persisted.

-Volumes are the preferred way to persist data in Docker containers and services. Some use cases for volumes include:

--> Sharing data among multiple running containers.Multiple containers can mount the same volume simultaneously, 
either read-write or read-only. Volumes are only removed when you explicitly remove them.

--> When you want to store your container’s data on a remote host or a cloud providers (AWS, Azure, GKE), rather than locally,
to encrypt the contents of volumes, or to add other functionality.

--> When you need to back up, restore, or migrate data from one Docker host to another, volumes are a better choice. 
You can stop containers using the volume, then back up the volume’s directory (such as /var/lib/docker/volumes/<volume-name>).

--> You can manage volumes using Docker CLI commands or the Docker API.


1-Volumes
-Create and manage volumes
$ sudo docker volume create my-vol
my-vol

$ sudo docker volume ls

DRIVER              VOLUME NAME
local               my-vol
$ sudo docker volume inspect my-vol
[
    {
        "CreatedAt": "2018-10-07T19:09:57+01:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/my-vol/_data",
        "Name": "my-vol",
        "Options": {},
        "Scope": "local"
    }
]


-Start a container with a volume

For standalone containers: you can use the -v or --volume flag 
For swarm services : the --mount flag was used (only supported)
--> With Docker 17.06, you can also use --mount with standalone containers
--> New users should try --mount syntax which is simpler than --volume syntax (volume driver options)

$ sudo docker run -d --name myapache -v my-vol:/app httpd:latest
da5bd56201d49855bbc8768464ca6de5743d9d4cf039fd907bb3763bbdb154f9
OR
$ sudo docker run -d --name myapache --mount source=my-vol,target=/app httpd:latest
$ sudo docker ps
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                      NAMES
da5bd56201d4        httpd:latest               "httpd-foreground"       10 seconds ago      Up 8 seconds        80/tcp                     myapache
$ sudo docker inspect myapache
"Mounts": [
            {
                "Type": "volume",
                "Name": "my-vol",
                "Source": "/var/lib/docker/volumes/my-vol/_data",
                "Destination": "/app",
                "Driver": "local",
                "Mode": "z",
                "RW": true,
                "Propagation": ""
            }
        ],

$ ls /var/lib/docker/volumes/my-vol/_data
$ sudo docker exec myapache ls /app
$ echo "test" >/var/lib/docker/volumes/my-vol/_data/file.txt
$ sudo docker exec myapache ls /app
file.txt
$ sudo docker exec myapache cat /app/file.txt
test
$ sudo docker exec myapache touch /app/file2.txt
$ sudo ls /var/lib/docker/volumes/my-vol/_data
file2.txt  file.txt

-Share volume between 2 containers

$ sudo  docker run -d --name myapache2 -v my-vol:/app2 httpd:latest
c5fac22937f35ec0c4de101480980fb93da736fd20a4a415ec105d1686f63cee

$ ls /var/lib/docker/volumes/my-vol/_data
file2.txt  file.txt

$ sudo docker exec myapache2 ls /app2/
file.txt
file2.txt
$ sudo docker exec myapache2 touch /app2/file3.txt
$ sudo ls /var/lib/docker/volumes/my-vol/_data
file2.txt  file3.txt  file.txt


3-Using volume drivers
-When building fault-tolerant applications, you might need to configure multiple replicas 
of the same service to have access to the same files.
-You can create volumes with a driver that supports writing files to an external storage system like NFS or Amazon S3.
-Volume drivers allow you to abstract the underlying storage system from the application logic
(you can update the services to use a different driver without changing the application logic), for ex from NFS to AWS S3.

**************************************************************************************************
		Lab 6 : Manage Network
**************************************************************************************************
Docker Network driver:
-User-defined bridge networks are best when you need multiple containers to communicate on the same Docker host.
-Overlay networks are best when you need containers running on different Docker hosts to communicate, 
or when multiple applications work together using swarm services.
-Third-party network plugins allow you to integrate Docker with specialized network stacks.
-Host networks are best when the network stack should not be isolated from the Docker host, 
but you want other aspects of the container to be isolated.
-Macvlan networks are best when you are migrating from a VM setup or need your containers
 to look like physical hosts on your network, each with a unique MAC address.

Note:
 
At the start of the daemon docker:
-Create the bridge ”docker0” : a private IP/MAC address are assigned to
bridge + configuration of configure routing tables (route and iptables)

For each container Docker creates two pairs of interfaces (both connected to the bridge and work as a pipe) :
-one in the container (eth *) another in the host machine (veth *)
-generation of an IP address and a MAC address for the container
-default route configuration in the container

$ ifconfig
docker0   Link encap:Ethernet  HWaddr 02:42:d0:09:1c:af  
          inet adr:172.17.0.1  Bcast:172.17.255.255  Masque:255.255.0.0
          adr inet6: fe80::42:d0ff:fe09:1caf/64 Scope:Lien
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:10133 errors:0 dropped:0 overruns:0 frame:0
          TX packets:15613 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 lg file transmission:0 
          RX bytes:971248 (948.4 KiB)  TX bytes:18934358 (18.0 MiB)
veth05d2e96 Link encap:Ethernet  HWaddr 82:61:79:1d:ed:87  
          adr inet6: fe80::8061:79ff:fe1d:ed87/64 Scope:Lien
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:33300 errors:0 dropped:0 overruns:0 frame:0
          TX packets:50101 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 lg file transmission:0 
          RX bytes:6224993 (5.9 MiB)  TX bytes:4238267 (4.0 MiB)

1-Manage the default network driver (bridge)

$ sudo docker network ls
NETWORK ID          NAME                           DRIVER              SCOPE
8369f0287a10        bridge                         bridge              local
1a92673da24c        host                           host                local
cf432f5cb924        none                           null                local
$ sudo docker inspect myapache


2-Use user-defined bridge networks

$ sudo docker network create --driver bridge my-network

$ sudo docker network ls
NETWORK ID          NAME                           DRIVER              SCOPE
8369f0287a10        bridge                         bridge              local
1a92673da24c        host                           host                local
f002eb7ba513        my-network                     bridge              local
cf432f5cb924        none                           null                local

$ sudo docker network inspect my-network
[
    {
        "Name": "my-network",
        "Id": "f002eb7ba513d84613ef5d190cf35cca96bffc9aba2f5da6f0857195c9f3b9c3",
        "Created": "2018-10-07T22:54:31.636198276+01:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.19.0.0/16",
                    "Gateway": "172.19.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
$ sudo docker run -dit --name myapache4 --network my-network httpd:latest
$ sudo docker network inspect my-network
[
    {
        "Name": "my-network",
        "Id": "f002eb7ba513d84613ef5d190cf35cca96bffc9aba2f5da6f0857195c9f3b9c3",
        "Created": "2018-10-07T22:54:31.636198276+01:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.19.0.0/16",
                    "Gateway": "172.19.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "677cf9b68fabd160cdd76c6f6723814a9bcebaf00337795b080b7c1c320755e3": {
                "Name": "myapache4",
                "EndpointID": "7b7685f57d0143eae1af40a6e886f02d00e8a2b84af53c777491f7a542b1dfb2",
                "MacAddress": "02:42:ac:13:00:02",
                "IPv4Address": "172.19.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]

Note:
On user-defined networks like my-network, containers can not only communicate by IP address,
 but can also resolve a container name to an IP address. This capability is called automatic service discovery.
