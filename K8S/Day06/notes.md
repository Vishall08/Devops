
---

## ✅ **What is a Deployment in Kubernetes?**

A **Deployment** ensures that a specified number of pod replicas are running at all times. It automates pod creation, updating, and management.

### ✅ **Key Benefits:**

* Declarative updates
* Rollbacks and revision history
* Scaling (manually or automatically)
* Zero-downtime updates (Rolling Updates)

---

## 📁 Folder Structure

```bash
mkdir deployment
cd deployment
nano mynginx.yml
```

---

## 📝 Deployment YAML File (with correction)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: mydeployment
spec: 
  replicas: 3   # Correct: 'Replica' ➜ 'replicas'
  selector: 
    matchLabels: 
      env: dev
  template:
    metadata:
      labels:
        env: dev
    spec:
      containers:
        - name: nginxcontainer
          image: nginx
          ports:
            - containerPort: 80  # Correct: 'contrainerPort' ➜ 'containerPort'
```

---

## 🔧 Commands

### 🟢 Apply Deployment

```bash
kubectl apply -f mynginx.yml
```

### 📦 Get all resources

```bash
kubectl get deployments
kubectl get all
```

### 🔄 Update the container image (e.g., from nginx to httpd)

```bash
kubectl set image deployment mydeployment nginxcontainer=httpd
```

---

## 📌 Check Rollout Status

```bash
kubectl rollout status deployment mydeployment
```

---

## 📤 Export current deployment

```bash
kubectl get deployment mydeployment -o yaml
```

---

## 📜 View Revision History

```bash
kubectl rollout history deployment mydeployment
```

### 🕰️ Rollback to previous revision

```bash
kubectl rollout undo deployment mydeployment
```

### ⏪ Rollback to specific revision

```bash
kubectl rollout undo deployment mydeployment --to-revision=2
```

---

## 📝 Annotate with Change Cause

```bash
kubectl annotate deployments.apps mydeployment kubernetes.io/change-cause="Updated to httpd"
```

---

## 🔁 Update Again

```bash
kubectl set image deployment/mydeployment nginxcontainer=php
```

---

## ✍️ Enhanced Deployment YAML with **Rolling Update Strategy**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata: 
  name: mydeployment
spec: 
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 2         # Allows up to 2 extra pods during update
      maxUnavailable: 0   # Ensures no pod is unavailable
  selector:
    matchLabels: 
      env: dev
  template:
    metadata:
      labels:
        env: dev
    spec:
      containers:
        - name: nginxcontainer
          image: nginx
          ports:
            - containerPort: 80
```

> **Note:** Rolling updates ensure **zero-downtime deployments** by gradually updating pods.

---

## 🧪 Monitor Deployment

```bash
kubectl get deploy --watch
```

---

## 🚀 Final Check

```bash
kubectl get all
```

---

## 🧠 Summary of Concepts

| Feature           | Description                                 |
| ----------------- | ------------------------------------------- |
| **Deployment**    | Manages replica sets, ensures desired state |
| **ReplicaSet**    | Maintains a stable set of replica Pods      |
| **RollingUpdate** | Gradually updates Pods                      |
| **Revisions**     | Every image change creates a new revision   |
| **Rollback**      | Go back to any previous revision            |
| **Annotations**   | Metadata to record why changes were made    |

---
