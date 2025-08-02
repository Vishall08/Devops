
---

# **CronJob, Parallel Jobs & StatefulSet**

---

## **1️⃣ CronJob in Kubernetes**

A **CronJob** runs Jobs **on a schedule**, similar to **Linux cron**.

* **Use Cases:** Backups, reporting, cleanup tasks
* **Syntax:** Cron schedule → `"minute hour day month weekday"`

  * `"*/5 * * * *"` → Runs **every 5 minutes**
  * `"0 0 * * *"` → Runs **every midnight**

---

### 🧾 CronJob YAML (`cronwala.yml`)

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hellojob
spec:
  schedule: "*/5 * * * *"    # Every 5 min
  jobTemplate:
    spec:
      template:
        metadata:
          labels:
            app: hello
        spec:
          containers:
            - name: hello
              image: busybox
              command: ["sh", "-c", "echo Hello, K8s from CronJob! && date"]
          restartPolicy: Never
```

---

### 🔹 Commands to Test CronJob

```bash
kubectl apply -f cronwala.yml
kubectl get cronjob
kubectl get jobs
kubectl get pods
kubectl describe cronjob hellojob
```

> Pods are created automatically every 5 min, and deleted after completion depending on `successfulJobsHistoryLimit`.

---

### 🔹 Troubleshooting CronJob

* **If Pod shows `FailedScheduling`**

  1. Check node resources

     ```bash
     kubectl describe node <node-name>
     free -m
     ```
  2. Clear memory/buffer cache (Linux)

     ```bash
     sync; echo 3 > /proc/sys/vm/drop_caches
     ```
* **Delete all resources**

  ```bash
  kubectl delete all --all --force
  ```

---

## **2️⃣ Parallel Job with Completions & Parallelism**

A **Job** can run **multiple Pods** in parallel or sequentially.

* **`completions`** → Total successful pods required
* **`parallelism`** → How many pods run simultaneously

---

### 🧾 Parallel Job YAML (`jobwala.yml`)

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: hellojob
spec:
  completions: 5        # Run 5 successful jobs
  parallelism: 2        # Run 2 pods at a time
  backoffLimit: 4       # Retry failed pods 4 times
  template:
    metadata:
      labels:
        app: hello
    spec:
      containers:
        - name: hello
          image: busybox
          command: ["sh", "-c", "echo Hello K8s Job && sleep 3"]
      restartPolicy: Never
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

* 5 pods will run.
* 2 pods run in **parallel**.
* Job completes when **5 pods succeed**.

---

## **3️⃣ StatefulSet in Kubernetes**

**StatefulSet** is used for **stateful applications** that require:

1. **Stable Network Identity**

   * Pods get predictable names: `db-0, db-1, db-2`
2. **Stable Persistent Storage**

   * Each replica gets its own **PVC**
3. **Ordered Pod Deployment & Scaling**

   * Pods start/stop **sequentially**

---

### 🧾 Basic StatefulSet YAML (`statewala.yml`)

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: db
spec:
  serviceName: "dbservice"
  replicas: 3
  selector:
    matchLabels:
      app: mydb
  template:
    metadata:
      labels:
        app: mydb
    spec:
      containers:
        - name: mydbcontainer
          image: mysql:8.0
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: root123
  volumeClaimTemplates:
    - metadata:
        name: dbb
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
```

---

### 🔹 Commands

```bash
kubectl apply -f statewala.yml
kubectl get statefulset
kubectl get pods --watch
kubectl describe statefulset db
kubectl get pvc
kubectl get pv
```

**Behavior:**

* Pods will be created sequentially: `db-0 → db-1 → db-2`
* Each pod gets its **own PVC**: `dbb-db-0`, `dbb-db-1`, `dbb-db-2`

---

### 🧾 StatefulSet with ConfigMap for Passwords

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: db
spec:
  serviceName: "dbservice"
  replicas: 3
  selector:
    matchLabels:
      app: mydb
  template:
    metadata:
      labels:
        app: mydb
    spec:
      containers:
        - name: mydbcontainer
          image: mysql:8.0
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                configMapKeyRef:
                  name: mysql-config
                  key: mysql-password
          ports:
            - containerPort: 3306
  volumeClaimTemplates:
    - metadata:
        name: dbb
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
```

---

### 🔹 Key Points About StatefulSet

1. **Headless Service Required**

   * Use `ClusterIP: None` for DNS like `db-0.dbservice`

   ```yaml
   apiVersion: v1
   kind: Service
   metadata:
     name: dbservice
   spec:
     clusterIP: None
     selector:
       app: mydb
     ports:
       - port: 3306
   ```

2. **Storage per Pod**

   * VolumeClaimTemplates ensures **each pod gets its own PVC**.

3. **Scaling**

   ```bash
   kubectl scale statefulset db --replicas=5
   ```

4. **Deletion**

   ```bash
   kubectl delete statefulset db
   kubectl delete pvc --all
   ```

---

## **4️⃣ Related Advanced Concepts**

* **StorageClass for Dynamic PVs**

  ```yaml
  apiVersion: storage.k8s.io/v1
  kind: StorageClass
  metadata:
    name: fast-storage
  provisioner: kubernetes.io/aws-ebs
  parameters:
    type: gp2
  reclaimPolicy: Delete
  ```

  → PV is created automatically when a PVC requests storage.

* **Headless Services with StatefulSet**

  * Required for **Pod DNS resolution**.
  * Access Pods using `podname.servicename.namespace.svc.cluster.local`.

* **Pod Management Policies**

  * **OrderedReady** (default) → sequential
  * **Parallel** → start all pods at once

---

### ✅ **Summary**

| Resource     | Purpose                              | Example Use Case             |
| ------------ | ------------------------------------ | ---------------------------- |
| CronJob      | Scheduled Jobs                       | Nightly backups, log cleanup |
| Parallel Job | Multiple pods for a task             | Batch data processing        |
| StatefulSet  | Stateful apps with stable storage    | Databases, Kafka, Zookeeper  |
| VolumeClaim  | Per Pod Persistent Volume allocation | MySQL, PostgreSQL            |

---

Your **Day11** now includes:

* ✅ Corrected YAMLs
* ✅ CronJob, Job with `parallelism` & `completions`
* ✅ StatefulSet with PV/PVC & ConfigMap usage
* ✅ Added **Headless Service & StorageClass concepts**

---
