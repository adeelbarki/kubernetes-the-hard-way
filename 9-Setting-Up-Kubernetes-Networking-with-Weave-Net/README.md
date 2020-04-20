# Setting Up Kubernetes Networking with Weave Net
Weave Net is one of several tools that provide an implementation of the Kubernetes networking model. Created a virtual network that is accessible to all pods within the cluster.

## Enable IP forwarding on all worker nodes
We need to make sure IP forwarding is enabled on the worker nodes. Enable it by running the following on both worker nodes:  
`sudo sysctl net.ipv4.conf.all.forwarding=1`  
`echo "net.ipv4.conf.all.forwarding=1" | sudo tee -a /etc/sysctl.conf`  

## Install Weave Net in the cluster
* Install Weave Net using a configuration from Weaveworks  
`kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')&env.IPALLOC_RANGE=10.200.0.0/16"`

* Verify pods  
`kubectl get pods -n kube-system`

* Output  
![output-pods](https://user-images.githubusercontent.com/22978850/79724060-5525ca00-8300-11ea-9958-bd74b3888900.png)

* Run new pods to test weavenet network

* Create an Nginx deployment with 2 replicas by running file `1-nginx-deployment.sh`

* Create a service for that deployment to test connectivity  
`kubectl expose deployment/nginx`  

* Run busybox plus pod (This box provides ssh & curl functionality)  
`kubectl run busybox --image=radial/busyboxplus:curl --command -- sleep 3600`  

* Get the pod name in a variable  
`POD_NAME=$(kubectl get pods -l run=busybox -o jsonpath="{.items[0].metadata.name}")`

* Get the IP addresses of our two nginx pods  
`kubectl get ep nginx`  
(Two ip addresses will list under endpoints)

* Replace ip address with nginx pod ip address.  
`kubectl exec $POD_NAME -- curl <first nginx pod IP address>`  
`kubectl exec $POD_NAME -- curl <second nginx pod IP address>`  
(Both commands should return some HTML code. This shows successfull connection to other pods)

* Connect with services  
`kubectl get svc`  
(This should display the IP address for our Nginx service)

* Test if we can access service from busybox pod.  
`kubectl exec $POD_NAME -- curl <nginx service IP address>`  

* Output will be same html code!