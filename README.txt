**************************************************************************************************
		Lab 11 : OpenShift Container Platform de Red Hat OCP (PaaS based on Kubernetes)
**************************************************************************************************

1-Install dependencies
-Git
-VirtualBox (with host only network)
-Docker-machine tooling
-OC client tools https://github.com/openshift/origin/releases
-Ansible-playbook tooling : sudo apt-get install ansible -->https://docs.ansible.com/ansible/2.5/installation_guide/intro_installation.html

walid@walidos:~/ocp-install-demo$ oc version
oc v3.9.0-alpha.3+78ddc10
kubernetes v1.9.1+a0ce1bc657
features: Basic-Auth GSSAPI Kerberos SPNEGO
Unable to connect to the server: dial tcp 192.168.99.100:8443: getsockopt: no route to host
walid@walidos:~/ocp-install-demo$

2-Clone OCP from github

git clone https://github.com/walidsaad/ocp-install-demo.git

3-Set up the oc verion in init.sh file

4-Install OCP

walid@walidos:~/ocp-install-demo$ ./init_ocp39.sh 
[3;J

##############################################################
##                                                          ##
##  Setting up your very own                                ##
##                                                          ##
##    ###  ####  ##### #   #  #### #   # ##### ##### #####  ##
##   #   # #   # #     ##  # #     #   #   #   #       #    ##
##   #   # ####  ###   # # #  ###  #####   #   ####    #    ##
##   #   # #     #     #  ##     # #   #   #   #       #    ##
##    ###  #     ##### #   # ####  #   # ##### #       #    ##
##                                                          ##
##    ####  ###  #   # #####  ###  ##### #   # ##### #####  ##
##   #     #   # ##  #   #   #   #   #   ##  # #     #   #  ##
##   #     #   # # # #   #   #####   #   # # # ###   #####  ##
##   #     #   # #  ##   #   #   #   #   #  ## #     #  #   ##
##    ####  ###  #   #   #   #   # ##### #   # ##### #   #  ##
##                                                          ##
##      ####  #      ###  ##### #####  ###  ##### #   #     ##
##      #   # #     #   #   #   #     #   # #   # ## ##     ##
##      ####  #     #####   #   ####  #   # ##### # # #     ##
##      #     #     #   #   #   #     #   # #  #  #   #     ##
##      #     ##### #   #   #   #      ###  #   # #   #     ##
##                                                          ##
##  https://github.com/redhatdemocentral/ocp-install-demo   ##
##                                                          ##
##############################################################

You are running on Linux.
This script assumes you are going to use KVM on Linux.
You'll need to install docker-machine and docker-machine-driver-kvm in your $PATH manually.
Download them from https://github.com/docker/machine/releases and https://github.com/dhiltgen/docker-machine-kvm/releases, respectively.
Docker-machine tooling installed...

OpenShift command line tools installed... checking for valid version...

Setting up OpenShift docker machine using virtualbox...

Host already exists: "openshift"

Error occurred during openshift docker machine creation...

Cleaning out existing 'openshift' machine...

About to remove openshift
WARNING: This action will delete both local reference and remote instance.
Successfully removed openshift
Setting up OpenShift docker machine using virtualbox...

Running pre-create checks...
Creating machine...
(openshift) Downloading /home/walid/.docker/machine/cache/boot2docker.iso from https://github.com/boot2docker/boot2docker/releases/download/v18.01.0-ce/boot2docker.iso...
(openshift) 0%....10%....20%....30%....40%....50%....60%....70%....80%....90%....100%
(openshift) Creating VirtualBox VM...
(openshift) Creating SSH key...
(openshift) Starting the VM...
(openshift) Check network to re-create if needed...
(openshift) Waiting for an IP...
Waiting for machine to be running, this may take a few minutes...
Detecting operating system of created instance...
Waiting for SSH to be available...
Detecting the provisioner...
Provisioning with boot2docker...
Copying certs to the local machine directory...
Copying certs to the remote machine...
Setting Docker configuration on the remote daemon...
Checking connection to Docker...
Docker is up and running!
To see how to connect your Docker Client to the Docker Engine running on this virtual machine, run: docker-machine env openshift
Installing OCP with cluster up...

Starting OpenShift using registry.access.redhat.com/openshift3/ose:v3.7 ...
Pulling image registry.access.redhat.com/openshift3/ose:v3.7
Pulled 1/4 layers, 26% complete
Pulled 1/4 layers, 30% complete
Pulled 1/4 layers, 32% complete
Pulled 1/4 layers, 35% complete
Pulled 1/4 layers, 39% complete
Pulled 1/4 layers, 44% complete
Pulled 1/4 layers, 47% complete
Pulled 1/4 layers, 50% complete
Pulled 1/4 layers, 55% complete
Pulled 1/4 layers, 61% complete
Pulled 1/4 layers, 67% complete
Pulled 1/4 layers, 73% complete
Pulled 2/4 layers, 82% complete
Pulled 3/4 layers, 87% complete
Pulled 3/4 layers, 90% complete
Pulled 3/4 layers, 95% complete
Pulled 3/4 layers, 99% complete
Pulled 4/4 layers, 100% complete
Extracting
Image pull complete
OpenShift server started.

The server is accessible via web console at:
    https://192.168.99.100:8443

You are logged in as:
    User:     developer
    Password: <any value>

To login as administrator:
    oc login -u system:admin


Logging in as admin user...

Logged into "https://192.168.99.100:8443" as "system:admin" using existing credentials.

You have access to the following projects and can switch between them with 'oc project <projectname>':

    default
    kube-public
    kube-system
  * myproject
    openshift
    openshift-infra
    openshift-node

Using project "myproject".

Set OCP IP to https://192.168.99.100:8443...

Granting admin user full cluster-admin rights...

cluster role "cluster-admin" added: "admin"

Granting rights to service catalog access...

cluster role "system:openshift:templateservicebroker-client" added: ["system:unauthenticated" "system:authenticated"]

Updating JBoss image streams...

imagestream "jboss-webserver30-tomcat7-openshift" created
imagestream "jboss-webserver30-tomcat8-openshift" created
imagestream "jboss-webserver31-tomcat7-openshift" created
imagestream "jboss-webserver31-tomcat8-openshift" created
imagestream "jboss-eap64-openshift" created
imagestream "jboss-eap70-openshift" created
imagestream "jboss-eap71-openshift" created
imagestream "jboss-decisionserver62-openshift" created
imagestream "jboss-decisionserver63-openshift" created
imagestream "jboss-decisionserver64-openshift" created
imagestream "jboss-processserver63-openshift" created
imagestream "jboss-processserver64-openshift" created
imagestream "jboss-datagrid65-openshift" created
imagestream "jboss-datagrid71-openshift" created
imagestream "jboss-datagrid65-client-openshift" created
imagestream "jboss-datagrid71-client-openshift" created
imagestream "jboss-datavirt63-openshift" created
imagestream "jboss-datavirt63-driver-openshift" created
imagestream "jboss-amq-62" created
imagestream "jboss-amq-63" created
imagestream "redhat-sso70-openshift" created
imagestream "redhat-sso71-openshift" created
imagestream "redhat-openjdk18-openshift" created

Updating Fuse image streams...

imagestream "fis-java-openshift" created
imagestream "fis-karaf-openshift" created

Updating EAP templates...

template "eap70-basic-s2i" created

Updating Decision Server templates...

template "decisionserver63-basic-s2i" created
template "decisionserver64-basic-s2i" created

Updating Process Server templates...

template "processserver64-postgresql-s2i" created
template "processserver64-postgresql-persistent-s2i" created

Updating RHEL 7 image streams...

imagestream "httpd" created
imagestream "ruby" created
imagestream "nodejs" created
imagestream "perl" created
imagestream "php" created
imagestream "python" created
imagestream "mysql" created
imagestream "mariadb" created
imagestream "postgresql" created
imagestream "mongodb" created
imagestream "redis" created
imagestream "jenkins" created

Update .Net image streams...

imagestream "dotnet" created
imagestream "dotnet-runtime" created

====================================================
=                                                  =
= Install complete, get ready to rock your Cloud.  =
= Look for information at end of OCP install.      =
=                                                  =
=  The server is accessible via web console at:    =
=                                                  =
=	  https://192.168.99.100:8443              =
=                                                  =
=  Log in as user: openshift-dev                   =
=        password: devel                           =
=                                                  =
=  Admin log in as: admin                          =
=         password: admin                          =
=                                                  =
=  Now get your Red Hat Demo Central example       =
=  projects here:                                  =
=                                                  =
=     https://github.com/redhatdemocentral         =
=                                                  =
=  To stop and restart your OCP cluster with       =
=  installed containers, see Readme.md in the      =
=  NOTES section for details.                      =
=                                                  =
=  When finished, clean up your demo with:         =
=                                                  =
=     $ docker-machine rm -f openshift             =
=                                                  =
====================================================
walid@walidos:~/ocp-install-demo$ 

5-Deploy application using OCP web console
-Create web server application.



6- shutdown using:
walid@walidos:~/ocp-install-demo$ oc cluster down --docker-machine=openshift
walid@walidos:~/ocp-install-demo$ docker-machine stop openshift

7-restart use the existing data and configuration:

walid@walidos:~/ocp-install-demo$ docker-machine start openshift
Starting "openshift"...
(openshift) Check network to re-create if needed...
(openshift) Waiting for an IP...
Machine "openshift" was started.
Waiting for SSH to be available...
Detecting the provisioner...
Started machines may have new IP addresses. You may need to re-run the `docker-machine env` command.
walid@walidos:~/ocp-install-demo$ docker-machine env openshift
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/home/walid/.docker/machine/machines/openshift"
export DOCKER_MACHINE_NAME="openshift"
# Run this command to configure your shell: 
# eval $(docker-machine env openshift)
walid@walidos:~/ocp-install-demo$ docker-machine ssh openshift
                        ##         .
                  ## ## ##        ==
               ## ## ## ## ##    ===
           /"""""""""""""""""\___/ ===
      ~~~ {~~ ~~~~ ~~~ ~~~~ ~~~ ~ /  ===- ~~~
           \______ o           __/
             \    \         __/
              \____\_______/
 _                 _   ____     _            _
| |__   ___   ___ | |_|___ \ __| | ___   ___| | _____ _ __
| '_ \ / _ \ / _ \| __| __) / _` |/ _ \ / __| |/ / _ \ '__|
| |_) | (_) | (_) | |_ / __/ (_| | (_) | (__|   <  __/ |
|_.__/ \___/ \___/ \__|_____\__,_|\___/ \___|_|\_\___|_|
Boot2Docker version 18.01.0-ce, build HEAD : 0bb7bbd - Thu Jan 11 16:32:39 UTC 2018
Docker version 18.01.0-ce, build 03596f5
docker@openshift:~$ exit
walid@walidos:~/ocp-install-demo$ oc cluster up --image=registry.access.redhat.com/openshift3/ose --host-data-dir=/var/lib/boot2docker/ocp-data --docker-machine=openshift --host-config-dir=/var/lib/boot2docker/ocp-config --use-existing-config=true --version=v3.9 --host-pv-dir=/var/lib/boot2docker/ocp-pv
Starting OpenShift using registry.access.redhat.com/openshift3/ose:v3.9 ...
OpenShift server started.

The server is accessible via web console at:
    https://192.168.99.100:8443

walid@walidos:~/ocp-install-demo$ 

8-Create Sample Application from Catalog
-Apache HTTP Server (httpd)
-Node.js
-Sacleout application

9-CI/CD Adopting a DevOps approach by using OCP Continuous Integration and Delivery
9.1-Create Jenkins instance (persistent)
9.2-Create Maven Pipeline Job with Jenkins instance (https://github.com/walidsaad/maven-example.git), Maven Slave
pipeline {
  /* Which container to bring up for the build. Pick one of the templates configured in Kubernetes plugin. */
  agent {
    label 'maven'
  }

  stages {
    stage('Pull Source') {
      steps {
        git url: 'https://github.com/walidsaad/maven-example.git', branch: 'master'
      }
    }
    stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
    }
    stage('Unit Tests') {
      steps {
        sh 'mvn test'
      }
    }
    stage('Deliver') {
            steps {
                
                sh 'java -jar target/training-app-1.1-SNAPSHOT.jar'
            }
        }
}
}
-Deploy and monitor the job

9.3-Use OCP DevOps CI/CD by deploying an OCP Pipeline Application (NodeJS+MongoDB App), this example shows how manage CI/DI pipeline from OCP
-Add to Project, Browse Catalog, select Pipeline Build Example
OR add the sample pipeline template if no exist in catalog  $ oc create -f https://raw.githubusercontent.com/openshift/origin/master/examples/jenkins/pipeline/samplepipeline.yaml

walid@walidos:~$ oc project myproject
Now using project "myproject" on server "https://192.168.99.100:8443".
walid@walidos:~$ oc get pods
NAME                             READY     STATUS      RESTARTS   AGE
jenkins-2-l49db                  1/1       Running     0          11h
mongodb-1-x2bnp                  1/1       Running     0          42m
myapp-1-build                    0/1       Completed   0          14h
nodejs-mongodb-example-1-build   0/1       Completed   0          33m
nodejs-mongodb-example-1-fhfdq   1/1       Running     0          27m
test-1-build                     0/1       Completed   0          14h
walid@walidos:~$ 



9.4-Creating CI/CD Toolchains with Multiproject Pipeline (Advanced)
https://github.com/siamaksade/openshift-cd-demo

