---

# 📘 **Working with Pods Using `kubectl` & YAML**

---

## 🎯 **Objectives:**

* Understand `kubectl` pod management commands
* Write and deploy your first Pod YAML (`nginxpod.yml`)
* Learn how to inspect and interact with running pods
* View logs, enter shell, and get extra pod info

---

## 🛠️ **1. Basic `kubectl get` Commands**

```bash
kubectl get pods
```

* Lists all pods in the **default** namespace.

```bash
kubectl get pods -n kube-system
```

* Lists pods in the **`kube-system` namespace**, where core system components (e.g., `coredns`, `kube-proxy`) run.

```bash
kubectl get pods -o wide
```

* Provides **detailed info** like:

  * Node where the pod is scheduled
  * Pod IP
  * Container image
  * Start time

---

## 📂 **2. Create a Directory & YAML File**

```bash
mkdir nginxpod
cd nginxpod/
nano nginxpod.yml
```

---

## 📄 **3. YAML Definition: `nginxpod.yml`**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginxpod
  labels:
    app: myapp
spec:
  containers:
    - name: nginxcontainer
      image: nginx
      ports:
        - containerPort: 80
```

### 🔍 Explanation of Fields:

| Field               | Description                        |
| ------------------- | ---------------------------------- |
| `apiVersion: v1`    | Kubernetes API version             |
| `kind: Pod`         | Resource type                      |
| `metadata.name`     | Pod name                           |
| `metadata.labels`   | Key-value label used for selection |
| `spec.containers`   | List of containers in the pod      |
| `image: nginx`      | Docker image used                  |
| `containerPort: 80` | Exposes port 80 in the container   |

---

## 🚀 **4. Deploy the Pod**

```bash
kubectl apply -f nginxpod.yml
```

✅ This creates the pod named **nginxpod** in the default namespace.

---

## 🔍 **5. Inspecting Pod Status and Info**

### ✅ View Pod Status

```bash
kubectl get pods
```

### 📘 View Pod with More Info

```bash
kubectl get pods -o wide
```

**Additional Info:**

| Column   | Meaning                                 |
| -------- | --------------------------------------- |
| READY    | Ready containers / Total containers     |
| STATUS   | Pod state (Running, Pending, etc.)      |
| RESTARTS | Number of restarts (due to crash, etc.) |
| AGE      | How long the pod has been running       |
| IP       | Internal Pod IP                         |
| NODE     | Which worker node it's running on       |

---

## 🔬 **6. Describe the Pod in Detail**

```bash
kubectl describe pod nginxpod
```

You’ll see:

* Events (like image pulling, scheduled)
* Conditions
* Mounted volumes
* Container status

---

## 💻 **7. Execute Commands Inside the Pod (Interactive Shell)**

```bash
kubectl exec -it nginxpod -- /bin/bash
```

This opens a **shell inside the container** running in the pod.

You can now run Linux commands like:

```bash
apt update
cat /etc/os-release
exit
```

---

## 📋 **8. View Logs from the Pod**

```bash
kubectl logs nginxpod
```

Outputs the **stdout logs** from the container — useful for debugging.

---

## 🧼 Optional Cleanup

```bash
kubectl delete pod nginxpod
```

---

## ✅ Summary Table

| Command                | Description                  |
| ---------------------- | ---------------------------- |
| `kubectl get pods`     | Lists all pods               |
| `-n kube-system`       | Targets system namespace     |
| `-o wide`              | Shows detailed pod info      |
| `kubectl apply -f`     | Applies a YAML definition    |
| `kubectl describe pod` | Shows full pod details       |
| `kubectl exec -it`     | Opens shell in pod container |
| `kubectl logs`         | Displays container logs      |
| `kubectl delete pod`   | Deletes the pod              |

---

## 🧪 Homework/Practice Tasks

1. Modify the pod to use a custom label or port.
2. Try running a pod with another image like `httpd`, `busybox`, or `alpine`.
3. Practice `kubectl describe` to understand events and debug failures.
4. View logs for different scenarios (like image not found).

---


