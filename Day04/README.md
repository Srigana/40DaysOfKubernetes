# The Two Main Pillars of K8s

A Kubernetes cluster is divided into two distinct parts:

### 1. The Control Plane

The Control Plane is the decision-maker. It monitors the cluster and responds to changes (e.g., starting a new container if one crashes).

- **Role:** Manages the worker nodes and the Pods.
- **Production Standard:** In a real-world setup, the Control Plane is usually spread across **multiple computers** for "High Availability"—if one computer fails, the cluster stays alive.
  ### Control Plane Components
  The **Control Plane** is the brain of Kubernetes. While these components can technically run anywhere, they are usually grouped together on dedicated "Master" machines to keep them separate from your actual application workloads.
  ### 1. kube-apiserver
  - The **kube-apiserver** is the secure, scalable "Brain Hub." It is the **only** part of the cluster that talks to the database, the **only** part that validates users, and the **only** part that coordinates the work of all other components.
  - **What it is:** The central management hub.
  - **Role:** Every command you send (via `kubectl` or a UI) goes through the API server.
  - **Key trait:** It is designed to scale horizontally. You can run multiple copies to handle heavy traffic.
  ### 2. etcd
  - **What it is:** A high-availability "key-value" database.
  - **Role:** It stores the **entire state** of your cluster (which nodes exist, what pods are running, secrets, etc.).
  - **Crucial Note:** If you lose etcd data, you lose your cluster configuration. **Always have a backup plan.**
  ### 3. kube-scheduler
  - **What it is:** The component that decides where your apps live.
  - **Role:** It looks for newly created Pods and picks the best **Worker Node** for them to live on.
  - **How it decides:** It checks resource needs (CPU/RAM), hardware constraints, and "affinity" (rules about which apps should stay together or apart).
  ### 4. kube-controller-manager
  - **What it is:** A bundle of "watchdog" processes.
  - **Role:** It constantly monitors the cluster to make sure the _actual_ state matches your _desired_ state.
  - **Key Controllers:**
    - **Node Controller:** Notices if a node stops responding.
    - **Job Controller:** Ensures "one-off" tasks run to completion.
    - **ServiceAccount Controller:** Sets up basic permissions for new namespaces.
  ### 5. cloud-controller-manager
  - **What it is:** The bridge between K8s and cloud providers (AWS, Azure, Google Cloud).
  - **Role:** It handles cloud-specific tasks like creating Load Balancers or checking if a cloud VM has been deleted.
  - **Note:** If you are running K8s on your own private hardware (On-premises), you won't have this component.

### 2. Worker Nodes

Nodes are the actual machines (VMs or physical servers) where your applications live.

- **The Rule:** Every cluster **must** have at least one worker node to actually run an application.
- **Pods:** These are the smallest units in Kubernetes. Your application containers live inside these Pods, which are hosted on the nodes.
  ### Worker Node Components
  ### 1. Kubelet
  The **kubelet** is the most important component on a worker node. It is an agent that talks directly to the Control Plane.
  - **What it does:** It receives a "PodSpec" (a file describing what a Pod should look like) from the API Server.
  - **The Responsibility:** It ensures the containers described in that PodSpec are running and healthy. If a container crashes, the kubelet is the one that notices and restarts it.
  - **Boundary:** It only cares about containers created by Kubernetes. If you manually start a container on the side, the kubelet ignores it.
  ### 2. Container Runtime
  The **Container Runtime** is the software that actually runs the containers. Kubernetes doesn't run containers directly; it tells the runtime to do it.
  - **Standard:** Kubernetes uses the **CRI (Container Runtime Interface)** to talk to different engines.
  - **Common Examples:** \* **containerd** (most common)
    - **CRI-O**
  - **Analogy:** If Kubernetes is a DVD player, the Container Runtime is the motor that actually spins the disc.
  ### 3. kube-proxy
  This is the networking brain of the node.
  - **What it does:** It maintains network rules on each node.
  - **The Purpose:** These rules allow your Pods to talk to each other and allow people from the outside world to reach your app.
  - **How it works:** It usually hooks into the operating system’s "packet filtering layer" (like IP tables) to steer traffic to the right container.
  - **Note:** This is **optional** if you use a modern third-party network plugin that handles its own traffic forwarding.

### Key Component: The Network Proxy

- Every node needs a way to talk to other nodes and the outside world.
- **kube-proxy:** By default, this runs on **every node**. It handles the networking rules so that traffic can reach your application correctly.
- **The Exception:** Some advanced network plugins handle this themselves. In those cases, you might not see `kube-proxy` running.
