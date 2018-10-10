**************************************************************************************************
		Lab 10 : Kubernetes
**************************************************************************************************
1-Install dependencies :

-For ubuntu:
sudo apt-get update
wget https://releases.hashicorp.com/vagrant/2.1.4/vagrant_2.1.4_x86_64.deb
dpkg -i vagrant_2.1.4_x86_64.deb
apt-get install python2.7 python2.7-dev python-pip
pip install ansible
-For Centos :
sudo yum -y install https://releases.hashicorp.com/vagrant/2.1.4/vagrant_2.1.4_x86_64.rpm (https://releases.hashicorp.com/vagrant/2.1.4/)
sudo yum install git ansible

2-Download Git Repository (Vagrant+Ansible)
$git clone https://github.com/walidsaad/Mosaico3.git

3-Prepare the Cluster with Vagrant

$ cd Mosaico3/servers/
$ vagrant up
Bringing machine 'kmaster' up with 'virtualbox' provider...
Bringing machine 'knode1' up with 'virtualbox' provider...
........................
........................

4-Add SSH Config
walid@walidos:~/Mosaico3/servers/vbox$ vagrant ssh-config >>~/.ssh/config
walid@walidos:~/Mosaico3/servers/vbox$ chmod 600 ~/.ssh/config

5-Eidt the /etc/hosts file
walid@walidos:~/Mosaico3/servers/vbox$ sudo vim /etc/hosts
127.0.0.1 master node1 node2
 
6-Install Kubernestes with Ansible

walid@walidos:~/Mosaico3/servers/vbox$ cd ../../kubernetes
grid02@grid02:~/Mosaico3/kubernetes$ ansible-playbook site.yml
.....................................
.....................................
walid@walidos:~/Mosaico3/servers/vbox$
walid@walidos:~/Mosaico3/servers/vbox$ vagrant ssh master
vagrant@master:~$ sudo kubeadm init --apiserver-advertise-address=10.0.0.10 --pod-network-cidr=10.244.0.0/16
[init] using Kubernetes version: v1.11.3
[preflight] running pre-flight checks
I0911 00:12:09.309413   10001 kernel_validator.go:81] Validating kernel version
I0911 00:12:09.309659   10001 kernel_validator.go:96] Validating kernel config
[preflight/images] Pulling images required for setting up a Kubernetes cluster
[preflight/images] This might take a minute or two, depending on the speed of your internet connection
[preflight/images] You can also perform this action in beforehand using 'kubeadm config images pull'
^[[A[kubelet] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[preflight] Activating the kubelet service
[certificates] Generated ca certificate and key.
[certificates] Generated apiserver certificate and key.
[certificates] apiserver serving cert is signed for DNS names [master kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 10.0.0.10]
[certificates] Generated apiserver-kubelet-client certificate and key.
[certificates] Generated sa key and public key.
[certificates] Generated front-proxy-ca certificate and key.
[certificates] Generated front-proxy-client certificate and key.
[certificates] Generated etcd/ca certificate and key.
[certificates] Generated etcd/server certificate and key.
[certificates] etcd/server serving cert is signed for DNS names [master localhost] and IPs [127.0.0.1 ::1]
[certificates] Generated etcd/peer certificate and key.
[certificates] etcd/peer serving cert is signed for DNS names [master localhost] and IPs [10.0.0.10 127.0.0.1 ::1]
[certificates] Generated etcd/healthcheck-client certificate and key.
[certificates] Generated apiserver-etcd-client certificate and key.
[certificates] valid certificates and keys now exist in "/etc/kubernetes/pki"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/admin.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/kubelet.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/controller-manager.conf"
[kubeconfig] Wrote KubeConfig file to disk: "/etc/kubernetes/scheduler.conf"
[controlplane] wrote Static Pod manifest for component kube-apiserver to "/etc/kubernetes/manifests/kube-apiserver.yaml"
[controlplane] wrote Static Pod manifest for component kube-controller-manager to "/etc/kubernetes/manifests/kube-controller-manager.yaml"
[controlplane] wrote Static Pod manifest for component kube-scheduler to "/etc/kubernetes/manifests/kube-scheduler.yaml"
[etcd] Wrote Static Pod manifest for a local etcd instance to "/etc/kubernetes/manifests/etcd.yaml"
[init] waiting for the kubelet to boot up the control plane as Static Pods from directory "/etc/kubernetes/manifests" 
[init] this might take a minute or longer if the control plane images have to be pulled
[apiclient] All control plane components are healthy after 47.004386 seconds
[uploadconfig] storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.11" in namespace kube-system with the configuration for the kubelets in the cluster
[markmaster] Marking the node master as master by adding the label "node-role.kubernetes.io/master=''"
[markmaster] Marking the node master as master by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[patchnode] Uploading the CRI Socket information "/var/run/dockershim.sock" to the Node API object "master" as an annotation
[bootstraptoken] using token: c5f5s8.k002ruynz8sqgzmi
[bootstraptoken] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstraptoken] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstraptoken] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstraptoken] creating the "cluster-info" ConfigMap in the "kube-public" namespace
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes master has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of machines by running the following on each node
as root:

  kubeadm join 10.0.0.10:6443 --token c5f5s8.k002ruynz8sqgzmi --discovery-token-ca-cert-hash sha256:a6930447a15667ccd93c0a6ae3cd4a25637f6f1f5e6039e339abb83513a2924a

7-Configure Master (Admin Conf and Network)
vagrant@master:~$ mkdir -p $HOME/.kube && sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config && sudo chown $(id -u):$(id -g) $HOME/.kube/config

https://www.weave.works/docs/net/latest/kubernetes/kube-addon/
vagrant@master:~$ kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

7-Join Nodes to Master

vagrant@node1:~$ sudo kubeadm join 10.0.0.10:6443 --token c5f5s8.k002ruynz8sqgzmi --discovery-token-ca-cert-hash sha256:a6930447a15667ccd93c0a6ae3cd4a25637f6f1f5e6039e339abb83513a2924a
[preflight] running pre-flight checks
I0911 00:32:22.905741   10595 kernel_validator.go:81] Validating kernel version
I0911 00:32:22.906179   10595 kernel_validator.go:96] Validating kernel config
[discovery] Trying to connect to API Server "10.0.0.10:6443"
[discovery] Created cluster-info discovery client, requesting info from "https://10.0.0.10:6443"
[discovery] Requesting info from "https://10.0.0.10:6443" again to validate TLS against the pinned public key
[discovery] Cluster info signature and contents are valid and TLS certificate validates against pinned roots, will use API Server "10.0.0.10:6443"
[discovery] Successfully established connection with API Server "10.0.0.10:6443"
[kubelet] Downloading configuration for the kubelet from the "kubelet-config-1.11" ConfigMap in the kube-system namespace
[kubelet] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[preflight] Activating the kubelet service
[tlsbootstrap] Waiting for the kubelet to perform the TLS Bootstrap...
[patchnode] Uploading the CRI Socket information "/var/run/dockershim.sock" to the Node API object "node1" as an annotation

This node has joined the cluster:
* Certificate signing request was sent to master and a response
  was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the master to see this node join the cluster.
vagrant@node1:~$ 
vagrant@master:~$ kubectl get nodes
NAME      STATUS    ROLES     AGE       VERSION
master    Ready     master    2d        v1.11.2
node1     Ready     <none>    2d        v1.11.2
node2     Ready     <none>    2d        v1.11.2

8-Create the first Pod

vagrant@master:~$ vim pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox
    command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 360']
vagrant@master:~$ kubectl create -f pod.yaml 
pod/myapp-pod created
vagrant@master:~$ 

vagrant@master:~$ kubectl get pods -o wide
NAME        READY     STATUS             RESTARTS   AGE       IP          NODE      NOMINATED NODE
myapp-pod   0/1       ImagePullBackOff   0          9h        10.32.0.4   node2     <none>

vagrant@master:~$ kubectl logs myapp-pod
Hello Kubernetes!
vagrant@master:~$ kubectl exec myapp-pod echo bonjour
bonjour
vagrant@master:~$ kubectl exec myapp-pod env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=myapp-pod
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
HOME=/root

vagrant@master:~$ kubectl exec myapp-pod ping www.google.com
PING www.google.com (172.217.23.68): 56 data bytes
64 bytes from 172.217.23.68: seq=0 ttl=61 time=23.082 ms
64 bytes from 172.217.23.68: seq=1 ttl=61 time=23.410 ms
^C
vagrant@master:~$

vagrant@master:~$ kubectl get pods -o wide
NAME        READY     STATUS    RESTARTS   AGE       IP          NODE      NOMINATED NODE
myapp-pod   1/1       Running   44         15h       10.32.0.4   node2     <none>

9-Create Pods with Deployment Object

vagrant@master:~$ kubectl run kubernetes-bootcamp --image=gcr.io/google-samples/kubernetes-bootcamp:v1 --port=8080
deployment.apps/kubernetes-bootcamp created
vagrant@master:~$ kubectl get pods -o wide
NAME                                  READY     STATUS              RESTARTS   AGE       IP          NODE      NOMINATED NODE
kubernetes-bootcamp-69bf88c8c-5x9x9   0/1       ContainerCreating   0          4s        <none>      node1     <none>
myapp-pod                             1/1       Running             44         15h       10.32.0.4   node2     <none>

vagrant@master:~$ kubectl get deployments
NAME                  DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1         1         1            1           2m

10-Create Services and expose deployment

vagrant@master:~$ kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   2d
vagrant@master:~$ kubectl get deployments
NAME                  DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1         1         1            1           3h
vagrant@master:~$ kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080
service/kubernetes-bootcamp exposed
vagrant@master:~$ kubectl get services
NAME                  TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes            ClusterIP   10.96.0.1      <none>        443/TCP          2d
kubernetes-bootcamp   NodePort    10.106.4.223   <none>        8080:32156/TCP   7s
vagrant@master:~$ kubectl describe services/kubernetes-bootcamp
Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   run=kubernetes-bootcamp
Annotations:              <none>
Selector:                 run=kubernetes-bootcamp
Type:                     NodePort
IP:                       10.106.4.223
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  32156/TCP
Endpoints:                10.40.0.1:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
vagrant@master:~$ export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
vagrant@master:~$ ifconfig 
My IP is 10.0.0.10
vagrant@master:~$ curl 10.0.0.10:$NODE_PORT
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-69bf88c8c-5x9x9 | v=1
vagrant@master:~


11-Scaling an application

vagrant@master:~$ kubectl get deployment
NAME                  DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   1         1         1            1           7h
vagrant@master:~$ kubectl scale deployments/kubernetes-bootcamp --replicas=3
deployment.extensions/kubernetes-bootcamp scaled
vagrant@master:~$ kubectl get deployment
NAME                  DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
kubernetes-bootcamp   3         3         3            2           7h

The DESIRED state is showing the configured number of replicas

The CURRENT state show how many replicas are running now

The UP-TO-DATE is the number of replicas that were updated to match the desired (configured) state

The AVAILABLE state shows how many replicas are actually AVAILABLE to the users


vagrant@master:~$ kubectl get pods
NAME                                  READY     STATUS              RESTARTS   AGE
kubernetes-bootcamp-69bf88c8c-5x9x9   1/1       Running             0          7h
kubernetes-bootcamp-69bf88c8c-k9jl7   1/1       Running             0          11s
kubernetes-bootcamp-69bf88c8c-qsg95   0/1       ContainerCreating   0          11s

vagrant@master:~$ kubectl get pods
NAME                                  READY     STATUS              RESTARTS   AGE
kubernetes-bootcamp-69bf88c8c-5x9x9   1/1       Running             0          7h
kubernetes-bootcamp-69bf88c8c-k9jl7   1/1       Running             0          15s
kubernetes-bootcamp-69bf88c8c-qsg95   0/1       ContainerCreating   0          15s

vagrant@master:~$ kubectl get pods -o wide
NAME                                  READY     STATUS             RESTARTS   AGE       IP          NODE      NOMINATED NODE
kubernetes-bootcamp-69bf88c8c-5x9x9   1/1       Running            0          7h        10.40.0.1   node1     <none>
kubernetes-bootcamp-69bf88c8c-k9jl7   1/1       Running            0          27s       10.40.0.2   node1     <none>
kubernetes-bootcamp-69bf88c8c-qsg95   1/1       Running            0          27s       10.32.0.5   node2     <none>

vagrant@master:~$ kubectl describe services/kubernetes-bootcamp
Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   run=kubernetes-bootcamp
Annotations:              <none>
Selector:                 run=kubernetes-bootcamp
Type:                     NodePort
IP:                       10.106.4.223
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  32156/TCP
Endpoints:                10.32.0.5:8080,10.40.0.1:8080,10.40.0.2:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

vagrant@master:~$ export NODE_PORT=$(kubectl get services/kubernetes-bootcamp -o go-template='{{(index .spec.ports 0).nodePort}}')
vagrant@master:~$ curl 10.0.0.10:$NODE_PORT
Hello Kubernetes bootcamp! | Running on: kubernetes-bootcamp-69bf88c8c-k9jl7 | v=1



12-Using Labels

vagrant@master:~$ kubectl get pods -l app=myapp
NAME        READY     STATUS    RESTARTS   AGE
myapp-pod   1/1       Running   103        23h
vagrant@master:~$ kubectl get pods -l run=kubernetes-bootcamp
NAME                                  READY     STATUS    RESTARTS   AGE
kubernetes-bootcamp-69bf88c8c-5x9x9   1/1       Running   0          7h
kubernetes-bootcamp-69bf88c8c-k9jl7   1/1       Running   0          13m
kubernetes-bootcamp-69bf88c8c-qsg95   1/1       Running   0          13m
vagrant@master:~$ 



13-Delete Pods
vagrant@master:~$ kubectl delete pods kubernetes-bootcamp-69bf88c8c-5x9x9
pod "kubernetes-bootcamp-69bf88c8c-5x9x9" deleted
-->Aussitôt supprimé, aussitôt recréé ! 

vagrant@master:~$ kubectl get pods -o wide
NAME                                  READY     STATUS        RESTARTS   AGE       IP          NODE      NOMINATED NODE
kubernetes-bootcamp-69bf88c8c-5x9x9   0/1       Terminating   0          7h        <none>      node1     <none>
kubernetes-bootcamp-69bf88c8c-jkv7s   1/1       Running       0          34s       10.40.0.3   node1     <none>
kubernetes-bootcamp-69bf88c8c-k9jl7   1/1       Running       0          32m       10.40.0.2   node1     <none>
kubernetes-bootcamp-69bf88c8c-qsg95   1/1       Running       0          32m       10.32.0.5   node2     <none>
myapp-pod                             1/1       Running       106        23h       10.32.0.4   node2     <none>
vagrant@master:~$ kubectl get pods -o wide
NAME                                  READY     STATUS    RESTARTS   AGE       IP          NODE      NOMINATED NODE
kubernetes-bootcamp-69bf88c8c-jkv7s   1/1       Running   0          45s       10.40.0.3   node1     <none>
kubernetes-bootcamp-69bf88c8c-k9jl7   1/1       Running   0          32m       10.40.0.2   node1     <none>
kubernetes-bootcamp-69bf88c8c-qsg95   1/1       Running   0          32m       10.32.0.5   node2     <none>
myapp-pod                             1/1       Running   106        23h       10.32.0.4   node2     <none>
vagrant@master:~$

14-Performing a Rolling Update

-Update the version of the app (V1 to V2)

vagrant@master:~$ kubectl set image deployments/kubernetes-bootcamp kubernetes-bootcamp=jocatalin/kubernetes-bootcamp:v2
deployment.extensions/kubernetes-bootcamp image updated
vagrant@master:~$ kubectl get pods
NAME                                  READY     STATUS              RESTARTS   AGE
kubernetes-bootcamp-69bf88c8c-jkv7s   1/1       Running             0          27m
kubernetes-bootcamp-69bf88c8c-k9jl7   1/1       Running             0          58m
kubernetes-bootcamp-69bf88c8c-qsg95   1/1       Running             0          58m
kubernetes-bootcamp-b9cdd8865-r55qn   0/1       ContainerCreating   0          3s
myapp-pod                             1/1       Running             109        1d
vagrant@master:~$ kubectl get pods
NAME                                  READY     STATUS              RESTARTS   AGE
kubernetes-bootcamp-69bf88c8c-jkv7s   1/1       Terminating         0          27m
kubernetes-bootcamp-69bf88c8c-k9jl7   1/1       Running             0          58m
kubernetes-bootcamp-69bf88c8c-qsg95   1/1       Running             0          58m
kubernetes-bootcamp-b9cdd8865-bzqdf   0/1       ContainerCreating   0          0s
kubernetes-bootcamp-b9cdd8865-r55qn   1/1       Running             0          6s
myapp-pod                             1/1       Running             109        1d

vagrant@master:~$ kubectl get pods
NAME                                  READY     STATUS        RESTARTS   AGE
kubernetes-bootcamp-69bf88c8c-jkv7s   1/1       Terminating   0          27m
kubernetes-bootcamp-69bf88c8c-k9jl7   1/1       Terminating   0          59m
kubernetes-bootcamp-69bf88c8c-qsg95   1/1       Terminating   0          59m
kubernetes-bootcamp-b9cdd8865-bzqdf   1/1       Running       0          18s
kubernetes-bootcamp-b9cdd8865-jrkdm   1/1       Running       0          7s
kubernetes-bootcamp-b9cdd8865-r55qn   1/1       Running       0          24s
myapp-pod                             1/1       Running       109        1d

vagrant@master:~$ kubectl get pods
NAME                                  READY     STATUS        RESTARTS   AGE
kubernetes-bootcamp-69bf88c8c-jkv7s   0/1       Terminating   0          27m
kubernetes-bootcamp-69bf88c8c-k9jl7   1/1       Terminating   0          59m
kubernetes-bootcamp-69bf88c8c-qsg95   1/1       Terminating   0          59m
kubernetes-bootcamp-b9cdd8865-bzqdf   1/1       Running       0          34s
kubernetes-bootcamp-b9cdd8865-jrkdm   1/1       Running       0          23s
kubernetes-bootcamp-b9cdd8865-r55qn   1/1       Running       0          40s
myapp-pod                             1/1       Running       109        1d

vagrant@master:~$ kubectl get pods
NAME                                  READY     STATUS    RESTARTS   AGE
kubernetes-bootcamp-b9cdd8865-bzqdf   1/1       Running   0          51s
kubernetes-bootcamp-b9cdd8865-jrkdm   1/1       Running   0          40s
kubernetes-bootcamp-b9cdd8865-r55qn   1/1       Running   0          57s
myapp-pod                             1/1       Running   109        1d
vagrant@master:~$ 

-Verify an update (V1 to V2)
vagrant@master:~$ kubectl describe services/kubernetes-bootcamp
Name:                     kubernetes-bootcamp
Namespace:                default
Labels:                   run=kubernetes-bootcamp
Annotations:              <none>
Selector:                 run=kubernetes-bootcamp
Type:                     NodePort
IP:                       10.106.4.223
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  32156/TCP
Endpoints:                10.32.0.6:8080,10.32.0.7:8080,10.40.0.1:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

vagrant@master:~$ kubectl describe pods

-Rollback an update (V2 to V1)
vagrant@master:~$ kubectl rollout undo deployments/kubernetes-bootcamp
deployment.extensions/kubernetes-bootcamp
vagrant@master:~$ kubectl get pods -o wide
NAME                                  READY     STATUS             RESTARTS   AGE       IP          NODE      NOMINATED NODE
kubernetes-bootcamp-69bf88c8c-gcl6p   1/1       Running            0          7s        10.32.0.5   node2     <none>
kubernetes-bootcamp-69bf88c8c-wnzkx   1/1       Running            0          5s        10.40.0.3   node1     <none>
kubernetes-bootcamp-69bf88c8c-xg5xz   1/1       Running            0          8s        10.40.0.2   node1     <none>
kubernetes-bootcamp-b9cdd8865-bzqdf   1/1       Terminating        0          15m       10.40.0.1   node1     <none>
kubernetes-bootcamp-b9cdd8865-jrkdm   1/1       Terminating        0          15m       10.32.0.7   node2     <none>
kubernetes-bootcamp-b9cdd8865-r55qn   1/1       Terminating        0          15m       10.32.0.6   node2     <none>

agrant@master:~$ kubectl get pods -o wide
NAME                                  READY     STATUS    RESTARTS   AGE       IP          NODE      NOMINATED NODE
kubernetes-bootcamp-69bf88c8c-gcl6p   1/1       Running   0          1m        10.32.0.5   node2     <none>
kubernetes-bootcamp-69bf88c8c-wnzkx   1/1       Running   0          1m        10.40.0.3   node1     <none>
kubernetes-bootcamp-69bf88c8c-xg5xz   1/1       Running   0          1m        10.40.0.2   node1     <none>
myapp-pod                             1/1       Running   112        1d        10.32.0.4   node2     <none>

vagrant@master:~$ kubectl describe pods -l run=kubernetes-bootcamp
Name:               kubernetes-bootcamp-69bf88c8c-gcl6p
Namespace:          default
Priority:           0
PriorityClassName:  <none>
Node:               node2/10.0.0.12
Start Time:         Fri, 14 Sep 2018 01:04:31 +0000
Labels:             pod-template-hash=256944747
                    run=kubernetes-bootcamp
Annotations:        <none>
Status:             Running
IP:                 10.32.0.5
Controlled By:      ReplicaSet/kubernetes-bootcamp-69bf88c8c
Containers:
  kubernetes-bootcamp:
    Container ID:   docker://571fa4c690eeed4139c10dbb0321cd4921d7605ae2f89862f5050a0acc5bb955
    Image:          gcr.io/google-samples/kubernetes-bootcamp:v1
    Image ID:       docker-pullable://gcr.io/google-samples/kubernetes-bootcamp@sha256:0d6b8ee63bb57c5f5b6156f446b3bc3b3c143d233037f3a2f00e279c8fcc64af
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Fri, 14 Sep 2018 01:04:32 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-6dtkp (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-6dtkp:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-6dtkp
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  4m    default-scheduler  Successfully assigned default/kubernetes-bootcamp-69bf88c8c-gcl6p to node2
  Normal  Pulled     4m    kubelet, node2     Container image "gcr.io/google-samples/kubernetes-bootcamp:v1" already present on machine
  Normal  Created    4m    kubelet, node2     Created container
  Normal  Started    4m    kubelet, node2     Started container





15-Working with Namespaces

vagrant@master:~$ kubectl create namespace dev
namespace/dev created
vagrant@master:~$ kubectl get namespaces
NAME          STATUS    AGE
default       Active    3d
dev           Active    12s
kube-public   Active    3d
kube-system   Active    3d
vagrant@master:~$ 

vagrant@master:~$ vim nginx.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80

vagrant@master:~$ kubectl create -f nginx.yaml -n dev
deployment.apps/nginx-deployment created
vagrant@master:~$ kubectl get pods -o wide
NAME                                  READY     STATUS    RESTARTS   AGE       IP          NODE      NOMINATED NODE
kubernetes-bootcamp-69bf88c8c-gcl6p   1/1       Running   0          15m       10.32.0.5   node2     <none>
kubernetes-bootcamp-69bf88c8c-wnzkx   1/1       Running   0          15m       10.40.0.3   node1     <none>
kubernetes-bootcamp-69bf88c8c-xg5xz   1/1       Running   0          15m       10.40.0.2   node1     <none>
myapp-pod                             1/1       Running   113        1d        10.32.0.4   node2     <none>
vagrant@master:~$ kubectl get pods -o wide -n dev
NAME                                READY     STATUS    RESTARTS   AGE       IP          NODE      NOMINATED NODE
nginx-deployment-67594d6bf6-966tg   1/1       Running   0          26s       10.32.0.6   node2     <none>
nginx-deployment-67594d6bf6-ps2tb   1/1       Running   0          26s       10.40.0.1   node1     <none>
vagrant@master:~$ 

16-Working with Volume

-Use Case 1: External Storage Server (Cloud of NFS)
-Static Persistent volume claim (PVC)
(exemple  GCE, AWS EBS, or using external NFS)
-Dynamic Provisioning with StorageClasses
(exemple using NFS client provisioner or Cloud client provisioner)

-Use Case 2 : In cluster Storage Server (NFS)

16.1-Static Persistent volume claim (PVC)

---->Prerequisites
SSH in to each node and install the nfs-common OS package (e.g. sudo apt-get update && sudo apt-get -y install nfs-common)
---->Create NFS server Pod

vagrant@master:~$ cat nfs-server-rc.yaml 
kind: Service
apiVersion: v1
metadata:
  name: nfs-server
spec:
  ports:
    - name: nfs
      port: 2049
    - name: mountd
      port: 20048
    - name: rpcbind
      port: 111
  selector:
    role: nfs-server
---
apiVersion: v1
kind: ReplicationController
metadata:
  name: nfs-server
spec:
  replicas: 1
  selector:
    role: nfs-server
  template:
    metadata:
      labels:
        role: nfs-server
    spec:
      containers:
      - name: nfs-server
        image: gcr.io/google_containers/volume-nfs:0.8
        ports:
          - name: nfs
            containerPort: 2049
          - name: mountd
            containerPort: 20048
          - name: rpcbind
            containerPort: 111
        securityContext:
          privileged: true
        volumeMounts:
          - mountPath: /exports
            name: nfs-export-fast
      volumes:
        - name: nfs-export-fast
          hostPath:
            path: /data/nfs

vagrant@master:~$ kubectl create -f nfs-server-rc.yaml

vagrant@master:~$ kubectl get pods -o wide
NAME               READY     STATUS    RESTARTS   AGE       IP          NODE      NOMINATED NODE
nfs-server-nqml2   1/1       Running   0          14m       10.32.0.4   node2     <none>

- SSH into "node2", where our NFS server pod is running
vagrant@master:~$ ssh node2
vagrant@node2:~$ cat /data/nfs/index.html
Hello from NFS!
vagrant@node2:~$ exit
logout
Connection to node2 closed.




---->Create Pod using our NFS server
vagrant@master:~$ kubectl get service
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP                      5d
nfs-server   ClusterIP   10.109.32.89   <none>        2049/TCP,20048/TCP,111/TCP   17m
vagrant@master:~$ 
Consuming the Mount by creating a pod

vagrant@master:~$ cat nginx-nfs.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: web
spec:
  containers:
  - name: web
    image: nginx
    volumeMounts:
      # name must match the volume name below
    - name: nfs
      mountPath: "/usr/share/nginx/html/"
    ports:
    - name: web
      containerPort: 80
      protocol: TCP
  volumes:
  - name: nfs
    nfs:
      # FIXME: use the right name
      #server: nfs-server.default.kube.local
      server: "10.109.32.89"
      path: "/"
      readOnly: false

vagrant@master:~$ kubectl create -f nginx-nfs.yaml

vagrant@master:~$ kubectl get pods -o wide
NAME               READY     STATUS    RESTARTS   AGE       IP          NODE      NOMINATED NODE
nfs-server-nqml2   1/1       Running   0          22m       10.32.0.4   node2     <none>
web                1/1       Running   0          21s       10.40.0.2   node1     <none>
vagrant@master:~$

Exec into the container to test writing to the NFS
vagrant@master:~$ kubectl exec -it web -- bash
root@web:/# cat /usr/share/nginx/html/index.html
Hello from NFS!
root@web:/# 
root@web:/# echo "create new file" >> /usr/share/nginx/html/test.txt
root@web:/# cat /usr/share/nginx/html/test.txt   
create new file
root@web:/# exit

# SSH into "node2", where our NFS server pod is running

vagrant@master:~$ ssh node2

Verify that the file created inside of our web pod (running on "storage1") was persisted to the NFS directory on "storage0":

vagrant@node2:~$ cat /data/nfs/
index.html  test.txt    
vagrant@node2:~$ cat /data/nfs/test.txt 
create new file
vagrant@node2:~$ 


16.2-Dynamic Volumes using the NFS Provisioner


###Stateful Applications: Deploying WordPress and MySQL with NFS Dynamic Volumes###

-Create the NFS Server POD

vagrant@master:~$ kubectl create -f nfs-server-rc.yaml

-Create the default storage class (replace the server IP by the CLUSTER-IP of your nfs-server service , kubectl get service)

vagrant@master:~$ cat nfs-storageclass.yaml 
kind: StorageClass
apiVersion: storage.k8s.io/v1beta1
metadata:
  namespace: default
  name: default-storage
  annotations:
    storageclass.kubernetes.io/is-default-class: "true"
  labels:
    kubernetes.io/cluster-service: "true"
provisioner: kubernetes.io/nfs
parameters:
  path: /
  server: 10.107.221.244

vagrant@master:~$ kubectl create -f nfs-storageclass.yaml 
storageclass.storage.k8s.io/default-storage created
vagrant@master:~$ kubectl get storageclass
NAME                        PROVISIONER         AGE
default-storage (default)   kubernetes.io/nfs   16s

-Create persistent volumes (should be created by Admin cluster)

vagrant@master:~$ cat nfs-persistent-volume.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv-1
  labels:
    type: local
spec:
  storageClassName: default-storage
  capacity:
    storage: 500Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /tmp/data/pv-1
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: local-pv-2
  labels:
    type: local
spec:
  storageClassName: default-storage
  capacity:
    storage: 500Mi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: /tmp/data/pv-2
vagrant@master:~$

vagrant@master:~$ kubectl create -f nfs-persistent-volume.yaml 
persistentvolume/local-pv-1 created
persistentvolume/local-pv-2 created
vagrant@master:~$ kubectl get persistentvolumes
NAME         CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM     STORAGECLASS      REASON    AGE
local-pv-1   500Mi      RWO            Retain           Available             default-storage             11s
local-pv-2   500Mi      RWO            Retain           Available             default-storage             11s
vagrant@master:~$


-Create a Secret for MySQL Password
vagrant@master:~$  kubectl create secret generic mysql-pass --from-literal=password=YOUR_PASSWORD

default-token-6dtkp   kubernetes.io/service-account-token   3         15d
mysql-pass            Opaque                                1         1d
vagrant@master:~$ 

-Create persistent volumes claim and Deploy application (Mysql and Wordpress)
vagrant@master:~$ cat wordpress-deployment.yaml 
apiVersion: v1
kind: Service
metadata:
  name: wordpress-mysql
  labels:
    app: wordpress
spec:
  ports:
    - port: 3306
  selector:
    app: wordpress
    tier: mysql
  clusterIP: None
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
  labels:
    app: wordpress
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 400Mi
---
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: wordpress-mysql
  labels:
    app: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
      tier: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: mysql
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-pass
              key: password
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
apiVersion: v1
kind: Service
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  ports:
    - port: 80
  selector:
    app: wordpress
    tier: frontend
  type: LoadBalancer
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: wp-pv-claim
  labels:
    app: wordpress
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 400Mi
---
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: wordpress
  labels:
    app: wordpress
spec:
  selector:
    matchLabels:
      app: wordpress
      tier: frontend
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: wordpress
        tier: frontend
    spec:
      containers:
      - image: wordpress:4.8-apache
        name: wordpress
        env:
        - name: WORDPRESS_DB_HOST
          value: wordpress-mysql
        - name: WORDPRESS_DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-pass
              key: password
        ports:
        - containerPort: 80
          name: wordpress
        volumeMounts:
        - name: wordpress-persistent-storage
          mountPath: /var/www/html
      volumes:
      - name: wordpress-persistent-storage
        persistentVolumeClaim:
          claimName: wp-pv-claim
vagrant@master:~$ 

vagrant@master:~$ kubectl create -f wordpress-deployment.yaml  
service/wordpress-mysql created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/wordpress-mysql created
service/wordpress created
persistentvolumeclaim/wp-pv-claim created
deployment.apps/wordpress created
vagrant@master:~$ kubectl get pods -o wide
NAME                              READY     STATUS        RESTARTS   AGE       IP          NODE      NOMINATED NODE
nfs-server-kn8vn                  1/1       Running       0          2h        10.32.0.4   node2     <none>
wordpress-5bc75fd7bd-pqcqf        1/1       Running       0          4s        10.32.0.5   node2     <none>
wordpress-mysql-565494758-8vbd2   1/1       Running       0          5s        10.40.0.2   node1     <none>

-Inspect deployment

vagrant@master:~$ ssh node2
vagrant@node2:~$ ls /tmp/data/
pv-1  pv-2
vagrant@node2:~$ ls /tmp/data/pv-1
index.php    wp-activate.php     wp-comments-post.php  wp-content   wp-links-opml.php  wp-mail.php      wp-trackback.php
license.txt  wp-admin            wp-config.php         wp-cron.php  wp-load.php        wp-settings.php  xmlrpc.php
readme.html  wp-blog-header.php  wp-config-sample.php  wp-includes  wp-login.php       wp-signup.php
vagrant@node2:~$ ls /tmp/data/pv-2
auto.cnf  ibdata1  ib_logfile0  ib_logfile1  mysql  performance_schema  wordpress

vagrant@master:~$ kubectl exec wordpress-mysql-565494758-8vbd2 -it -- bash
root@wordpress-mysql-565494758-8vbd2:/# ls
bin  boot  dev	docker-entrypoint-initdb.d  entrypoint.sh  etc	home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var

root@wordpress-mysql-565494758-8vbd2:/# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 5
Server version: 5.6.41 MySQL Community Server (GPL)

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| wordpress          |
+--------------------+
4 rows in set (0.00 sec)

-Test Wordpress
vagrant@master:~$ kubectl get services
NAME              TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                      AGE
kubernetes        ClusterIP      10.96.0.1        <none>        443/TCP                      16d
nfs-server        ClusterIP      10.107.221.244   <none>        2049/TCP,20048/TCP,111/TCP   12h
wordpress         LoadBalancer   10.106.222.58    <pending>     80:31857/TCP                 10h
wordpress-mysql   ClusterIP      None             <none>        3306/TCP                     10h
vagrant@master:~$ 
vagrant@master:~$ kubectl describe svc wordpress | grep NodePort 
NodePort:                 <unset>  31857/TCP
vagrant@master:~$ 

Put the two together to get the URL for the WordPress service: http://10.106.222.58:31857

-Scale Deployment
vagrant@master:~$ kubectl scale --replicas=2 deployment/wordpress
deployment.extensions/wordpress scaled
vagrant@master:~$ kubectl get pods -o wide
NAME                              READY     STATUS        RESTARTS   AGE       IP          NODE      NOMINATED NODE
nfs-server-kn8vn                  1/1       Running       0          2h        10.32.0.4   node2     <none>
web                               0/1       Terminating   0          9d        10.40.0.2   node1     <none>
wordpress-5bc75fd7bd-pqcqf        1/1       Running       0          9m        10.32.0.5   node2     <none>
wordpress-5bc75fd7bd-spclk        1/1       Running       0          4s        10.40.0.3   node1     <none>
wordpress-mysql-565494758-8vbd2   1/1       Running       0          9m        10.40.0.2   node1     <none>


-clean Deployment
vagrant@master:~$ kubectl delete service -l app=wordpress
vagrant@master:~$ kubectl delete deployment -l app=wordpress
vagrant@master:~$ kubectl delete pvc -l app=wordpress

vagrant@master:~$ kubectl delete secret mysql-pass

