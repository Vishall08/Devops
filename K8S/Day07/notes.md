---

## 📘 **Health Probes in Kubernetes**

Health probes allow Kubernetes to determine the **health and readiness** of containers. They help ensure that traffic is only routed to **healthy and ready** Pods, and that unresponsive containers are restarted or prevented from receiving traffic.

---

### 🔍 **Why Use Probes?**

* Helps **Kubelet** detect when to:

  * **Restart** a container (if it's unhealthy)
  * **Stop sending traffic** (if it's not ready)
  * **Wait before sending traffic** (during startup)
* Improves **reliability**, **availability**, and **autoscaling accuracy**

---

### 🔄 **Types of Probes**

| Probe Type          | Purpose                                       | Result                                      |
| ------------------- | --------------------------------------------- | ------------------------------------------- |
| **Liveness Probe**  | Checks if the container is *alive or dead*    | Failure → Kubelet restarts the container    |
| **Readiness Probe** | Checks if the app is *ready to serve traffic* | Failure → Pod removed from Service endpoint |
| **Startup Probe**   | Checks if the app *has started* properly      | Failure → Kubelet restarts container        |

---

### 🧪 **Probe Mechanisms**

Each probe supports **three** main methods:

#### 1. **httpGet**

* Makes HTTP request to container.
* Good for web servers or APIs.

```yaml
livenessProbe:
  httpGet:
    path: /healthz
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 10
```

#### 2. **tcpSocket**

* Opens a TCP connection on a port.
* Good for applications like databases, chat services.

```yaml
livenessProbe:
  tcpSocket:
    port: 3306
  initialDelaySeconds: 10
  periodSeconds: 15
```

#### 3. **exec (command)**

* Runs a command inside container and checks exit code (0 = success).
* Good for file checks, internal state validation.

```yaml
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/ready
  initialDelaySeconds: 5
  periodSeconds: 10
```

---

### 🧾 **Full Example: Probes in a Pod**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-health
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 10
    readinessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
    startupProbe:
      httpGet:
        path: /
        port: 80
      failureThreshold: 10
      periodSeconds: 3
```

---

### ⚙️ **Key Configuration Parameters**

| Field                 | Description                                        |
| --------------------- | -------------------------------------------------- |
| `initialDelaySeconds` | Time before first check                            |
| `periodSeconds`       | Time between checks                                |
| `failureThreshold`    | Fail count before action (restart or mark unready) |
| `successThreshold`    | Pass count to mark as healthy                      |
| `timeoutSeconds`      | Max time to wait for probe response                |

---

### ✅ **When to Use What?**

| Scenario                     | Use               |
| ---------------------------- | ----------------- |
| App takes long to boot       | `startupProbe`    |
| App can crash randomly       | `livenessProbe`   |
| App temporarily overloaded   | `readinessProbe`  |
| TCP-based app                | `tcpSocket` probe |
| Health check file or command | `exec` probe      |

---

### 🧠 **Extra Concepts Related to Health Probes**

#### ✅ 1. **Difference between Liveness and Readiness**

* **Liveness** = Pod’s life → Restart on failure.
* **Readiness** = Traffic routing → Only route to ready pods.
* A pod can be **alive** but not **ready** (e.g., overloaded or initializing DB connection).

#### ✅ 2. **Combined Probes for Robust Health Management**

* Use **Startup** → then **Liveness** and **Readiness**
* Helps prevent **false restarts** during startup.

#### ✅ 3. **Probes with Deployments**

* You can define probes in Deployment templates too:

```yaml
spec:
  containers:
  - name: web
    image: myapp
    readinessProbe:
      httpGet:
        path: /status
        port: 8080
```

#### ✅ 4. **Probes and Service Endpoints**

* Only Pods passing the **Readiness Probe** are listed as endpoints in a Service.

```bash
kubectl get endpoints <service-name>
```

---

### 🛠️ **Useful Commands**

```bash
kubectl describe pod <pod-name>     # View probe status and events
kubectl get endpoints               # View which pods are ready for service
kubectl logs <pod-name>             # Check logs if probes are failing
```

---

### 🧩 **Troubleshooting Probes**

* Set lower `failureThreshold` to detect failure quickly.
* Set proper `initialDelaySeconds` for long startups.
* Use logs (`kubectl logs`) to check why probes fail.

---

### 📌 Summary

| Probe     | Checks What        | Action on Failure     |
| --------- | ------------------ | --------------------- |
| Liveness  | Pod is alive?      | Restart pod           |
| Readiness | Ready for traffic? | Remove from Service   |
| Startup   | App started?       | Retry/startup timeout |

---
