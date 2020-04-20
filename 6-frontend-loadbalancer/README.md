# Setting Up a Frontend Load Balancer for the Kubernetes API
One way to manage access to the Kubernetes API across distributed control nodes is to use a load balancer. In this lab we wil set up the process of setting up an Nginx load balancer to manage traffic to the Kubernetes API across multiple nodes.

## Install Nginx on the load balancer server
Install nginx on server that is configured for load balancer

`sudo apt-get install -y nginx`
`sudo systemctl enable nginx`
`sudo systemctl status nginx` (status check)


## Configure Nginx to balance Kubernetes API traffic across the two controllers

* Configure Nginx load-balancer
`sudo mkdir -p /etc/nginx/tcpconf.d`

* Open nginx.conf file
`sudo vi /etc/nginx/nginx.conf`

* Add `include /etc/nginx/tcpconf.d/*;` this line at the end of nginx.conf and close vim by pressing Esc key and typing `:wq!`

* Run file `1-config-kubernetes-api` to configure Kubernetes API load balancing

* Open file that is just created `sudo vi /etc/nginx/tcpconf.d/kubernetes.conf` and edit controller private IPs.

* Reload the Nginx configuration by running command `sudo nginx -s reload`

* Send a request to the Kubernetes API through the load balancer in order to check if its working
`curl -k https://localhost:6443/version`