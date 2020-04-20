# Generating a Data Encryption Config for Kubernetes
Kubernetes offers the ability to encrypt sensitive data at rest. In order to take advantage of this feature, it is necessary to generate an encryption key and a data encryption config. Let's generate an encryption key and a data encryption config file for Kubernetes. 

## Generate an encryption key and include it in a Kubernetes data encryption config file
To accomplish this task, run command `ENCRYPTION_KEY=$(head -c 32 /dev/urandom | base64)` then run file `generate-encryption-key.sh` on your workspace server.

## Copy the file to the Kubernetes controller servers.
Copy generated file `encryption-config.yaml` to each Kubernetes controller by running these commands from the workspace server. Be sure to replace the placeholders with the actual IP addresses of the controller servers.  
* `scp encryption-config.yaml cloud_user@<controller 1 public ip>:~/`  
* `scp encryption-config.yaml cloud_user@<controller 2 public ip>:~/`  