
---

# 📘 **Kubernetes - Complete Guide**

---

## 🧠 **Introduction to Kubernetes**

### 📖 **Definition:**

**Kubernetes** (K8s) is an **open-source container orchestration platform** for **automating** the deployment, scaling, and management of **containerized applications**.

It was developed by **Google**, now maintained by the **Cloud Native Computing Foundation (CNCF)**.

---

## 🌟 **Key Features of Kubernetes**

---

### 🔁 1. **Auto-Restart Containers**

If a container crashes or becomes unresponsive, Kubernetes automatically restarts it to ensure the application remains available.

→ Handled via **Liveness Probes** and `restartPolicy`.

---

### ❤️‍🩹 2. **Health Checks**

Kubernetes performs regular health checks using **probes**:

* **Liveness Probe** – Is the app running?
* **Readiness Probe** – Is it ready to serve traffic?

---

### 📈 3. **Auto Scaling**

With **Horizontal Pod Autoscaler (HPA)**, Kubernetes can increase or decrease the number of pods based on resource usage like CPU.

```bash
kubectl autoscale deployment myapp --cpu-percent=50 --min=1 --max=10
```

---

### ⚖️ 4. **Load Balancing**

Kubernetes distributes network traffic across multiple pods using **Services** to ensure stability and high availability.

---

### 🌐 5. **Networking**

Each pod in Kubernetes gets a unique IP, allowing pod-to-pod communication. **CoreDNS** enables internal DNS for service discovery.

---

### 🧠 6. **Auto Node Allocation**

When you deploy a pod, Kubernetes automatically schedules (places) it on a **suitable node** based on available CPU, memory, and policies.

This is done by the **Scheduler** component of the Master Node.

---

## ⚙️ **Kubernetes Architecture**

### 👑 **Master Node (Control Plane)**

Controls the entire cluster.

* **API Server** – Entry point for commands (`kubectl`)
* **Scheduler** – Assigns pods to nodes
* **Controller Manager** – Maintains cluster state
* **etcd** – Key-value store (cluster data)

---

### 👷 **Worker Node**

Runs the actual **applications** in **Pods**.

* **kubelet** – Manages pods on the node
* **kube-proxy** – Handles networking
* **Container Runtime** – Runs containers (e.g., containerd, Docker)

---

### 🔲 **Pods**

Smallest deployable unit. A pod wraps one or more containers that share:

* Network
* Storage
* Lifecycle

---

### 🧱 **Nodes**

A node is a **VM or physical server** part of the Kubernetes cluster (either master or worker node).

---

### 🧩 **Microservices Architecture**

Kubernetes supports microservices by enabling:

* Independent deployment of services
* Individual scaling
* Service discovery and load balancing

Each microservice can run in its own pod, service, and deployment.

---

## 🔄 **Kubernetes Release Management**

Kubernetes supports:

* **Rolling Updates**: Gradually update applications with zero downtime.
* **Rollbacks**: Instantly revert to a previous stable state if the new version fails.

```bash
kubectl rollout undo deployment myapp
```

---

## 📦 **Kubernetes Objects (Resources)**

Kubernetes manages applications using **API objects**, declared in YAML or JSON files.

---

### 🔹 1. **Pod**

A wrapper around containers.

---

### 🔹 2. **ReplicaSet**

Ensures a specified number of pod replicas are running at any given time.

> 🔄 **Better than ReplicationController (RC)** which is outdated and doesn't support rollback.

---

### 🔹 3. **ReplicationController (Legacy)**

* Old method of ensuring specified pod count.
* Lacks modern update/rollback features.

---

### 🔹 4. **Deployment**

Most common way to manage stateless applications.

* Automatically manages ReplicaSets
* Supports rolling updates and rollbacks

```yaml
kind: Deployment
```

---

### 🔹 5. **StatefulSet**

Manages **stateful applications** like databases.

* Pods have **stable identities** and **persistent storage**
* Ordered deployment and scaling

Example: MongoDB, MySQL

---

### 🔹 6. **DaemonSet**

Ensures that **one copy of a pod runs on every node**.

Use case: Logging agents, monitoring agents, etc.

---

## 🌐 **Services in Kubernetes**

---

### 🔸 1. **ClusterIP (default)**

* Internal service within the cluster
* Not accessible from outside

---

### 🔸 2. **NodePort**

* Exposes the service on a static port on each Node
* Accessible from outside using NodeIP\:Port

---

### 🔸 3. **LoadBalancer**

* Provisions an **external IP** using cloud provider's load balancer (AWS ELB, GCP LB, etc.)

---

## 🗃️ **Other Kubernetes Objects**

---

### 📁 3. **Volume**

Kubernetes **Volumes** provide persistent or ephemeral storage for containers.

* Local volumes
* PersistentVolumes (PV) and PersistentVolumeClaims (PVC)

---

### 🌐 4. **Network**

Handles communication:

* **Pod-to-Pod**
* **Service Discovery**
* **Ingress/Egress control**
* Uses CNI (Container Network Interface) plugins

---

### ⚙️ 5. **ConfigMap**

Stores **non-sensitive** configuration as key-value pairs, injected into pods.

Example use: App settings, URLs, filenames

```yaml
apiVersion: v1
kind: ConfigMap
```

---

### 🔒 6. **Secret**

Stores **sensitive data** like passwords, tokens, certificates.

* Encrypted at rest
* Mounted as files or injected as env variables

```yaml
apiVersion: v1
kind: Secret
```

---

### 🌐 7. **Ingress**

Manages **external access** to services (usually HTTP/HTTPS).

* Works like a reverse proxy
* Can route based on hostname/path

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
```

---

## ✅ Summary Table

| Concept              | Description                                     |
| -------------------- | ----------------------------------------------- |
| Kubernetes           | Container orchestration system                  |
| Auto-Restart         | Restarts failed containers automatically        |
| Health Check         | Monitors container health using probes          |
| Auto Scaling         | Adjusts pod count based on load                 |
| Load Balancing       | Distributes traffic across pods                 |
| Auto Node Allocation | Schedules pods to available nodes               |
| Pod                  | Smallest unit with containers                   |
| ReplicaSet           | Ensures desired pod count                       |
| Deployment           | Manages stateless apps with updates/rollbacks   |
| StatefulSet          | For stateful apps with storage needs            |
| DaemonSet            | Runs one pod per node                           |
| Services             | Expose apps (ClusterIP, NodePort, LoadBalancer) |
| Volume               | Persistent or shared storage                    |
| ConfigMap            | Stores app configurations                       |
| Secret               | Stores sensitive credentials                    |
| Ingress              | HTTP routing from outside the cluster           |

---
