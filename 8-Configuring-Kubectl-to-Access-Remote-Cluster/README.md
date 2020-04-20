# Configuring Kubectl to Access a Remote Cluster
We configured kubectl on our workspace machine and interacted with a Kubernetes cluster (controller) remotely.

## Set the kubectl cluster data
* Remember to replace controller IP address with controller public ip address  
`KUBERNETES_PUBLIC_ADDRESS=<Controller public IP>`  

* Configure kubectl cluster 
```
kubectl config set-cluster kubernetes-the-hard-way \
  --certificate-authority=ca.pem \
  --embed-certs=true \
  --server=https://${KUBERNETES_PUBLIC_ADDRESS}:6443
```

## Set the credentials for kubectl
The credentials are the certificate and key files admin.pem and admin-key.pem. These can be found in the home directory on the workspace server. You can set them up for kubectl  

`kubectl config set-credentials admin --client-certificate=admin.pem --client-key=admin-key.pem`  

## Set the context for the cluster
`kubectl config set-context kubernetes-the-hard-way --cluster=kubernetes-the-hard-way --user=admin`  

## Use the new kubectl context to check which pods are currently running on the cluster
* Configure kubectl to use context
`kubectl config use-context kubernetes-the-hard-way`  

* Verify pods (output: no resource found)
`kubectl get pods`  