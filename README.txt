$ docker volume create portainer_data
$ docker service create \
--name portainer \
--publish 9000:9000 \
--replicas=1 \
--constraint 'node.role == manager' \
--mount type=bind,src=//var/run/docker.sock,dst=/var/run/docker.sock \
--mount type=volume,src=portainer_data,dst=/data \
portainer/portainer \
-H unix:///var/run/docker.sock

sudo yum -y install https://releases.hashicorp.com/vagrant/2.1.4/vagrant_2.1.4_x86_64.rpm (https://releases.hashicorp.com/vagrant/2.1.4/)

sudo yum install git ansible
