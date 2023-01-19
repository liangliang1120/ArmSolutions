## Building locally
Building this image requires Docker 17.05 or higher, Podman 3.3 or above. 
install Docker and Podman
https://docs.docker.com/desktop/install/mac-install/
https://podman.io/getting-started/installation

![image](https://user-images.githubusercontent.com/35073431/212987969-e78ce1c1-df29-4bf9-9089-79065fedf07c.png)

brew install podman

'''
docker build -t ampere/nginx-vod-app .
'''

or
'''
podman build -t ampere/nginx-vod-app .
'''

https://solutions.amperecomputing.com/solutions/video-services?scrollToId=solutions-page-panel-2

## Deploying SUSE K3s 3-Node Cluster with Rancher on Ampere® Altra® Platforms

Deployment of SUSE K3s 3-node compact cluster on the Ampere® Altra® platform. 
The installation of 
- Rancher for K3s cluster management, 
- Longhorn for block storage, 
- Nginx Ingress Controller for ingress, 
- Prometheus and Grafana for collecting and presenting metrics.

K3s 3-node cluster and a bastion node.

## Setup Instructions
 Refer to the following steps to install K3s without Traefik:
1. For the servers provisioned with SUSE Linux Enterprise (SLE) Micro 5.2, ensure the drives for NICs are up to date.
If you want to deploy K3s on other Linux distributions, refer to https://www.suse.com/suse-rancher/support-matrix/all-supportedversions/rancher-v2-6-5/ for the Rancher K3s support matrix.
 
 ![image](https://user-images.githubusercontent.com/35073431/213030196-8237d657-b7d0-40c3-8c48-42822a775ac4.png)


 2.Deploy K3s on the first node (node1) and disable “traefik.”
Based on the support matrix, choose K3s version 1.23.6+k3s1.


connect to node1, get into the folder with perm file:
 '''
 ssh -i "K3s_cluster_key.pem" ec2-user@ec2-44-198-170-10.compute-1.amazonaws.com
 '''
 
 Refresh OpenSUSE repository from the Internet, execute:
 '''
sudo zypper refresh
'''
Upgrade OpenSUSE Linux, type:
'''
sudo zypper update
'''
deploy:
 '''
 node1 ~$ export INSTALL_K3S_EXEC="server --no-deploy traefik --cluster-init --write-kubeconfigmode=644"
node1 ~$ export K3s_VERSION="v1.23.6+k3s1"
node1 ~$ curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=${K3s_VERSION} INSTALL_K3S_EXEC=${INSTALL_K3S_EXEC} sh -s -
'''
![image](https://user-images.githubusercontent.com/35073431/213271416-a35faf16-edcf-4927-b859-5f1173ea2d6d.png)

3. For the servers provisioned with SLE Micro 5.1 or later, reboot the system when the K3s installer shell script completes the
installation.
'''
sudo reboot
'''
if use aws instance, you can get into the aws instance detail to reboot
![image](https://user-images.githubusercontent.com/35073431/213283492-7cd67a28-6b5b-4047-8b6e-b7b48e093eda.png)

4. Retrieve the access token for the K3s cluster when the system is up and running.
'''
node1 ~$ sudo cat /var/lib/rancher/k3s/server/node-token
'''
you can see the token, copy it out for following operation
![image](https://user-images.githubusercontent.com/35073431/213283050-8879249f-819c-407d-b87f-bfdbf608a278.png)


5. On the other two nodes (e.g., node2 and node3), clean up the previous installation of K3s, if any.
'''
node2 ~$ sudo /usr/local/bin/k3s-uninstall.sh
node2 ~$ sudo reboot
'''
- tip:
- make sure nodes can connect,you can edit the security group: create all traffic indound rule
![image](https://user-images.githubusercontent.com/35073431/213319826-5e25bfb2-5915-4515-9988-1f9090d48cea.png)


6. Add NODE_TOKEN and FIRST_SERVER in the script to include the two nodes into the cluster with the first node. Install K3s on the
other node.

'''
node2 ~$ export INSTALL_K3S_EXEC="server --no-deploy traefik"
node2 ~$ export FIRST_SERVER_IP="10.76.85.101". export FIRST_SERVER_IP="172.31.23.125"
node2 ~$ export NODE_TOKEN="xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx::server:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
export NODE_TOKEN="K10ab2475f33a8b4075669b1ee5ce0f1c023273fa187bc2b2013b3bc65ea837a96c::server:8f3a820f701f16830b116c4075b2a03c"
node2 ~$ export K3s_VERSION="v1.23.6+k3s1"
node2 ~$ curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION=${K3s_VERSION} K3S_URL=https://${FIRST_SERVER_IP}:6443 \
 K3S_TOKEN=${NODE_TOKEN} \
 K3S_KUBECONFIG_MODE="644" INSTALL_K3S_EXEC=${INSTALL_K3S_EXEC} \
 sh -
 '''
 ![image](https://user-images.githubusercontent.com/35073431/213310160-103fefc7-15a7-4113-afd6-2a790b72a8b9.png)


## Install Nginx Ingress Controller on K3s
1. Since there is no Traefik as an ingress controller, choose the right version of the Nginx Ingress Controller for supporting K3s.
In this PoC, it is version 1.1.0. It will create an ingress-nginx namespace. Also, you will need a patch for this deployment using a DNS load
balancer. 


