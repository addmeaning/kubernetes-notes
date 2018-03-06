# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 2

_**Some useful info about Docker**_

Most popular container format for Kubernetes is Docker container 

Containers fall into two main categories:
* system (run full boot process)
* application (run one app) 

**Image Security**

Deleting file not deletes it from previous layer, attacker can compose image
with layers that have secret file

_Secrets_ and images shouldn't be mixed.

**Image Sizes**

Layers should be ordered in terms of stability. More stable layers should go first.

**Remote Storing**

Google container registry is a alternative platform for Docker hub.

**Limiting memory resources**

*docker run* have several switches for that
* --memory 200m
* --memory-swap 1G
* --cpu-shares 1024