# The Two Main Pillars of K8s

A Kubernetes cluster is divided into two distinct parts:

### 1. The Control Plane

The Control Plane is the decision-maker. It monitors the cluster and responds to changes (e.g., starting a new container if one crashes).

- **Role:** Manages the worker nodes and the Pods.
- **Production Standard:** In a real-world setup, the Control Plane is usually spread across **multiple computers** for "High Availability"—if one computer fails, the cluster stays alive.
  ### Control Plane Components
  The **Control Plane** is the brain of Kubernetes. While these components can technically run anywhere, they are usually grouped together on dedicated "Master" machines to keep them separate from our actual application workloads.
  ### 1. kube-apiserver
  - The **kube-apiserver** is the secure, scalable "Brain Hub." It is the **only** part of the cluster that talks to the database, the **only** part that validates users, and the **only** part that coordinates the work of all other components.
  - **What it is:** The central management hub.
  - **Role:** Every command we send (via `kubectl` or a UI) goes through the API server.
  - **Key trait:** It is designed to scale horizontally. we can run multiple copies to handle heavy traffic.
  ### 2. etcd
  - **What it is:** A high-availability "key-value" database.
  - **Role:** It stores the **entire state** of our cluster (which nodes exist, what pods are running, secrets, etc.).
  - **Crucial Note:** If we lose etcd data, we lose our cluster configuration. **Always have a backup plan.**
  ### 3. kube-scheduler
  - **What it is:** The component that decides where our apps live.
  - **Role:** It looks for newly created Pods and picks the best **Worker Node** for them to live on.
  - **How it decides:** It checks resource needs (CPU/RAM), hardware constraints, and "affinity" (rules about which apps should stay together or apart).
  ### 4. kube-controller-manager
  - **What it is:** A bundle of "watchdog" processes.
  - **Role:** It constantly monitors the cluster to make sure the _actual_ state matches our _desired_ state.
  - **Key Controllers:**
    - **Node Controller:** Notices if a node stops responding.
    - **Job Controller:** Ensures "one-off" tasks run to completion.
    - **ServiceAccount Controller:** Sets up basic permissions for new namespaces.
  ### 5. cloud-controller-manager
  - **What it is:** The bridge between K8s and cloud providers (AWS, Azure, Google Cloud).
  - **Role:** It handles cloud-specific tasks like creating Load Balancers or checking if a cloud VM has been deleted.
  - **Note:** If we are running K8s on our own private hardware (On-premises), we won't have this component.

### 2. Worker Nodes

Nodes are the actual machines (VMs or physical servers) where our applications live.

- **The Rule:** Every cluster **must** have at least one worker node to actually run an application.
- **Pods:** These are the smallest units in Kubernetes. our application containers live inside these Pods, which are hosted on the nodes.
  ### Worker Node Components
  ### 1. Kubelet
  The **kubelet** is the most important component on a worker node. It is an agent that talks directly to the Control Plane.
  - **What it does:** It receives a "PodSpec" (a file describing what a Pod should look like) from the API Server.
  - **The Responsibility:** It ensures the containers described in that PodSpec are running and healthy. If a container crashes, the kubelet is the one that notices and restarts it.
  - **Boundary:** It only cares about containers created by Kubernetes. If we manually start a container on the side, the kubelet ignores it.
  ### 2. Container Runtime
  The **Container Runtime** is the software that actually runs the containers. Kubernetes doesn't run containers directly; it tells the runtime to do it.
  - **Standard:** Kubernetes uses the **CRI (Container Runtime Interface)** to talk to different engines.
  - **Common Examples:** \* **containerd** (most common)
    - **CRI-O**
  - **Analogy:** If Kubernetes is a DVD player, the Container Runtime is the motor that actually spins the disc.
  ### 3. kube-proxy
  This is the networking brain of the node.
  - **What it does:** It maintains network rules on each node.
  - **The Purpose:** These rules allow our Pods to talk to each other and allow people from the outside world to reach our app.
  - **How it works:** It usually hooks into the operating system’s "packet filtering layer" (like IP tables) to steer traffic to the right container.
  - **Note:** This is **optional** if we use a modern third-party network plugin that handles its own traffic forwarding.

### Key Component: The Network Proxy

- Every node needs a way to talk to other nodes and the outside world.
- **kube-proxy:** By default, this runs on **every node**. It handles the networking rules so that traffic can reach our application correctly.
- **The Exception:** Some advanced network plugins handle this themselves. In those cases, we might not see `kube-proxy` running.
