
---

## ✅ ReplicationController & ReplicaSet

---

#### 🔁 ReplicationController Overview:

* Ensures that a specified number of pod replicas are running at all times.
* Performs **self-healing** (recreates pods if they crash).
* Supports **manual scaling** using `kubectl scale`.
* Generally replaced by ReplicaSet (which supports advanced label selectors).

---

#### 🧹 Clean Up Environment First

```bash
kubectl delete all --all --force
```

---

### 📁 Create ReplicationController YAML

#### 📌 File: `mynginx.yml`

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myrc
spec:
  replicas: 3
  selector:
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

---

#### ✅ Commands

```bash
kubectl apply -f mynginx.yml
kubectl get rc
kubectl get pods
kubectl scale rc myrc --replicas=6
kubectl get pods -l env=dev
kubectl delete rc myrc --force
kubectl get rc -o wide
kubectl delete all --all --force
```

---

### 📁 Additional RC Files

#### 📌 File: `yournginx.yml`

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myrc1
spec:
  replicas: 3
  selector:
    env: prod
  template:
    metadata:
      labels:
        env: prod
    spec:
      containers:
        - name: nginxcontainer
          image: nginx
          ports:
            - containerPort: 80
```

#### 📌 File: `ournginx.yml`

```yaml
apiVersion: v1
kind: ReplicationController
metadata:
  name: myrc2
spec:
  replicas: 3
  selector:
    env: testing
  template:
    metadata:
      labels:
        env: testing
    spec:
      containers:
        - name: nginxcontainer
          image: nginx
          ports:
            - containerPort: 80
```

---

### ✅ Apply All RCs

```bash
kubectl apply -f yournginx.yml
kubectl apply -f ournginx.yml
kubectl get pods -l env=prod
kubectl get pods -l env=testing
```

---

### 🔁 ReplicaSet Overview:

* Advanced version of RC.
* Supports **label selectors** like `matchLabels`, `matchExpressions`.
* Commonly used by **Deployments** in modern K8s.

---

### 📁 Create ReplicaSet YAMLs

#### 📌 File: `mynginx.yml`

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myrs
spec:
  replicas: 3
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

✅ Repeat the same structure for:

* `yournginx.yml` – change name to `myrs1`, label `env: prod`
* `ournginx.yml` – change name to `myrs2`, label `env: testing`

---

### ✅ Useful Commands

```bash
kubectl apply -f mynginx.yml
kubectl apply -f yournginx.yml
kubectl apply -f ournginx.yml

kubectl get replicasets
kubectl get pods -l env=dev
kubectl get pods --selector='env in (dev, prod, testing)'
```

---
