
---

# **Kubernetes Autoscaling & Final Concepts**

---

## **1️⃣ Autoscaling in Kubernetes**

Kubernetes **autoscaling** automatically adjusts **resources or pod count** based on workload or metrics.

### **Types of Autoscaling in K8s**

1. **Horizontal Pod Autoscaler (HPA)**

   * **Scales pods count** based on CPU/memory utilization or custom metrics.
   * Example: Scale from **2 pods → 10 pods** during traffic spikes.

2. **Vertical Pod Autoscaler (VPA)**

   * **Adjusts pod resources** (CPU & memory) automatically.
   * Example: Increase **CPU from 250m → 500m** if under load.

3. **Cluster Autoscaler (CA)**

   * **Adds/removes nodes** from the cluster.
   * Works with cloud providers like **AWS EKS, GCP GKE, Azure AKS**.

---

## **2️⃣ Horizontal Pod Autoscaler (HPA)**

HPA is the most common autoscaling mechanism in Kubernetes.

**Requirements:**

* Metrics Server must be installed.
* Deployment must have **CPU/Memory resource requests defined**.

---

### **2.1 Install Metrics Server**

The **metrics-server** collects cluster-wide **CPU & memory usage** for HPA.

---

#### **Step 1: Download Metrics Server Manifest**

```bash
git clone https://github.com/kubernetes-sigs/metrics-server.git
cd metrics-server
kubectl apply -f manifests/components.yaml
```

OR download single YAML:

```bash
wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

---

#### **Step 2: Update `components.yaml`**

* Open metrics-server **deployment** file:

  ```bash
  nano components.yaml
  ```
* Add these **flags to the container spec** under `args:`:

```yaml
- args:
  - --cert-dir=/tmp
  - --secure-port=4443
  - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
  - --kubelet-insecure-tls
```

**Why?**

* **`--kubelet-insecure-tls`** → Skips kubelet certificate verification.
* **`--kubelet-preferred-address-types`** → Helps connect to kubelet on nodes in cloud or on-prem.

---

#### **Step 3: Apply Metrics Server**

```bash
kubectl apply -f components.yaml
kubectl get pods -n kube-system
kubectl get all -n kube-system
kubectl top nodes
kubectl top pods
```

✅ If you see CPU & Memory usage → Metrics server works.

---

## **3️⃣ Configure HPA (Horizontal Pod Autoscaler)**

### 🧾 Example Deployment (`hpanginx.yml`)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hpanginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: hpanginx
  template:
    metadata:
      labels:
        app: hpanginx
    spec:
      containers:
        - name: nginx
          image: nginx
          ports:
            - containerPort: 80
          resources:
            requests:
              cpu: 100m
            limits:
              cpu: 200m
```

---

### 🔹 Create HPA

```bash
kubectl apply -f hpanginx.yml

# Create HPA: scale between 2 and 10 pods when CPU > 50%
kubectl autoscale deployment hpanginx --cpu-percent=50 --min=2 --max=10

kubectl get hpa
kubectl describe hpa hpanginx
kubectl top pods
```

**Behavior:**

* If **CPU usage > 50%**, HPA **adds pods**.
* If **CPU usage < 50%**, HPA **removes pods** (down to min=2).

---

### 🔹 Test Autoscaling

```bash
kubectl run busybox --image=busybox --restart=Never -it -- sh
while true; do wget -q -O- http://hpanginx; done
```

* Generates load → HPA triggers scaling.
* Watch scaling:

  ```bash
  kubectl get hpa --watch
  kubectl get pods --watch
  ```

---

## **4️⃣ Vertical Pod Autoscaler (VPA)**

* Automatically adjusts **CPU & memory** of pods.
* Not included in default K8s.
* Install **VPA** from official GitHub:

  ```bash
  git clone https://github.com/kubernetes/autoscaler.git
  ```
* Common for **long-running workloads** like databases.

---

## **5️⃣ Cluster Autoscaler (CA)**

* Scales **nodes** up and down.
* Requires **Cloud-managed K8s (EKS, GKE, AKS)** or custom scripts with kubeadm + cloud API.
* Example:

  * **Node low CPU** → scale out new node.
  * **Node idle for 10 mins** → scale in.

---

## **6️⃣ Related & Missed Concepts for Production K8s**

Since this is **your last day of K8s learning**, here are **important topics that complete your Kubernetes knowledge**:

---

### **A) Ingress & TLS (External Access with HTTPS)**

* **Ingress** exposes HTTP/HTTPS routes to services.
* Works with **Ingress Controller** (NGINX, Traefik).

**Example Ingress:**

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myingress
spec:
  rules:
    - host: myapp.example.com
      http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: hpanginx
                port:
                  number: 80
```

---

### **B) RBAC (Role-Based Access Control)**

* Control **who can do what** in the cluster.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: dev
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "list"]
```

```bash
kubectl create rolebinding dev-read --role=pod-reader --user=alice -n dev
```

---

### **C) Resource Quotas & Limits**

* Restrict **resource usage per namespace**.

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-quota
  namespace: dev
spec:
  hard:
    pods: 5
    requests.cpu: 1
    limits.memory: 2Gi
```

---

### **D) Pod Disruption Budget (PDB)**

* Protects **minimum pods running** during maintenance.

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: myapp-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: hpanginx
```

---

### **E) Logging & Monitoring**

* **Logging:** EFK (Elasticsearch, Fluentd, Kibana)
* **Monitoring:** Prometheus + Grafana
* **Tracing:** Jaeger

---

## **✅ Final Kubernetes Learning Path Summary**

| Day | Topic                                |
| --- | ------------------------------------ |
| 01  | Intro, Pods, Nodes, Microservices    |
| 02  | K8s Installation & Cluster Setup     |
| 03  | Pods, YAML, Logs, Exec               |
| 04  | Services (ClusterIP, NodePort, LB)   |
| 05  | ReplicationController & ReplicaSet   |
| 06  | Deployment & Rollout Management      |
| 07  | Health Probes (Liveness, Readiness)  |
| 08  | Namespaces & Volumes                 |
| 09  | ConfigMap & Secrets                  |
| 10  | PV, PVC, DaemonSet, Jobs             |
| 11  | CronJobs, Parallel Jobs, StatefulSet |
| 12  | Autoscaling (HPA, VPA, CA), Metrics  |

---

🎯 **You now have a full Kubernetes skill set** for:

* Deploying apps with HA and scaling
* Persistent storage
* Config management & secrets
* Jobs & CronJobs for automation
* Autoscaling & resource optimization
* Namespaces, RBAC, and production best practices

---

