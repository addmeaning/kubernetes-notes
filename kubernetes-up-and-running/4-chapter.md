# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 4. Common kubectl commands

In this chapter basic kubernetes API will be covered
##### Namespaces
Namespaces ogranizes objects in the cluster. 
Default kubectl namespace is `default`
to switch namespace you should pass `--namespace` option

##### Contexts
Used for mor permament namespace changes.
Context changes recorded in _$HOME/.kube/config_
This file also stores authentication and cluster address info.

u can create context with another namespace:

```
$ kubectl config set-context <context-name> --namespace=custom-namespace
```

and use it

```
$ kubectl config use-context my-context
```

##### Kubernetes API objects

All objects in Kubernetes has REST reprsentation.
Each object has unique HTTP path.
`kubectl` command makes HTTP request to these URLS to access objects that reside at these paths.

##### CRUD on Kubernetes objects

object represented by JSON or YAML, which are used for CRUD as parameters or outputs.

```
$ kubectl apply -f obj.yaml
```

You can also edit service using interactive mode by typing
```
kubectl edit <resource-name><obj-name>
```

It will download current revision of file from cluster, launch editor and after you save file upload it back to cluster.

for deleting service defined in file you should provide either file

```
kubectl delete -f obj.yaml
```

or object name 
```
kubectl delete <resource-name> <object-name>
```

##### Annotating
to label you object use `kubectl label` command


#### Logs and terminal

Logs can be seen using this command `kubectl logs <pod-name>`

terminal can be accessed by `kubectl exec -it <pod-name> -- /bin/bash`

You also can send i receive files by using `kubectl cp` command

To send:
```
$ kubectl cp /path/to/local/file <pod-name>:/path/to/remote/file
```

To receive:
```
$ kubectl cp <pod-name>:/path/to/remote/file /path/to/local/file
```

