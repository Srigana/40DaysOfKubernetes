# ☸️ Kubernetes Multi-Node Cluster with kind

This project demonstrates how to provision local Kubernetes clusters using [kind](https://kind.sigs.k8s.io/) (Kubernetes IN Docker).

---

## 🛠 1. Installation

### Install kind

On macOS, use Homebrew to install the kind binary:

```bash
brew install kind
```

### Verify Installation

Confirm that kind is installed and check the version:

```bash
kind version
```

## 🏗 2. Cluster Creation

**Default Cluster**

To create a standard single-node cluster:

```bash
kind create cluster
```

**Custom Cluster (Specific Image & Name)**

Use this command to specify a Kubernetes version (via node image) and a custom cluster name:

```bash
kind create cluster \
  --name kind-cluster1 \
  --image kindest/node:v1.34.3@sha256:08497ee19eace7b4b5348db5c6a1591d7752b164530a36f855cb0f2bdcbadd48
```

## 📂 3. Multi-Node Configuration

To simulate a production environment with multiple workers, use a YAML configuration file.

**Create `config.yaml`**

```bash
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
```

**Apply Configuration**

```bash
kind create cluster \
  --name kind-cluster2 \
  --config config.yaml \
  --image kindest/node:v1.34.3@sha256:08497ee19eace7b4b5348db5c6a1591d7752b164530a36f855cb0f2bdcbadd48
```

## 🚦 4. Management & Contexts

| Action         | Command                                        |
| :------------- | :--------------------------------------------- |
| List Clusters  | `kind get clusters`                            |
| Check Contexts | `kubectl config get-contexts`                  |
| Switch Context | `kubectl config use-context kind-cluster1`     |
| Cluster Info   | `kubectl cluster-info --context kind-cluster1` |

## 🧹 5. Cleanup

To delete a cluster and free up Docker resources:

```bash
kind delete cluster --name kind-cluster1
```
