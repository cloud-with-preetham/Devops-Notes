# Kubernetes Notes

## 1. What is Kubernetes?

Kubernetes (K8s) is an open-source container orchestration platform used to automate deployment, scaling, and management of containerized applications.

## 2. Architecture

### Control Plane Components

- API Server
- etcd
- Controller Manager
- Scheduler

### Worker Node Components

- Kubelet
- Kube Proxy
- Container Runtime (Docker / containerd)

## 3. Core Concepts

### Pod

- Smallest deployable unit
- Can contain one or more containers

### Deployment

- Manages ReplicaSets
- Provides rolling updates and scaling

### Service

- Exposes Pods
- Types:
  - ClusterIP
  - NodePort
  - LoadBalancer

### Namespace

- Logical isolation within cluster

### ConfigMap & Secret

- ConfigMap: non-sensitive data
- Secret: sensitive data (base64 encoded)

## 4. kubectl Basics

- kubectl get pods
- kubectl describe pod <name>
- kubectl logs <pod>
- kubectl exec -it <pod> -- /bin/sh

## 5. Deployments

- Create: kubectl apply -f deployment.yaml
- Update: kubectl apply -f deployment.yaml
- Delete: kubectl delete -f deployment.yaml

## 6. Services

- Expose: kubectl expose deployment <name> --type=NodePort --port=80

## 7. Debugging

- kubectl get events
- kubectl describe pod
- kubectl logs

## 8. Best Practices

- Use namespaces
- Use labels and selectors
- Keep containers lightweight
- Use liveness/readiness probes
