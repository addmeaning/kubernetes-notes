# Kubernetes
### Notes from Kubernetes in Action
#### chapter 3

**Pods**
All containers of pod land on same node.

It is important to separate processes to different containers because of shared stdin, included health check service, that will restart the container when process ended

All container of the pod run under the same network (share same IP and port space) and same loopback network interface, so container can communicate with other containers in the same pod through localhost

**Pod definition**
typical pod definition may contain folowing sections:
1. Kubernetes API version used in this YAML
2. Type of Kubernetes object/resource (like Pod, Service)
3. Metadata (annotations, labels, object name)
4. Specification (technical part of object/resource: pod containers, volumes)
5. Status of the pod and underlying containers (status contains read-only runtime data that shows state of resource at a given moments)

> Port exposure:
purely informational, if container accepting conneciton through a port bound to 0.0.0.0 address, other pods can connect to that pod. However it is always better to explicitly state exposed port for clarity 