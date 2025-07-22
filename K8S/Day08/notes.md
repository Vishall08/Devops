---

## ✅ **Namespaces & Volumes in Kubernetes**

---

### 📌 **Namespaces in Kubernetes**

Namespaces allow you to divide cluster resources between multiple users. They are like virtual clusters inside a physical cluster and help in organizing and isolating Kubernetes resources.

#### 🧩 Key Commands:

```bash
kubectl get namespaces                             # View all namespaces
kubectl get pods -n kube-system                    # View pods in kube-system namespace
kubectl create namespace mynamespace               # Create a new namespace
kubectl describe namespace mynamespace             # Get details of a namespace
kubectl delete namespace mynamespace               # Delete a namespace

# Set default namespace in the current context
kubectl config set-context --current --namespace=mynamespace
kubectl get pods                                    # Now runs in 'mynamespace'
kubectl config set-context --current --namespace=default
```

#### 🧾 Example: Creating a Custom Namespace via YAML

```yaml
# customname.yml
apiVersion: v1
kind: Namespace
metadata:
  name: aaplanamespace
```

```bash
kubectl apply -f customname.yml
```

#### ✅ Use-case:

Namespaces are useful when you want to separate environments (like dev/test/prod), teams, or projects within a single cluster.

---

## 📦 **Volumes in Kubernetes**

Kubernetes Volumes provide **persistent or shared storage** for containers. A volume's lifecycle is tied to the Pod, but it can outlive individual containers.

---

### 1️⃣ **emptyDir**

* A volume is created when a pod is assigned to a node.
* It is **empty at first** and **deleted** when the pod is deleted.

#### 📘 Use-case:

For temporary data sharing between multiple containers in a pod.

#### 🧾 Example:

```yaml
volumes:
  - name: shared-storage
    emptyDir: {}
```

---

### 2️⃣ **hostPath**

* Maps a **file or directory from the host node’s filesystem** into a pod.
* Useful for testing or debugging.

#### 🧾 Example:

```yaml
volumes:
  - name: host-volume
    hostPath:
      path: /data/app
      type: Directory
```

> ⚠️ Not recommended in production for portability and security reasons.

---

### 3️⃣ **NFS (Network File System)**

* Connects Kubernetes pods to an **external shared filesystem**.
* Requires an NFS server.

#### 🧾 Example:

```yaml
volumes:
  - name: nfs-volume
    nfs:
      server: 192.168.1.100
      path: /nfsdata
```

> ✅ Suitable for sharing files across pods or nodes.

---

### 4️⃣ **PersistentVolume (PV) & PersistentVolumeClaim (PVC)**

#### 📘 Purpose:

Decouple **storage provisioning from pod lifecycle**.

---

#### 🔹 PersistentVolume (PV)

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
  hostPath:
    path: "/mnt/data"
```

---

#### 🔹 PersistentVolumeClaim (PVC)

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

> Pods mount PVCs instead of accessing volumes directly.

---

### 5️⃣ **ConfigMap**

* Used to inject **configuration data** into pods as env vars or files.

#### 🧾 Example:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_MODE: "development"
  LOG_LEVEL: "debug"
```

---

### 6️⃣ **Secret**

* Similar to ConfigMap but used for **sensitive data** like passwords, tokens, or keys.

#### 🧾 Example:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  username: YWRtaW4=       # base64 of 'admin'
  password: cGFzc3dvcmQ=   # base64 of 'password'
```

> Use `echo -n 'admin' | base64` to encode and `base64 -d` to decode.

---

## 📂 Folder & YAML Practice

```bash
mkdir volumeswala
cd volumeswala
```

### ✍️ Create: `twocontainers.yml`

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sharedpod
spec:
  volumes:
    - name: shared-data
      emptyDir: {}
  containers:
    - name: app1
      image: busybox
      command: ['sh', '-c', 'echo Hello from app1 > /data/message; sleep 3600']
      volumeMounts:
        - name: shared-data
          mountPath: /data

    - name: app2
      image: busybox
      command: ['sh', '-c', 'cat /data/message; sleep 3600']
      volumeMounts:
        - name: shared-data
          mountPath: /data
```

> This uses `emptyDir` for both containers to share data.

---

## ☁️ EFS (Elastic File System) on AWS (Bonus Concept)

EFS is AWS’s managed **NFS storage**, highly available and scalable. You can use EFS as Persistent Volume in Kubernetes when hosted on AWS.

#### Use-case:

Shared storage across multiple pods and availability zones.

#### With EFS CSI Driver:

* Create EFS file system
* Use the EFS ID in the PV definition
* Pods mount the EFS volume

---

## ✅ Summary:

| Volume Type        | Lifecycle  | Use-case                             |
| ------------------ | ---------- | ------------------------------------ |
| `emptyDir`         | Pod-level  | Temporary shared storage in Pod      |
| `hostPath`         | Node-level | Dev/test with host filesystem        |
| `nfs`              | External   | Shared file system                   |
| `PersistentVolume` | Cluster    | Stable long-term storage abstraction |
| `ConfigMap`        | Cluster    | Non-sensitive configuration          |
| `Secret`           | Cluster    | Sensitive information like passwords |

---
