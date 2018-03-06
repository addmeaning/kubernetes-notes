# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 7. Service Discovery

Kubernetes is a very dynamic system. It spins and drops pod. In order to work correctly there should be a system for nodes to find each other — _service discovery_.

**Service discovery** — help solve problem finding processes listening what addresses with what services.
* DNS is a traditional system of service discovery in internate


##### Service object
service can be runned with same command `kubectl run`
```
kubectl run my-prod \
  --image=gcr.io/kuar-demo/kuard-amd64:1 \
  --replicas=3 \
  --port=8080 \
  --labels="ver=1,app=my-prod,env=prod"

kubectl expose deployment my-prod
```

Now servcie will have _cluster IP_.
System will load-balance across all the pods (filtered by selector)

##### Readiness checks

to send traffic only to ready pod you should define `readinessProbe`
```
      containers:
        ...
        name: alpaca-prod
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          periodSeconds: 2
          initialDelaySeconds: 0
          failureThreshold: 3
          successThreshold: 1
```

to get all network endpoint of the service use
`kubectl get endpoints alpaca-prod --watch`

##### NodePorts
Node port help expose port (or port range) from each node to service
node port can be set in service manifest:
```
...
spec:
    ...
    type: NodePort
    ...
```

##### LoadBalancer
>Note: LoadBalancer type can be used when you cloud provider support load balancer. LoadBalancer can by set as type in `spec.type`

##### Advanced Details

###### Endpoints
To describe endpoints use `kubectl describe endpoint <service>`
use `--watch` flag to observe continuous change of endpoint state