# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 12. Deployments

Deployment exists to manage release of new versions.
Process configurable by:
* time between pod upgrades
* health check

#### Usage of Deployments

simple `kubectl run` will create object of deployment

#### Internals
Deployment manage replicaSet. If we configure deployment such that desired number of nodes=2, even if we scale down underlying replicaSet (e. g. for number of nodes=1) we will still have two replicas, system will create second copy of deployment due to desired state of deployment replicas = 2.

If we need to manually change state replicaSet we need to delete deployment.
> remember to switch `--cascade=false` or you will delete pods also

Example deployment
```
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  replicas: 2
  selector:
    matchLabels:
      run: nginx
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx:1.7.12
        imagePullPolicy: Always
      dnsPolicy: ClusterFirst
      restartPolicy: Always
```

Note that Deployment is very similar to ReplicaSet.
In addition to Pod specification there is also a strategy object.
Strategy defines ways in which rollout of new softwre can proceed.
There are two strategies
* **Recreate**
* **RollingUpdate**

##### Playing with rolling update
* `kubectl rollout status deployments <name>` for status of deployments
* `kubectl rollout pause/resume deployments <name>` for pausing/resuming rollout
* `kubectl rollout history deployment <name>` for history of deployment. This will have two columns: revision and change-cause. to set change-cause use `spec.template.metadata.annotations: kubernetes.io/change-cause: "Cause"`
    * to dig a little bit deeper `kubectl rollout history deployment nginx --revision=2`

#### Deployment Strategies

**Recreate** — equals drop and create. fast and simple, but leads to downtime

**RollingUpdateStategy** gradually replaces old nodes with new ones.
This means that service shouldbe interchangeable compatible with slightly older/newer version of you software.

RollingUpdateStrategy can be parametrized by:
* maxSurge (firstly add additional nodes, install new version on them, then drop old) maxSurge = 100% — example of blue/green deployment.
* maxUnavailable (pick n old nodes and install new version on it, repeat)

Interesting option:

`spec.minReadySeconds` configures how many second you should wait before proceed 
`spec.progressDeadlineSeconds` useful if update done by automatic system. If update takes longer than this option deployment considered failed.
