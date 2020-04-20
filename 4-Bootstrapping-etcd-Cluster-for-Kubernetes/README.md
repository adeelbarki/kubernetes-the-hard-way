# Bootstrapping an etcd Cluster for Kubernetes
Kubernetes uses etcd to reliably store data in a distributed fashion. One of the necessary steps for setting up a Kubernetes cluster from scratch is to configure an etcd cluster that spans all of the Kubernetes control nodes. We will install and configure an etcd cluster in preparation for setting up Kubernetes. This will create a multi-node etcd cluster that is capable of supporting the Kubernetes control plane.

## Install the etcd binary on both control nodes
* Run this command on both kubernetes control nodes:  
`wget -q --show-progress --https-only --timestamping "https://github.com/coreos/etcd/releases/download/v3.3.5/etcd-v3.3.5-linux-amd64.tar.gz"`__
`tar -xvf etcd-v3.3.5-linux-amd64.tar.gz`  
`sudo mv etcd-v3.3.5-linux-amd64/etcd* /usr/local/bin/`  

## Configure and start the etcd service on both control nodes
* Configure these steps on both kubernetes control nodes:  
`sudo mkdir -p /etc/etcd /var/lib/etcd`  
`sudo cp ca.pem kubernetes-key.pem kubernetes.pem /etc/etcd/`  

* Set the ETCD_NAME variable. Be sure to replace the placeholder in this command with controller-0 or controller-1, as appropriate for each server:  
`ETCD_NAME=<controller-0 or controller-1>`  
`INTERNAL_IP=$(curl http://169.254.169.254/latest/meta-data/local-ipv4)`  

* Replace with private ip of the respective controller:  
`CONTROLLER_0_INTERNAL_IP=<controller 0 private ip>`  
`CONTROLLER_1_INTERNAL_IP=<controller 1 private ip>`  

* Create the etcd systemd unit file by running file `create-etcd-systemd.sh`. Be sure to replace the placeholders in --name and --initial-cluster with real values  

* Start and enable the etcd service  
`sudo systemctl daemon-reload`  
`sudo systemctl enable etcd`  
`sudo systemctl start etcd`  

* Verify that your etcd cluster is working like this  
```sudo ETCDCTL_API=3 etcdctl member list \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=/etc/etcd/ca.pem \
  --cert=/etc/etcd/kubernetes.pem \
  --key=/etc/etcd/kubernetes-key.pem
```