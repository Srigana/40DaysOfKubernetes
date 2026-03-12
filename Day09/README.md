# Day 09 - Kubernetes Namespaces

## Overview

Namespaces provide a way to isolate groups of resources within a single cluster.
They are ideal for separating environments (dev, prod), teams (finance, marketing),
or applications from each other.

---

## Namespaces in This Lab

| Namespace       | Purpose                               | Pods                              |
| --------------- | ------------------------------------- | --------------------------------- |
| `default`       | Baseline — no workloads deployed here | —                                 |
| `dev`           | Development workloads                 | `redis-db`                        |
| `finance`       | Finance team services                 | `payroll`, `redis`                |
| `manufacturing` | Manufacturing workloads               | `red-app`                         |
| `marketing`     | Marketing team services               | `blue`, `redis-db`                |
| `research`      | Research workloads (CrashLoopBackOff) | `dna-1`, `dna-2`                  |
| `kube-system`   | Kubernetes internal components        | coredns, traefik, metrics-server… |

---

## Key Commands

### Viewing Namespaces and Pods

```bash
# List all namespaces
kubectl get namespaces

# Describe all namespaces (metadata, quotas, limits)
kubectl describe namespace

# Get pods in a specific namespace
kubectl get pods --namespace=finance
kubectl get pods -n finance          # shorthand

# Get pods across ALL namespaces
kubectl get pods -A
kubectl get pods --all-namespaces    # same thing
```

### Creating Resources in a Namespace

```bash
# Run a pod in a specific namespace
kubectl run redis --image=redis --namespace=finance

# Apply a YAML manifest into a namespace
kubectl apply -f pod.yaml -n finance
```

## Accessing Services Across Namespaces

### Same Namespace

When a pod calls a service in the **same namespace**, use just the service name:

```bash
# From inside a pod in the 'marketing' namespace
curl http://redis-db
curl http://redis-db:6379
```

### Different Namespace

When calling a service in a **different namespace**, use the full DNS name:

```
<service-name>.<namespace>.svc.cluster.local
```

```bash
# From a pod in 'marketing', accessing 'payroll' service in 'finance'
curl http://payroll.finance.svc.cluster.local

# Shorter form (works within the same cluster)
curl http://payroll.finance
```

## Setting a Default Namespace for Your Session

Set a default:

```bash
kubectl config set-context --current --namespace=finance

# Verify
kubectl config view --minify | grep namespace

# Now this implicitly runs against 'finance'
kubectl get pods
```

---

## Namespace YAML

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

```bash
kubectl apply -f namespace.yaml
# or imperatively:
kubectl create namespace dev
```
