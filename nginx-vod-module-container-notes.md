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

Ingress is an API object that manages external access to services in the cluster, and the typical access method is HTTP. 
Ingress can provide load balancing, SSL termination, and name-based virtual hosting.

Deployment of SUSE K3s 3-node compact cluster on the Ampere® Altra® platform. 
The installation of 
- Rancher for K3s cluster management, 
- Longhorn for block storage, 
- Nginx Ingress Controller for ingress, 
- Prometheus and Grafana for collecting and presenting metrics.

K3s 3-node cluster and a bastion node.

## Setup Instructions
 install K3s without Traefik:
 
 ![image](https://user-images.githubusercontent.com/35073431/213030196-8237d657-b7d0-40c3-8c48-42822a775ac4.png)

 
 2.Deploy K3s on the first node (node1) and disable “traefik.”
Based on the support matrix, choose K3s version 1.23.6+k3s1.


3. For the servers provisioned with SLE Micro 5.1 or later, reboot the system when the K3s installer shell script completes the
installation.


4. Retrieve the access token for the K3s cluster when the system is up and running.


5. On the other two nodes (e.g., node2 and node3), clean up the previous installation of K3s, if any.

6. Add NODE_TOKEN and FIRST_SERVER in the script to include the two nodes into the cluster with the first node. Install K3s on the
other node.

## Install Nginx Ingress Controller on K3s
1. Since there is no Traefik as an ingress controller, choose the right version of the Nginx Ingress Controller for supporting K3s.
In this PoC, it is version 1.1.0. It will create an ingress-nginx namespace. Also, you will need a patch for this deployment using a DNS load
balancer. 


