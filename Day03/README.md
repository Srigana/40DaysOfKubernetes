# What is Kubernetes?

Kubernetes (often abbreviated as K8s) is an open-source container orchestration platform designed to automate the deployment, scaling, and management of containerized applications.

## Challenges of Stand Alone Applications

1. Manual Recovery
2. Scaling Difficulties
3. Networking Complexity
4. Resource Management
5. Update Downtime

## Key Features of K8s

Kubernetes manages the "lifecycle" of your containers so you don't have to do it manually.

- **Self-Healing:** If a container crashes, K8s restarts it. If a node dies, it replaces the containers on a new node.
- **Service Discovery & Load Balancing:** K8s gives containers their own IP/DNS and balances traffic so no single container gets overwhelmed.
- **Automated Rollouts/Rollbacks:** You describe the "desired state" (e.g., "I want 3 copies of App v2"), and K8s handles the transition. If something breaks, you can roll back instantly.
- **Storage Orchestration:** Automatically attaches storage (Cloud, local, etc.) to your apps.
- **Bin Packing:** You tell K8s how much CPU/RAM an app needs; K8s finds the best spot for it to maximize resource use.
- **Secret Management:** Securely handles passwords and keys without putting them in your application code.
