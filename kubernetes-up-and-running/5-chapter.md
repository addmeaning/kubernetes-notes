# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 5. Pods

Pod — group of containers (and) bind into single, atomic unit.
Pods are smallest deployment unit. All containers in pod lands on same machine

Applications in one Pod shar same IP address and port space, have same hostname.

_You should merge multiple containers in one pod only if those containers can't operate on diferrent machines_

##### Pod manifest
Manifest contain declarative configration of object.

Pod can be created with imperative command `kubectl`.

##### Writing Pod manifest
>TODO: find more information

Manifest can be written in JSON or YAML (preferred)
example manifest
```
apiVersion: v1
kind: Pod
metadata:
  name: kuard
spec:
  volumes:
    - name: "kuard-data"
      hostPath:
        path: "/var/lib/kuard"
  containers:
    - image: gcr.io/kuar-demo/kuard-amd64:1
      name: kuard
      volumeMounts:
        - mountPath: "/data"
          name: "kuard-data"
      ports:
        - containerPort: 8080
          name: http
          protocol: TCP
```


##### Accessing your Pod

Pod can be accessed with `exec` command
```
kubectl exec -it kuard ash
```

##### Health checks
For each container health check can be specified.
There are three types of health check:
* HTTP (200 -> container ok)
* TCP (for non HTTP services like dbs)
* exec

health checks specified in `livenessProbe` section of manifest

here example container
```
 - image: gcr.io/kuar-demo/kuard-amd64:1
      name: kuard
      livenessProbe:
        httpGet:
          path: /healthy
          port: 8080
        initialDelaySeconds: 5
        timeoutSeconds: 1
        periodSeconds: 10
        failureThreshold: 3
      ports:
        ...
```

Resource management

* Minimum request 
* Capping (MAX) Limit

Both specified in manifest.
CPU limits implemented with CPU shares mechanism
Memory can't be deallocated, so Kubernetes will kill and recreate container if uses more memory than available. After reallocation there will be less memory for restarted container (due deployment of new one)
It should be noted to prevent restart on high loads


```
        requests:
          cpu: "500m"
          memory: "128Mi"
        limits:
          cpu: "1000m"
          memory: "256Mi"
```


##### Volumes

```
      volumeMounts:
        - mountPath: "/data"
          name: "kuard-data"
```

Usage of volumes:
* communication/sync
* cache
* persistent data
* mounting the host filesystem

Even remote disk can be mounted as volume
```
volumes:
    - name: "kuard-data"
      nfs:
        server: my.nfs.server.local
        path: "/exports"
```