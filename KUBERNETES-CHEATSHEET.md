# Kubernetes Quick Reference

A concise reference for common Kubernetes resources and kubectl commands.

## Core Workloads

| Resource | Purpose |
|---|---|
| Pod | Smallest deployable unit; runs one or more containers |
| Deployment | Manages ReplicaSets and supports rolling updates, rollbacks, and scaling |
| ReplicaSet | Maintains the desired number of pod replicas |
| StatefulSet | Manages stateful applications with stable identities and storage |
| DaemonSet | Runs a pod on every eligible node, commonly for logging and monitoring agents |
| Job | Runs a task until it completes successfully |
| CronJob | Runs Jobs on a defined schedule |

## Networking

| Resource | Purpose |
|---|---|
| Service | Provides a stable network endpoint for a set of pods |
| ClusterIP | Exposes a service inside the cluster |
| NodePort | Exposes a service on a port on each node |
| LoadBalancer | Exposes a service through an external load balancer |
| Ingress | Routes HTTP/HTTPS traffic to services using host or path rules |

## Configuration and Storage

- **ConfigMap:** Stores non-sensitive configuration.
- **Secret:** Stores sensitive configuration; enable encryption at rest and restrict access with RBAC.
- **PersistentVolume (PV):** Represents cluster storage.
- **PersistentVolumeClaim (PVC):** Requests storage for a workload.
- **StorageClass:** Defines how storage is dynamically provisioned.

## Scheduling and Availability

- **Node selector:** Constrains a pod to nodes with matching labels.
- **Node affinity:** Provides expressive rules for selecting nodes.
- **Taints and tolerations:** Taints repel pods from nodes; matching tolerations allow pods to be scheduled there.
- **Requests and limits:** Requests influence scheduling; limits cap container resource usage.
- **Liveness probe:** Restarts a container when it is unhealthy.
- **Readiness probe:** Controls whether a pod receives service traffic.
- **Startup probe:** Gives slow-starting applications time to initialize.

## Essential kubectl Commands

```bash
kubectl get nodes
kubectl get pods -A
kubectl get deployments
kubectl get services
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl logs -f <pod-name>
kubectl apply -f deployment.yaml
kubectl delete -f deployment.yaml
kubectl scale deployment <name> --replicas=3
kubectl rollout status deployment/<name>
kubectl rollout history deployment/<name>
kubectl rollout undo deployment/<name>
kubectl exec -it <pod-name> -- sh
kubectl get events --sort-by=.metadata.creationTimestamp
```

## Basic Deployment Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:stable
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 100m
              memory: 128Mi
            limits:
              cpu: 500m
              memory: 256Mi
```

## Troubleshooting Workflow

1. Check pod status: `kubectl get pods -o wide`.
2. Inspect events and conditions: `kubectl describe pod <pod-name>`.
3. Review application output: `kubectl logs <pod-name> --previous` when a container has restarted.
4. Verify service selectors and endpoints: `kubectl get svc,endpoints`.
5. Check resource pressure: `kubectl top nodes` and `kubectl top pods` (requires Metrics Server).

## Useful Habits

- Prefer declarative manifests and version them in Git.
- Set resource requests and limits for application containers.
- Use readiness, liveness, and startup probes appropriately.
- Apply least-privilege RBAC and avoid putting secrets in ConfigMaps.
- Review rollout status and pod events after deployments.
