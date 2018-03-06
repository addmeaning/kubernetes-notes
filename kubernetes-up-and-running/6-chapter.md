# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 6. Labels
Labels are simple string key value pairs to define metadata for object.

**Rules for pairs**
_keys:_
* label can be broken by slash into prefix and suffix
* prefix must be DNS subdomain, 253 char limit
* suffix 63 char limit
* start with letter, -_. allowed between chars

_values:_
* start with letter, -_. allowed between chars
* 63 chars

_some examples of labels:_
```
app.version                         1.0.0
kubernetes.io/cluster-service       true
```

Labels can be applied with `--labels` flag in `kubectl run` 

Labels for deployments can be shown
```
kubectl get deployments --show-labels
```

Labels are mutable and can be changed

Label can be converted to column with `kubectl get deployments -L <tag>`

##### Selecting
to filter by label pods use `--selector` switch
```
kubectl get pods --selector="ver=2, env=prod, stack in (test, cassandra)"
```

**operators:**
* key=value
* key!=value
* key in (v1, v2)
* key notin (v1, v2)
* key — key set
* !key — key not set


#### Annotations
Place to store metadata.
Annotation used to pass config between external system or to tool itself.

Annotation use cases:
* last update reason info
* comunicate scheduling policy to schedulizer
* last update source/person
* build/release/image information (git hash, timestamp, pr number)
* _Deployment_ object
>TODO learn more about deployment objects
* you can store any data in annotation, for example base64 image for UI of the objec
>TODO learn more about rolling deployments

annotations defined in metadata section
```
...
metadata:
  annotations:
    example.com/icon-url: "https://example.com/icon.png"
...
```