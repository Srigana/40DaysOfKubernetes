# Day 08 - Kubernetes Services

## Overview

This day covers **Kubernetes Services** — how they expose pods to network traffic, route requests using label selectors, and how to use `NodePort` services with `kind` clusters that have port mappings configured.

---

## Concepts Covered

### What is a Kubernetes Service?
A Service is a stable network abstraction that sits in front of a set of pods. Since pod IPs are ephemeral (they change on restarts or rescheduling), a Service provides a consistent IP and DNS name, and uses **label selectors** to route traffic to matching pods.

### Service Types Used
| Type | Description |
|------|-------------|
| `ClusterIP` | Default. Internal-only. Accessible within the cluster. |
| `NodePort` | Exposes the service on a static port (30000–32767) on each node. |

---

## Files

| File | Description |
|------|-------------|
| `nodeport.yaml` | NodePort service targeting pods with label `env=demo` |
| `deployment.yaml` | Nginx deployment with label `app=myapp` |
| `service.yaml` | NodePort service targeting the `myapp` deployment |
| `kind.yaml` | kind cluster config with port mappings for NodePort access from localhost |

---

## Key Lessons & Mistakes

### kind Clusters Need Explicit Port Mappings for NodePort Access
With a plain kind cluster (no port mappings), `curl localhost:<NodePort>` fails because Docker doesn't forward that port to your host.

The fix is to define port mappings in `kind.yaml` before creating the cluster:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 30001
        hostPort: 30001
      - containerPort: 30008
        hostPort: 30008
```

Then create the cluster:
```bash
kind create cluster --config kind.yaml --name cka-cluster2
```

After this, `curl localhost:30001` works from Mac.

---

## Commands Used

```bash
# View service API spec
kubectl explain svc

# Show pod labels
kubectl get pods --show-labels

# Create a service from YAML
kubectl create -f nodeport.yaml

# View all services
kubectl get svc

# Inspect a service (selector, endpoints, ports)
kubectl describe svc nodeport-svc

# Check endpoints for a service
kubectl get ep nodeport-svc

# Scale a deployment
kubectl scale --replicas=2 deploy/myapp

# Run a temp pod to test internal DNS / ClusterIP
kubectl run temp-pod --image=busybox -- /bin/sh -c "sleep 3600"
kubectl exec -it temp-pod -- /bin/sh
# Inside pod:
wget http://myapp

# Delete temp pod
kubectl delete pod temp-pod

# Test NodePort from host (requires kind port mapping)
curl localhost:30001
curl localhost:30008
```

---

## Working NodePort Service Example

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
    - port: 80          # Service port (ClusterIP)
      targetPort: 80    # Container port 
      nodePort: 30008   # External port (30000–32767)
```

---

## Debugging Checklist

If your NodePort service isn't working, check in order:

1. **Endpoints are populated?** → `kubectl get ep <svc>`
2. **Selector matches pod labels?** → `kubectl describe svc <svc>` vs `kubectl get pods --show-labels`
3. **TargetPort matches container port?** → Check the deployment/pod spec
4. **Port mapping exists in kind config?** → `docker ps` to confirm host port forwarding
5. **Correct cluster context?** → `kubectl config current-context`

---

## References

- [Kubernetes Services Docs](https://kubernetes.io/docs/concepts/services-networking/service/)
- [kind Ingress / Port Mapping Guide](https://kind.sigs.k8s.io/docs/user/configuration/#extra-port-mappings)
