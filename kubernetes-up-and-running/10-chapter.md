# Kubernetes
### Notes from Kubernetes: Up and Running
#### chapter 10. Jobs

Unlike services Jobs provide way to to one-off task. Normally Kubernetes will restart node that exit with 0.
Example of Jobs
* Batch transformation
* DB migration

##### Job object
responsible for managing pods defined in job spec.
This pod run until successful completion.
If pod fail Job specification will try to recreate pod

##### Job Patterns
jobs are designed to manage batch-like workloads, where work items are processed by one or more pods.
Job pattern specifies:
* number of jub completions
* number of pods to run in parralel

##### Job Types
* **One shot** (single pod, until successful termination)
* **Parallel fixed completions** (one or many pod, one or more time, fixed completition count)
* **Work queue** (one or more pods, until successful termination)

##### One shot
Simple way run pod until successful completion.
Example run command for one shot job:
```
kubectl run -i oneshot \
  --image=gcr.io/kuar-demo/kuard-amd64:1 \
  --restart=OnFailure \
  -- --keygen-enable \
     --keygen-exit-on-complete \
     --keygen-num-to-gen 10
```
option: `-i` interactive

Example config for one shot job
```
apiVersion: batch/v1
kind: Job
metadata:
  name: oneshot
  labels:
    chapter: jobs
spec:
  template:
    metadata:
      labels:
        chapter: jobs
    spec:
      containers:
      - name: kuard
        image: gcr.io/kuar-demo/kuard-amd64:1
        imagePullPolicy: Always
        args:
        - "--keygen-enable"
        - "--keygen-exit-on-complete"
        - "--keygen-num-to-gen=10"
      restartPolicy: OnFailure
```

##### Parallelism
Parallel execution can be achieved by tuning `completitions` and `parallelism` parameter.