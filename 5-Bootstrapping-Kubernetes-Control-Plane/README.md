# Bootstrapping a Kubernetes Control Plane
We will set up a Kubernetes control plane. The control plane will manage the Kubernetes cluster and serves as its primary interface. It will show us a process of setting up a distributed Kubernetes control plane using two servers.

## Download and install the binaries.
Install the binaries for kube-apiserver, kube-controller-manager, kube-scheduler, and kubectl  

`sudo mkdir -p /etc/kubernetes/config`  
```
wget -q --show-progress --https-only --timestamping \
  "https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kube-apiserver" \
  "https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kube-controller-manager" \
  "https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kube-scheduler" \
  "https://storage.googleapis.com/kubernetes-release/release/v1.10.2/bin/linux/amd64/kubectl"
```

`chmod +x kube-apiserver kube-controller-manager kube-scheduler kubectl`  

`sudo mv kube-apiserver kube-controller-manager kube-scheduler kubectl /usr/local/bin/`  

## Configure the kube-apiserver service
* Configure the kube-apiserver service  

`sudo mkdir -p /var/lib/kubernetes/`
```
sudo cp ca.pem ca-key.pem kubernetes-key.pem kubernetes.pem \
  service-account-key.pem service-account.pem \
  encryption-config.yaml /var/lib/kubernetes/
```
`INTERNAL_IP=$(curl http://169.254.169.254/latest/meta-data/local-ipv4)`  

* Set environment variables for private IPs of both controller servers. Replace respected placeholders with the actual private IPs.
`ETCD_SERVER_0=<controller 0 private ip>`  

`ETCD_SERVER_1=<controller 1 private ip>`  

* Create the systemd unit file by rnning `1-system-unit-file.sh`

## Configure the kube-controller-manager service
Configure the kube-controller-manager systemd service  

`sudo mv kube-controller-manager.kubeconfig /var/lib/kubernetes/`  

and run `2-kubecontroler-manager--service.sh` file.

## Configure the kube-scheduler service
Configure the kube-scheduler systemd service
`sudo mv kube-scheduler.kubeconfig /var/lib/kubernetes/` and run `3-kube-scheduler.sh file`.

## Start all of the services
`sudo systemctl daemon-reload`  
`sudo systemctl enable kube-apiserver kube-controller-manager kube-scheduler`  
`sudo systemctl start kube-apiserver kube-controller-manager kube-scheduler`  

Verify if everything is working fine
`kubectl get componentstatuses --kubeconfig admin.kubeconfig`

## Output

![componentstatuses](https://user-images.githubusercontent.com/22978850/79718344-44bc2200-82f5-11ea-8d64-45c5c9b80f16.png)


## Enable HTTP health checks
* Install nginx `sudo apt-get install -y nginx`
* Run file `4-svc-cluster-local.sh`
* Move default local cluster to nginx site-available
 `sudo mv kubernetes.default.svc.cluster.local /etc/nginx/sites-available/kubernetes.default.svc.cluster.local`
* Create a symbolic link
 `sudo ln -s /etc/nginx/sites-available/kubernetes.default.svc.cluster.local /etc/nginx/sites-enabled/`
* Restart and enable nginx
 `sudo systemctl restart nginx`
 `sudo systemctl enable nginx`
* You can verify that the HTTP health checks are working on each control node by using below command (status 200 OK):
 `curl -H "Host: kubernetes.default.svc.cluster.local" -i http://127.0.0.1/healthz`

## Set up RBAC for kubelet authorization (only one controller)
Set up Role-based Access Control for kubelets. This step is only performed once on only once controller node.
* Run this file `5-RBAC-authorization.sh`
