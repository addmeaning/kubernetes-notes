kubectl run akka-hello --image=addmeaning/sparkathon-docker-hello:1.0 --port=8080
kubectl get pods
kubectl expose deployment akka-hello --type=LoadBalancer --name akka-http
kubectl get svc --watch
kubectl scale deployment akka-hello --replicas=3
kubectl get pods
