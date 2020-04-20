# Creating a Certificate Authority and TLS Certificates for Kubernetes

In order to generate certificates, you need to:

* Provision the certificate authority (CA)
* Generate the necessary Kubernetes client certs, as well as kubelet client certs for two worker nodes.
* Generate the Kubernetes API server certificate.
* Generate a Kubernetes service account key pair.

Here is the cluster architecture for which you will need to generate certificates. Note that these are not real servers, just values that we will use for the purposes of this activity.

Controllers:

* Hostname: controller0.mylabserver.com, IP: 172.34.0.0
* Hostname: controller1.mylabserver.com, IP: 172.34.0.1

Workers:

* Hostname: worker0.mylabserver.com, IP: 172.34.1.0
* Hostname: worker1.mylabserver.com, IP: 172.34.1.1

Kubernetes API Load Balancer:

* Hostname: kubernetes.mylabserver.com, IP: 172.34.2.0