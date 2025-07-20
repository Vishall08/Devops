---

## ✅ **Kubernetes – Services Explained**

---

### 📌 **What is a Service in Kubernetes?**

A **Service** in Kubernetes is a way to expose a group of **Pods** to internal or external network traffic. Pods in Kubernetes are **ephemeral**—they can go down and restart with new IPs. Services provide a **stable endpoint (IP/DNS)** to access those pods.

---

### 📁 **Types of Services in Kubernetes**

#### 🔹 1. **ClusterIP** (default)

* **Used for internal communication** within the cluster.
* Not accessible from outside the Kubernetes cluster.
* Use case: When backend pods talk to other backend pods or databases.

🔧 Example:

```yaml
type: ClusterIP
```

#### 🔹 2. **NodePort**

* Exposes the service on **each Node’s IP at a static port (30000–32767)**.
* Can be accessed **outside the cluster** via `NodeIP:NodePort`.

🔧 Example:

```yaml
type: NodePort
nodePort: 30001
```

#### 🔹 3. **LoadBalancer**

* Exposes the service externally using a **cloud provider’s load balancer**.
* Automatically provisions an external IP.
* Best for production environments (e.g., AWS ELB, GCP LoadBalancer).

🔧 Example:

```yaml
type: LoadBalancer
```

---

## 🛠️ YAML File Explanations

### 🔸 `myservice.yml` (NodePort Example)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mynodeportservice
spec:
  type: NodePort
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30001
```

#### 🔍 Breakdown:

* `type: NodePort` → Exposes pod on port 30001 of the node.
* `selector: app: myapp` → Targets pods with label `app: myapp`.
* `port: 80` → Port exposed by the service internally.
* `targetPort: 80` → Port where the container listens (defined in pod).

👉 Access this pod using: `http://<NodeIP>:30001`

---

### 🔸 `newnginxpod.yml` (Pod with Label)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: newnginxpod
  labels:
    app: myapp
spec:
  containers:
    - name: nginxcontainer
      image: nginx
      ports:
        - containerPort: 80
```

#### 🔍 Breakdown:

* This pod has the label `app: myapp`, so it's matched by the service above.
* The container is running the NGINX web server.

---

### 🔸 `loadwalaservice.yml` (LoadBalancer Example)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: loadwalaservice
spec:
  type: LoadBalancer
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

#### 🔍 Breakdown:

* Exposes your pods to the **internet via cloud provider load balancer**.
* Internally routes to all matching pods with label `app: myapp`.
* Automatically assigns an external IP (in cloud environments).

🧪 **In Minikube or Bare-metal cluster**, LoadBalancer won't assign an external IP automatically. You may need to use **MetalLB** or port-forwarding for simulation.

---

## 🔧 Useful Kubernetes Commands

| Command                       | Description                                       |
| ----------------------------- | ------------------------------------------------- |
| `kubectl apply -f <file>.yml` | Deploys the resource defined in the YAML          |
| `kubectl get services`        | Lists all services                                |
| `kubectl get pods`            | Lists all pods                                    |
| `kubectl get all`             | Lists all resources (pods, services, deployments) |
| `kubectl get endpoints`       | Shows which pod IPs are linked to each service    |

---

## 🧠 Important Concepts

### ✅ Labels and Selectors

* Labels: Key-value pairs attached to Kubernetes objects.
* Selectors: Services use them to find which pods to route traffic to.

> Example: Pods with `app: myapp` are selected by services that also use `selector: app: myapp`.

---

### ✅ Ports Explained

* `port`: Port exposed by the Service.
* `targetPort`: Port inside the container (pod).
* `nodePort`: Port on the Node (only for NodePort services).

---

### 📚 Summary for Day 04

| Feature         | ClusterIP           | NodePort                     | LoadBalancer          |
| --------------- | ------------------- | ---------------------------- | --------------------- |
| Internal Access | ✅                   | ✅                            | ✅                     |
| External Access | ❌                   | ✅ (via IP\:port)             | ✅ (via LB IP)         |
| Usage           | Internal app-to-app | Local testing or static port | Production with cloud |

---


