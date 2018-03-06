# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 9. DaemonSets

###### DaemonSet vs ReplicaSet
* ReplicaSet — application decoupled from node. You can run multiple independent nodes
* DaemonSet — single copy of your application run on all or subset nodes in the cluster (e. g. distibuted system like Apache Spark)

>Example daemonset configuration:
```
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: fluentd
  namespace: kube-system
  labels:
    app: fluentd
spec:
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
      - name: fluentd
        image: fluent/fluentd:v0.14.10
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
        - name: varlibdockercontainers
          mountPath: /var/lib/docker/containers
          readOnly: true
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log
      - name: varlibdockercontainers
        hostPath:
          path: /var/lib/docker/containers
```

#### Assingning DaemonSet to specific nodes.

Often it's useful to assing daemon sets to specific nodes. This can be archieved by adding label to node.
```
kubectl label nodes k0-default-pool-35609c18-z7tb ssd=true
```