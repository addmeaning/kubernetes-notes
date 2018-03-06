# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 11. ConfigMaps and Secrets

ConfigMaps and Secrets are helping make containers reusable across dev/staging/prod 

#### ConfigMaps
Can be created key value file.
######Syntax:
```
#comment
param1 = value1
param2 = value2
```

######Application
```
kubectl create configmap my-config \
  --from-file=my-config.txt \
  --from-literal=extra-param=extra-value \
  --from-literal=another-param=another-value
```

######Fetching
```
kubectl get configmaps my-config -o yaml
```

#### Secrets
While configMap great for most config data, there are passwords, keys and other extra-sensitive information.

###### Creating
```
kubectl create secret generic kuard-tls \
  --from-file=kuard.crt \
  --from-file=kuard.key
```
###### Consuming
Example of configuration that uses volume mount to store secret
```
apiVersion: v1
kind: Pod
metadata:
  name: kuard-tls
spec:
  containers:
    - name: kuard-tls
      image: gcr.io/kuar-demo/kuard-amd64:1
      imagePullPolicy: Always
      volumeMounts:
      - name: tls-certs
        mountPath: "/tls"
        readOnly: true
  volumes:
    - name: tls-certs
      secret:
        secretName: kuard-tls
```
>TODO what about string pass?


#### Private Docker repos
_Image pull secrets_ represent auth details for private repo.
In config can be found under `spec.imagePullSecrets`
```
kubectl create secret docker-registry my-image-pull-secret \
  --docker-username=<username> \
  --docker-password=<password> 
```

#### Naming 

Keys should match `[.]?[a-zA-Z0-9]([.]?[-_a-zA-Z0-9]*[a-zA-Z0-9])*`

#### Replace and update
Secret can be replaced using
```
kubectl create secret generic kuard-tls \
  --from-file=kuard.crt --from-file=kuard.key \
  --dry-run -o yaml | kubectl replace -f -
```
configMaps can be edit by `kubectl edit`