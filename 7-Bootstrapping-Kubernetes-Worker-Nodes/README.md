# Bootstrapping Kubernetes Worker Nodes
In this lab, we will configure worker nodes and create some worker nodes. 

## Install the required packages on both worker nodes
`sudo apt-get -y install socat conntrack ipset`__
[Socat: is a utitlity for data transfer between two addresses]__
[conntrack: This tool can be used to search, list, inspect and maintain the connection tracking subsystem of the Linux kernel]__
[ipset: store multiple IP addresses or port numbers and match against the collection by iptables at one swoop, update iptables or express complex IP address]__

## Download and install binaries
------------------------------------------------------------------
* Download binaries__
wget -q --show-progress --https-only --timestamping \
  https://github.com/kubernetes-incubator/cri-tools/releases/download/v1.0.0-beta.0/crictl-v1.0.0-beta.0-linux-amd64.tar.gz \
  https://storage.googleapis.com/kubernetes-the-hard-way/runsc \
  https://github.com/opencontainers/runc/releases/download/v1.0.0-rc5/runc.amd64 \
  https://github.com/containernetworking/plugins/releases/download/v0.6.0/cni-plugins-amd64-v0.6.0.tgz \
  https://github.com/containerd/containerd/releases/download/v1.1.0/containerd-1.1.0.linux-amd64.tar.gz \
  https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kubectl \
  https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kube-proxy \
  https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kubelet

------------------------------------------------------------------
* Create folders__
sudo mkdir -p \
  /etc/cni/net.d \
  /opt/cni/bin \
  /var/lib/kubelet \
  /var/lib/kube-proxy \
  /var/lib/kubernetes \
  /var/run/kubernetes

------------------------------------------------------------------
* Change permission to executable:__
`chmod +x kubectl kube-proxy kubelet runc.amd64 runsc`

* Rename file__
`sudo mv runc.amd64 runc`

* Move files o usr/local/bin__
`sudo mv kubectl kube-proxy kubelet runc runsc /usr/local/bin/`

* Extract files__
`sudo tar -xvf crictl-v1.0.0-beta.0-linux-amd64.tar.gz -C /usr/local/bin/`

`sudo tar -xvf cni-plugins-amd64-v0.6.0.tgz -C /opt/cni/bin/`

`sudo tar -xvf containerd-1.1.0.linux-amd64.tar.gz -C /`

## Configure the containerd service
* Create containerd directory__
`sudo mkdir -p /etc/containerd/`

* Run file `1-config-toml` and then run another file `2-containerd-unit-file.sh`.__

## Configure the kubelet service
* Set up kubelet on both worker nodes by using `worker0` or `worker1`  depending on the server DNS for each respective nodes.__
`HOSTNAME=<worker0>.mylabserver.com`__
`HOSTNAME=<worker1>.mylabserver.com`__

* Rename and move hostane key file.__
`sudo mv ${HOSTNAME}-key.pem ${HOSTNAME}.pem /var/lib/kubelet/`__

* Rename and move hostane kubeconfig file__
`sudo mv ${HOSTNAME}.kubeconfig /var/lib/kubelet/kubeconfig`__

* Move certificate file to kubernetes folder__
`sudo mv ca.pem /var/lib/kubernetes/`__

* Create kubelet config file by running file `3-kubelet-config-file.sh`

* Create the kubelet unit file by running file `4-kubelet-unit-file.sh`

## Configure kube-proxy service
* Configre kube-proxy__ 
`sudo mv kube-proxy.kubeconfig /var/lib/kube-proxy/kubeconfig`

* Create kube-proxy config file by runng file `5-kube-proxy-config-file.sh`

* Create kube-proxy unit file by runng file `6-kube-proxy-unit-file.sh`

* Start and enable services__
`sudo systemctl daemon-reload`__
`sudo systemctl enable containerd kubelet kube-proxy`__
`sudo systemctl start containerd kubelet kube-proxy`__

* Check status
`sudo systemctl status containerd kubelet kube-proxy`

## Log in to the control node and verify worker nodes
`kubectl get nodes --kubeconfig /home/cloud_user/admin.kubeconfig`

* Output on control node
-----------------------------------------------------------------__
NAME                      STATUS     ROLES     AGE       VERSION<br />
worker0.mylabserver.com   NotReady   <none>    1m        v1.10.2<br />
worker1.mylabserver.com   NotReady   <none>    1m        v1.10.2<br />
-----------------------------------------------------------------__




