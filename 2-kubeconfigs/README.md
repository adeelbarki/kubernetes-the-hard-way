# Generating Kubeconfigs for a New Kubernetes Cluster

The following kubeconfig files need to be created:

* Kubelet (one kubeconfig for each worker node)
* Kube-proxy
* Kube-controller-manager
* Kube-scheduler
* Admin

Here is the cluster architecture. Note that these are not real servers, just values that we will use for the purposes of this activity.

Controllers:

* Hostname: controller0.mylabserver.com, IP: 172.34.0.0
* Hostname: controller1.mylabserver.com, IP: 172.34.0.1

Workers:

* Hostname: worker0.mylabserver.com, IP: 172.34.1.0
* Hostname: worker1.mylabserver.com, IP: 172.34.1.1

Kubernetes API Load Balancer:

* Hostname: kubernetes.mylabserver.com, IP: 172.34.2.0

## Additional Notes:

* Client certificates have already been created. They can be found in certificates folder.
* The kubelet and kube-proxy services will access the Kubernetes API through the Kubernetes API load balancer. All other services can access it locally at https://127.0.0.1:6443.
* Check the task descriptions and the solution video for more guidance!