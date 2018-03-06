# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 8. ReplicaSets

You should consider creating replicas of your resource for:
* redundancy — to tolerate failure
* scale — more request can be handled
* sharding — different computation in parallel

##### Reconciliation loops

Idea behind reconciliation loop is to turn current state into desired state.
reconciliation loop continuously query service and if desired state != current — takes action.

##### Pods and ReplicaSets
ReplicaSets don't own managed Pods and use labels to query state of set.
You can apply ReplicaSet rules to existing container

##### Debugging
if node misbehaves it is better to change it label to move it from replica set. ReplicaSet will create a new container (because of missing one) and you can interactively debugging this container

##### ReplicaSet Spec
ReplicaSet should have
* name (in `metadata.name` field)
* spec section with:
    * number of pods
    * template

example:
```
apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  name: kuard
spec:
  replicas: 1
  template: //this is a line where pod definition starts
    metadata:
      labels:
        app: kuard
        version: "2"
    spec:
      containers:
        - name: kuard
          image: "gcr.io/kuar-demo/kuard-amd64:2"
```

##### Other commands
`kubectl describe rs <node>` for details
`kubectl get pods <pod> -o yaml` to find replicaSet that manages pod (it should have `kubernetes.io/created-by` tag)

##### Autoscaling
Kubernetes can auto scale nodes in cases high usage.
Process called horizontal pod autoscaling (**hpa** resource type)

Scaling types:
* **horizontal scaling** which involves creating additional replicas of the Pod
* **vertical scalling** increasing resources for particular pod. (not implemented in Kubernetes right now)

###### Example
autoscaling besed on CPU load can be done with
```
kubectl autoscale rs <name> --min=2 --max=5 --cpu-percent=80
```
to get horizontal pod autoscalers `kubectl get hpa`

>Again, there is no hpa and replicaset decoupled, and can be modified/deleted independently


#### Deleting ReplicaSet

to delete replicaceSet use `kubectl delete rs <name>` use `--cascade=false` flag to delete rs only, without pods.

