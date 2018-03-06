# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 13. Storage

Creating Stateful resource can be challenging to several reasons
* additional configuration required that should be bundled into container
* getting DNS resolvable names for individual containers in a replicaSet
* data migration

#### Importing external service
You can gradually introduce kubernetes for you existent infrastructure (e. g. db)
by exposing running db as a service to Kubernetes. It will look like inside service to K8s and then it will be trivial to replace with actual k8s service.

##### Named resource
You can define external resource like
```
kind: Service
apiVersion: v1
metadata:
  name: external-database
spec:
  type: ExternalName
  externalName: database.company.com
```
> Note that this code uses DNS to resolve db name

When you create service, k8s creates DNS service with record that points to that IP

##### IP address
```
kind: Endpoints
apiVersion: v1
metadata:
  name: external-ip-database
subsets:
  - addresses:
    - ip: 192.168.0.1
    ports:
    - port: 3306
```
> You can specify more than one IP address 

#### Health Check of External Service.
There is no health check :)

#### Running MySQL Singleton
to do this we will create:
* persistent volume
* MySQL pod with MySQL application
* service that exposes pod to another containers 

storage:
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: database
  labels:
    volume: my-volume
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 1Gi
  nfs:
    server: 192.168.0.1
    path: "/exports"
```

After we create storage we should specifying access to this persistent volume by claiming persistent volume for Pod
```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: database
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 1Gi
  selector:
    matchLabels:
      volume: my-volume
```

mysql replicaSet:
```
apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  name: mysql
  # labels so that we can bind a Service to this Pod
  labels:
    app: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: database
        image: mysql
        resources:
          requests:
            cpu: 1
            memory: 2Gi
        env:
        # Environment variables are not a best practice for security,
        # but we're using them here for brevity in the example.
        # See Chapter 11 for better options.
        - name: MYSQL_ROOT_PASSWORD
          value: some-password-here
        livenessProbe:
          tcpSocket:
            port: 3306
        ports:
        - containerPort: 3306
        volumeMounts:
          - name: database
            # /var/lib/mysql is where MySQL stores its databases
            mountPath: "/var/lib/mysql"
      volumes:
      - name: database
        persistentVolumeClaim:
          claimName: database
```

and service :)

```
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  ports:
  - port: 3306
    protocol: TCP
  selector:
    app: mysql
```

##### Dynamic Volume Provisioning

when you configure DVP yu can create claim for volume and dynamic provisioner create volume and bind it to claim

definition:
```
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: default
  annotations:
    storageclass.beta.kubernetes.io/is-default-class: "true"
  labels:
    kubernetes.io/cluster-service: "true"
provisioner: kubernetes.io/azure-disk
```

##### Kubernetes-Native Storage with StatefulSets
StatefulSEts are replicated group of pods, simular to ReplicaSet
**Differencies**:
* each replica get hostname with unique index
* replicas created in order from lowest to highest index, creation blocked until previous index healthy
* replicas deleted from hightest to lowest

definition:
```
apiVersion: apps/v1beta1
kind: StatefulSet
metadata:
  name: mongo
spec:
  serviceName: "mongo"
  replicas: 3
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
      - name: mongodb
        image: mongo:3.4.1
        command:
        - mongod
        - --replSet
        - rs0
        ports:
        - containerPort: 27017
          name: peer
```

we need also create serice without cluster virtual IP address

```
apiVersion: v1
kind: Service
metadata:
  name: mongo
spec:
  ports:
  - port: 27017
    name: peer
  clusterIP: None
  selector:
    app: mongo
```

this service will create addresses for all entries in statefulSet
this means that will be created
```
 mongo-0⁠.mongo⁠.default⁠.svc⁠.cluster​.local
 mongo-1⁠.mongo⁠.default⁠.svc⁠.cluster​.local
 mongo-2⁠.mongo⁠.default⁠.svc⁠.cluster​.local
 ```

 then we should telnet to mongo container and create mongo-replica-set and add nodes to it.
 This can be done interactively
 ```
 $ kubectl exec -it mongo-0 mongo
> rs.initiate( {
  _id: "rs0",
  members:[ { _id: 0, host: "mongo-0.mongo:27017" } ]
 });
 OK
> rs.add("mongo-1.mongo:27017");
> rs.add("mongo-2.mongo:27017");
```

or via config file:
```
      - name: init-mongo
        image: mongo:3.4.1
        command:
        - bash
        - /config/init.sh
        volumeMounts:
        - name: config
          mountPath: /config
      volumes:
      - name: config
        configMap:
          name: "mongo-init"
```

##### Persistent Volumes for StatefulSets

because StatefulSet replicates more than one Pod you need to add persistent volume claim template.

```
 volumeClaimTemplates:
  - metadata:
      name: database
      annotations:
        volume.alpha.kubernetes.io/storage-class: anything
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 100Gi
```