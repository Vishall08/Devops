
---

# **PV, PVC, DaemonSet & Jobs in Kubernetes**

---

## **1️⃣ Persistent Volume (PV) & Persistent Volume Claim (PVC)**

Kubernetes separates **storage provisioning** into two parts:

* **PV (Persistent Volume)**: Cluster-level storage resource created by admins.
* **PVC (Persistent Volume Claim)**: User request to claim storage from a PV.

---

### 📌 PV Key Points:

1. **Access Modes**:

   * `ReadWriteOnce (RWO)` → Can be mounted read-write by a single node.
   * `ReadOnlyMany (ROX)` → Can be mounted read-only by many nodes.
   * `ReadWriteMany (RWX)` → Can be mounted read-write by many nodes.

2. **Reclaim Policies**:

   * `Retain` → PV data is kept after PVC deletion (manual cleanup needed).
   * `Delete` → PV and its storage are deleted automatically when PVC is deleted.
   * `Recycle` *(deprecated)* → Wipes data and reuses PV.

3. **Lifecycle**:

   * `Available` → Ready to be claimed.
   * `Bound` → Linked with a PVC.
   * `Released` → PVC deleted but PV not reused yet.
   * `Failed` → PV cannot be used.

---

### 🧾 PV YAML (`pvwala.yml`)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mypv
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  hostPath:
    path: /mydata
```

---

### 🧾 PVC YAML (`pvcwala.yml`)

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
```

---

### 🧾 Pod Using PVC (`nginxpod.yml`)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginxpod
spec:
  containers:
    - name: mycontainer
      image: nginx
      volumeMounts:
        - mountPath: /usr/share/nginx/html
          name: myvol
  volumes:
    - name: myvol
      persistentVolumeClaim:
        claimName: mypvc
```

---

### 🔹 Commands

```bash
kubectl apply -f pvwala.yml
kubectl get pv

kubectl apply -f pvcwala.yml
kubectl get pvc

kubectl apply -f nginxpod.yml
kubectl get pod
kubectl describe pod nginxpod
```

**Real-world Usage:**

* Store website data, DB data (MySQL, PostgreSQL)
* Works with **hostPath, NFS, EBS, EFS, GCP PD, Azure Disk**

---

## **2️⃣ DaemonSet**

A **DaemonSet** ensures that **one pod runs on every node** in the cluster (or selected nodes via node selectors).

**Use Cases:**

* Node monitoring (Prometheus Node Exporter)
* Log collection (Fluentd, Filebeat)
* Node-level storage agents

---

### 🧾 DaemonSet YAML (`daemonwala.yml`)

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: daemonwala
  labels:
    app: promi
spec:
  selector:
    matchLabels:
      app: promi
  template:
    metadata:
      labels:
        app: promi
    spec:
      containers:
        - name: daemonwala
          image: influxdb
          ports:
            - containerPort: 80
```

---

### 🔹 Commands

```bash
kubectl apply -f daemonwala.yml
kubectl get daemonset
kubectl describe daemonset daemonwala
kubectl get pods -o wide   # Should see pods on all nodes
```

**Key Notes:**

* If a new node is added, DaemonSet **automatically schedules** a pod on it.
* Deleting a node deletes the DaemonSet pod automatically.

---

## **3️⃣ Kubernetes Jobs**

A **Job** runs **one-time tasks** like batch processing or maintenance tasks.

* Runs pod(s) until **completion** (success exit code)
* **retry/backoff limit** can be set for failed pods

---

### 🧾 Job YAML (`jobwala.yml`)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hellojob
spec:
  template:
    metadata:
      labels:
        app: hello
    spec:
      containers:
        - name: hello
          image: busybox
          command: ["sh", "-c", "echo Hello, K8s! && sleep 5"]
      restartPolicy: Never
  backoffLimit: 4
```

---

### 🔹 Commands

```bash
kubectl apply -f jobwala.yml
kubectl get jobs
kubectl get pods
kubectl logs <job-pod-name>
kubectl describe job hellojob
```

**Behavior:**

* Creates a pod that runs the command and then **exits**.
* `restartPolicy: Never` ensures a pod will not restart on success.
* Use `backoffLimit` to limit retries for failed jobs.

---

### 🔹 CronJob (Related Concept)

A **CronJob** runs jobs **on a schedule**, similar to Linux cron.

**Example:**

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hellocron
spec:
  schedule: "*/2 * * * *"   # Every 2 minutes
  jobTemplate:
    spec:
      template:
        spec:
          containers:
            - name: hello
              image: busybox
              command: ["sh", "-c", "date; echo Hello from CronJob"]
          restartPolicy: Never
```

```bash
kubectl apply -f cronjob.yml
kubectl get cronjob
kubectl get jobs --watch
```

---

## **4️⃣ Related Concepts to Add**

1. **StatefulSet (Advanced)**

   * Manages **stateful apps like databases** (MySQL, MongoDB)
   * Provides **stable storage & network identity**
   * Works with **PVCs**

2. **Pod Management Types**

   * **ReplicaSet** → scaling stateless apps
   * **DaemonSet** → node-level pods
   * **Job / CronJob** → batch processing

3. **StorageClass**

   * Automates **dynamic PV provisioning**
   * Example: AWS EBS, GCP PD, Azure Disk

---

## ✅ **Summary Table**

| Resource    | Purpose                     | Example Use Case                |
| ----------- | --------------------------- | ------------------------------- |
| PV/PVC      | Persistent storage          | MySQL DB storage                |
| DaemonSet   | Pod on every node           | Node monitoring, log collection |
| Job         | Run once and exit           | DB backup, batch processing     |
| CronJob     | Scheduled jobs              | Nightly backups, reports        |
| StatefulSet | Stateful pods with identity | Databases, Kafka, Zookeeper     |

---

