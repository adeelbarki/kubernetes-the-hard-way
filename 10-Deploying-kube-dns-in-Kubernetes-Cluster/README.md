# Deploying kube-dns in a Kubernetes Cluster
It is most probable that IP addresses change so pods are located using hostname. For this we require dns service. We deployed kube-dns to a cluster in order to provide DNS service to pods.

## Deploy kube-dns to the cluster  
`kubectl create -f https://storage.googleapis.com/kubernetes-the-hard-way/kube-dns.yaml`  

Verify if kube-dns pods is ready
`kubectl get pods -l k8s-app=kube-dns -n kube-system`  

Output should look like this  
![output-kube-dns](https://user-images.githubusercontent.com/22978850/79725613-e8f89580-8302-11ea-89bc-3144262d509d.png)


## Test the DNS by creating a service, and perform a DNS lookup for service from another pod using the service name  
Test the DNS by creating a service, and access the service from another pod using the service name.  

* Create a simple service, expose deployment on port 80. Then get services  
`kubectl run nginx --image=nginx`  
`kubectl expose deployment nginx --port 80`  
`kubectl get svc`  

* Spin up a busybox pod for testing  
`kubectl run busybox --image=busybox:1.28 --command -- sleep 3600`  

* and get the pod name..  
`POD_NAME=$(kubectl get pods -l run=busybox -o jsonpath="{.items[0].metadata.name}")`  

* Perform a DNS lookup of nginx service from busybox pod to verify that DNS is working  
`kubectl exec $POD_NAME -- nslookup nginx`  

* Output  
![ns-lookup-nginx](https://user-images.githubusercontent.com/22978850/79725876-50aee080-8303-11ea-8a73-eba17a54cc64.png)



