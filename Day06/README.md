# Kubernetes Pod Management & Troubleshooting Reference

This repository serves as a technical reference for managing Kubernetes Pods. It covers imperative and declarative workflows, manifest extraction, and systematic troubleshooting of common deployment errors.

---

## 1. Methodologies for Creating Pods

Kubernetes supports two primary patterns for resource management:

1.  **Imperative Way**: Executed via direct CLI commands or API calls. Best for rapid testing and one-off tasks.
2.  **Declarative Way**: Managed through manifest files (YAML). This is the industry standard for production as it allows for version control and infrastructure-as-code (IaC) automation.

---

## 2. Command Reference & Technical Explanation

| Command                                 | Technical Purpose                                                                                                      |
| :-------------------------------------- | :--------------------------------------------------------------------------------------------------------------------- |
| `kubectl run <name> --image=<img-name>` | **Imperative Creation**: Directly creates a Pod object in the cluster.                                                 |
| `kubectl create -f pod.yaml`            | **Direct Creation**: Creates a resource from a file; fails if the resource already exists.                             |
| `kubectl apply -f pod.yaml`             | **Declarative Management**: Synchronizes the cluster state with the file. Highly recommended for updates (Idempotent). |
| `kubectl get pods -o wide`              | **Extended Observability**: Shows Pod status, IP addresses, and Node assignments.                                      |
| `kubectl describe pod <name>`           | **Deep Inspection**: Displays lifecycle events, configurations, and detailed error logs.                               |
| `kubectl edit pod <name>`               | **Live Mutation**: Opens the active manifest in a terminal editor for real-time configuration changes.                 |
| `kubectl get nodes -o wide`             | **Infrastructure View**: Provides details on the health and internal IPs of the cluster nodes.                         |
| `--dry-run=client -o yaml`              | **Local Validation**: Generates a YAML template locally without deploying to the cluster.                              |

---

## 3. Practical Tasks & Workflows

### Task 1: Imperative Pod Creation

Create a pod named `nginx` using the `nginx` image.

```bash
kubectl run nginx --image=nginx
```

### Task 2: Manifest Extraction and Duplication

Generate a YAML from an existing pod, modify it, and deploy a new instance.

1. Export existing config to file:

```bash
kubectl get pod nginx -o yaml > newpod.yaml
```

2. Modify the file: Open `newpod.yaml` and update `metadata.name` to `nginx-new`

3. Deploy the new resource:

```bash
kubectl apply -f newpod.yaml
```
