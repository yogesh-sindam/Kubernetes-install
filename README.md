## How to install kubernetes cluster (kubeadm setup) on ubuntu:latest
https://youtu.be/ScHDSoYzWN4?si=iuuK1xkSLAhDf0cp
######################################################################

# Kubernetes Nodes:
In a kubernetes cluster, you will encounter two distinct categories of nodes:

# Master node: These nodes play crucial role in managing the contol API calls for various components within the kubernetes
cluster. this includes overseeing pods, replication controller, services, nodes, and more. 

# Worker Node: worker nodes are resposible for providing runtime environments for container. 
its worth nothing that a group of container pods can extend across multiple worker Nodes, ensuring optimal resource allocation and management.

# Pre-requeisites:
Before diving into the installation, ensure that your environment meets the following conditions:

An ubuntu 24.04 LTS system.
Minimum 2GB RAM or More.
Minimum 2CPU core (or 2 vCPU's).
20 GB 0f free disk space on /var (or more).

Ensure machines in the cluster can ping each other via IP and Hostname: (Run on each nodes)
ping ip_address
```
set-hostname (to keep understadable)
$ sudo su 
$hostnamectl set-hostname master-node
$bash
$hostname 
```
$ sudo su 
$hostnamectl set-hostname worker-node
$bash
$hostname 
on each node 

Edit the /etc/hosts file  to include mapping of ip addresses to hostnames:
$sudo nano /etc/hosts 
172.31.0.1 master-node
172.31.0.2 worker-node
.
.
also on if you are having more nodes

ping each other via hostname:(master-node)
$ping worker-node

ping each other via hostname:(worker-node)
$ping master-node

Disable swap on all nodes:
Check swap staus suing free -h and disable id necessary.
$free -h

Installing Kubeadm:
Follow the below steps to install kubeadm and related dependencies on all nodes:

Master node and Worker nodes:
SSH into the master node.
Ensure sudo access is available
$sudo -l  (on each node )

Update packages and their versions:
$sudo apt-get update && sudo apt-get upgrade -y 

Install necessary packages: apt-transport-https, curl:
$sudo apt-get update && sudo apt-get install -y apt-transport-https curl 

Install Docker:
sudo apt-get install docker.io -y

Add Google apt key to verify releases:
NOTE: i used to this command form (https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)

$curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
$sudo apt-get update
$sudo apt-get install -y apt-transport-https ca-certificates curl gpg
$echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

$sudo apt-get update
$sudo apt-get install -y kubelet kubeadm kubectl

Prevent automatic update/removals of kubernetes packages:
$sudo apt-mark hold kubelet kubeadm kubectl


Initiate the kubenetes cluter (i did it on ec2 master-node):
Note:were you run the below command that node will become a control-plane 
$kubeadm init --pod-network-cidr=13.210.139.26/16 -v=9
13.210.139.26 = ec2 master node public ip
 O/P: Your kubernetes control-plane has initialized successfully!
	
once kubeadm init is completed, including setting up kubeconfig:	
To start using the cluster, need to run the following:
for regular user:
	mkdir -p $HOME/.kube
	cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
	chown $(id -u):$(id -g) $HOME/.kube/config
for root user:
	export KUBECONFIG=/etc/kubernetes/admin.conf

Adding Worker nodes:
Join worker nodes to the master node using the provided join cammand:
$kubeadm join 172.31.7.146:6443 --token hqqslu.skhds68sdw2q5ks5amy \
        --discovery-token-ca-cert-hash sha256:2c9646e3f969ef14d6d3937f0fd1lfjlgdjfdjl5fdbf954554c06d0686193
Note: this command will been run on worker node only 
```
$kubectl get nodes 
NAME          STATUS   ROLES           AGE    VERSION
master-node   NotReady    control-plane   144m   v1.32.1
worker-node   NotReady    <none>          40m    v1.32.1
```
verify cluster setup:
$kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

$kubectl get nodes 
NAME          STATUS   ROLES           AGE    VERSION
master-node   Ready    control-plane   144m   v1.32.1
worker-node   Ready    <none>          40m    v1.32.1

$kubectl get nodes -o wide 
NAME          STATUS   ROLES           AGE    VERSION   INTERNAL-IP    EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION   CONTAINER-RUNTIME
master-node   Ready    control-plane   145m   v1.32.1   172.31.7.146   <none>        Ubuntu 24.04 LTS     6.8.0-1021-aws   containerd://1.6.33
worker-node   Ready    <none>          41m    v1.32.1   172.31.5.144   <none>        Ubuntu 24.04.1 LTS   6.8.0-1021-aws   containerd://1.7.12
