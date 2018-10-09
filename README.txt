
#!/bin/bash

set -e

die () {
   echo >&2 "$@"
   exit 1
}

if [ $# -ne 3 ] 
then
	die 'worker nodes list, master node and token are required'
else
SWARM_CLUSTER_TOKEN="$3"

echo "Start creating Docker Swarm Cluster"
echo "1-Create Docker Host Master Node"
docker-machine create -d virtualbox --swarm  --swarm-discovery "token://$SWARM_CLUSTER_TOKEN" --swarm-master $2
echo "Docker HOST (Swarm Master) has been created successfully on $2"

echo "2-Create Docker Host Worker Nodes"
nb_lignes=`wc -l $1 | cut -d " " -f1`
echo $nb_lignes
for i in $(seq $nb_lignes)
do
machine=`head $1 -n $i | tail -1`
docker-machine create -d virtualbox --swarm  --swarm-discovery "token://$SWARM_CLUSTER_TOKEN"   $machine
echo "Docker HOST (Swarm Worker) has been created successfully on $machine"
done
echo "Docker Swarm Cluster has been created successfully"
fi

wget https://www-lipn.univ-paris13.fr/~saad/Docker-Swarm.zip
wget http://www-lipn.univ-paris13.fr/~saad/docker-machine-tuto.zip
