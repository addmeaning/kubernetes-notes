# Kubernetes
### Notes from Kubernetes in Action
#### chapter 1

### Brief architecture of Kubernetes cluster
cluster composed of 2 types of nods:
* **master node** (hosts Kubernetes Control Plane)
* **worker node** (run applications you deploy)

_**Control plane**_ consists:
* API server (you and other controle planes communicates with cluster using API server)
* Scheduler (assigns worker node to deployable component of app)
* Contoller manager(replicating services, keeping track of node, handling failures)
* etcd stores cluster configuration

_**Worker nodes**_ 
* container runtime (Docker)
* Kubelet (talks to API-server and manages contain)
* service proxy (kube-proxy) load-balances network traffic between application components

#### Benefits of K8s
* simplifying application deployment
* better hardware utilization
* health checking and self-healing
* automatic scaling
* simplifying development

#### Chapter summary
* monoliths easier to deploy, but harder to maintain over time and harder to scale
* microservices easier to develop and scale, but harder to deploy and configure
* containers provide all the benefits (except security) as VM, but lightweight and allowe utilize hardware better
* Kubernetes expose whole cluster as computational resource for running applications
