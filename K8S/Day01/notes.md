---

# 🧠 **Kubernetes – Introduction and Core Concepts**

---

## 🚀 Introduction to Kubernetes

### 📘 **Definition of Kubernetes:**

Kubernetes (often abbreviated as **K8s**) is an **open-source container orchestration platform** that automates the deployment, scaling, and management of **containerized applications**.

It helps run containers (like Docker containers) in **production environments**, efficiently managing resources, uptime, and scalability.

---

## 🔍 Core Features of Kubernetes

---

### 🔁 1) **Auto-Restart Containers**

Kubernetes monitors your applications continuously. If any container inside a **pod** crashes or stops unexpectedly, Kubernetes will automatically restart it to maintain availability.

**How?**
Through the `livenessProbe` and `restartPolicy`, Kubernetes knows when a container is unhealthy and restarts it.

```yaml
restartPolicy: Always
```

---

### ❤️‍🩹 2) **Health Checks (Probes)**

Kubernetes uses two main health checks:

* **Liveness Probe**: Checks if your app is *still running*. If not, it restarts the container.
* **Readiness Probe**: Checks if your app is *ready to serve traffic*. If not, it removes it from the service load balancer.

**Example:**

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
```

---

### 📈 3) **Auto Scaling**

Kubernetes can **scale your application automatically** based on CPU usage, memory, or custom metrics.

#### 🛠 Tool used: **Horizontal Pod Autoscaler (HPA)**

* Increases or decreases the number of **pods** based on workload.
* Keeps performance optimal while minimizing resource usage.

```bash
kubectl autoscale deployment myapp --cpu-percent=50 --min=2 --max=10
```

---

### 🌐 4) **Load Balancing**

Kubernetes provides built-in **load balancing** through **Services**.

* A **Service** is an abstraction layer that distributes network traffic to the right set of pods.
* It ensures that traffic is sent only to **healthy pods** (based on readiness probes).

**Types of Services:**

| Type         | Description                       |
| ------------ | --------------------------------- |
| ClusterIP    | Internal traffic only (default)   |
| NodePort     | Exposes service on each Node's IP |
| LoadBalancer | External load balancer (cloud)    |

---

### 🌉 5) **Networking**

Each pod gets a **unique IP address**. Kubernetes networking ensures:

* All pods can communicate with each other.
* Services expose applications internally and externally.
* DNS-based service discovery.

**Tools:**

* **CoreDNS** – for name resolution
* **CNI Plugins** – for network setup (Flannel, Calico, etc.)

---

## ⚙️ Kubernetes Architecture

Kubernetes follows a **Master-Worker (Control Plane - Node)** architecture.

---

### 👑 Master Node (Control Plane)

Manages and controls the entire Kubernetes cluster.

**Core components:**

| Component          | Description                                  |
| ------------------ | -------------------------------------------- |
| kube-apiserver     | Frontend of the K8s control plane            |
| etcd               | Key-value store for cluster state            |
| kube-scheduler     | Assigns pods to nodes                        |
| controller-manager | Ensures desired state (e.g., replicas, jobs) |

---

### 👷 Worker Node

Hosts your **pods** (application workloads).

**Key components:**

| Component             | Role                            |
| --------------------- | ------------------------------- |
| **kubelet**           | Talks to master, runs pods      |
| **kube-proxy**        | Handles networking for services |
| **Container runtime** | e.g., Docker, containerd        |

---

## 📦 Core Concepts in Kubernetes

---

### 🧱 **Pod**

A **Pod** is the **smallest deployable unit** in Kubernetes.

* A pod wraps one or more **containers**.
* Pods share network and storage.
* Managed by **ReplicaSets** and **Deployments**.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
    - name: myapp-container
      image: nginx
```

---

### 🖥️ **Node**

A **Node** is a physical or virtual machine in the Kubernetes cluster.

* It can be a **master** or a **worker**.
* Worker nodes run your applications.
* Each node runs `kubelet`, `kube-proxy`, and a container runtime.

---

### 🔗 **Microservices Architecture**

Kubernetes is ideal for managing **microservices**.

#### 🧩 What are Microservices?

* Architecture where apps are split into **small, independent services**.
* Each service handles a specific function and can be developed/deployed independently.

**Why K8s + Microservices?**

* Easily deploy separate pods for each service.
* Independent scaling for services.
* Isolated failures (one service can fail without bringing down the whole app).
* Efficient CI/CD pipelines.

---

## 📝 Summary Table

| Concept        | Description                                    |
| -------------- | ---------------------------------------------- |
| Kubernetes     | Container orchestration tool                   |
| Auto-Restart   | Automatically restarts failed containers       |
| Health Checks  | Liveness and Readiness probes                  |
| Auto Scaling   | HPA adjusts pod count based on metrics         |
| Load Balancing | Distributes traffic across pods                |
| Networking     | Enables pod communication and service exposure |
| Master Node    | Controls and manages cluster state             |
| Worker Node    | Runs actual workloads (pods)                   |
| Pods           | Smallest unit in K8s, wraps containers         |
| Nodes          | Machines (VMs or physical) in the cluster      |
| Microservices  | Independent, loosely coupled services          |

---

## ✅ Practice & Homework

### 🔧 Tools:

* [ ] Install **Minikube** or use **Play with Kubernetes** online.
* [ ] Install `kubectl` CLI.

### 📘 Learn Basic Commands:

```bash
kubectl version
kubectl cluster-info
kubectl get nodes
kubectl get pods
```

---
