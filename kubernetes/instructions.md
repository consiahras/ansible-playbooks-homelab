
For production would be better to use a load balancer as endpoint
https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/
sudo kubeadm init --control-plane-endpoint "LOAD_BALANCER_DNS:LOAD_BALANCER_PORT" --upload-certs


Install Kubernetes Cluster using kubeadm
Follow this documentation to set up a Kubernetes cluster on Ubuntu 20.04 LTS.

This documentation guides you in setting up a cluster with one master node and 3 worker nodes

My current Lab :
Role	  FQDN	IP	            OS.         	RAM	CPU
Master	ubu00	192.168.56.105	Ubuntu 20.04	4G	2
Worker	ubu01	192.168.56.103	Ubuntu 20.04	4G	2
Worker	ubu02	192.168.56.104	Ubuntu 20.04	4G	2
Worker	ubu03	192.168.56.102	Ubuntu 20.04	4G	2

On both Kubernetes master ubu00 and Kubernetes worker *ubu01-3
##**Login as `root` user
```
sudo su -
```
Perform all the commands as root user unless otherwise specified
##### Disable Firewall
```
ufw disable
```
##### Disable swap
```
swapoff -a; sed -i '/swap/d' /etc/fstab
```
##### Update sysctl settings for Kubernetes networking
```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```
##### Install docker engine
```
{
  apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  apt update
  apt search docker-ce   (returned docker-ce/focal 5:20.10.11~3-0~ubuntu-focal)
  apt install -y docker-ce=5:20.10.11~3-0~ubuntu-focal containerd.io
  
}
```
### Kubernetes Setup
##### Add Apt repository
```
{
  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list
  cat /etc/apt/sources.list.d/kubernetes.list
}
```
##### Install Kubernetes components
```
apt update
apt search  kubeadm kubelet kubectl
apt install -y kubeadm=1.22.4-00 kubelet=1.22.4-00 kubectl=1.22.4-00
```
##### In case you are using LXC containers for Kubernetes nodes
Hack required to provision K8s v1.15+ in LXC containers
```
{
  mknod /dev/kmsg c 1 11
  echo '#!/bin/sh -e' >> /etc/rc.local
  echo 'mknod /dev/kmsg c 1 11' >> /etc/rc.local
  chmod +x /etc/rc.local
}
```

## On kmaster
##### Initialize Kubernetes Cluster
Update the below command with the ip address of kmaster
Example: The suggested CIDR for flannel and canal networks is 10.244.0.0/16 and for calico network it could be 192.168.0.0/16.
for -apiserver-advertise-address use the ip that your nodes have connectivity
```
kubeadm init --apiserver-advertise-address=192.168.56.105 --pod-network-cidr=192.168.0.0/16 
```
##### Deploy Calico network : Check from https://docs.projectcalico.org/ the latest version and add it below
```
kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.21/manifests/calico.yaml
```

##### Cluster join command
```
kubeadm token create --print-join-command
```

##### To be able to run kubectl commands as non-root user
If you want to be able to run kubectl commands as non-root user, then as a non-root user perform these
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## On Kworker
##### Join the cluster
Use the output from __kubeadm token create__ command in previous step from the master server and run here.

## Verifying the cluster (On kmaster)
##### Get Nodes status
```
kubectl get nodes
```
##### Get component status
```
kubectl get cs
```
