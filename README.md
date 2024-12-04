# Tech talk: scaling Kubernetes applications effectively

## Setting up

### Kubernetes

```bash
# Create cluster
kind create cluster
# Switch context
kubectl cluster-info --context kind-kind
```

### Dependencies

```bash
# Install dependencie
kubectl apply -f 0-dependencies.yaml
```

## Actual demo

### Creating a pod

```bash
# Apply Pod manifest
kubectl apply -f 1-pod.yaml

# Get deployed pod
kubectl get pods --selector='app in (demo)'
# Port forward port 8080 on Pod to local machine
kubectl port-forward pod/demo 8080:8080
```

### Convert to a ReplicaSet

```bash
# Clean up environment & deploy ReplicaSet
kubectl delete -f 1-pod.yaml
kubectl apply -f 2-replicaset.yaml

# Get deployed pods
kubectl get pods --selector='app in (demo)'
# Port forward port 8080 on Pod to local machine
kubectl port-forward replicaset/demo 8080:8080
```

### scaling with an HorizontalPodAutoscaler

```bash
# Clean up environment & deploy HorizontalPodAutoscaler
kubectl delete -f 2-replicaset.yaml
kubectl apply -f 3-hpa.yaml

# Get deployed Pods
kubectl get pods --selector='app in (demo)'
# Get HorizontalPodAutoscaler
kubectl get hpa demo
# Port forward port 8080 on Pod to local machine
kubectl port-forward replicaset/demo 8080:8080

# Create Pod that sends a request to the application 100 times a second
kubectl run -i --tty load-generator --rm --image=busybox:1.28 --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://demo:8080; done"
# Get deployed Pods
kubectl get pods --selector='app in (demo)'
# Remove load generator Pod to stop autoscaling
kubectl delete pods load-generator
# Get deployed Pods
kubectl get pods --selector='app in (demo)'
```
