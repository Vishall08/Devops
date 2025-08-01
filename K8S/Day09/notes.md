
---

# **ConfigMap & Secret in Kubernetes**

Kubernetes uses **ConfigMaps** and **Secrets** to **decouple configuration from applications**, making pods **more portable and secure**.

---

## 🔹 **Key Difference Between Volumes and ConfigMap/Secrets**

* **emptyDir, hostPath, PV** → Data is stored on **worker nodes** (where pods run).
* **ConfigMap & Secret** → Data is stored in **etcd** (on **Master Node**), and mounted/used by Pods as needed.

---

## 1️⃣ **ConfigMap**

A **ConfigMap** stores **non-sensitive** configuration in **key-value pairs**.

* Example: environment variables, config files.
* Can be consumed as:

  1. **Environment variables**
  2. **Configuration files (via volume mount)**
  3. **Command-line arguments**

---

### 🧪 **Method 1: ConfigMap as Environment Variable**

**Create ConfigMap:**

```bash
kubectl create configmap mysql-config --from-literal=mysql-password=Pass@123
kubectl get configmap
kubectl describe configmap mysql-config
```

**Pod YAML Example (`mysqlwala.yml`):**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysqlwala
spec:
  containers:
    - name: mysqlwalacontainer
      image: mysql:8.0
      env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            configMapKeyRef:
              name: mysql-config
              key: mysql-password
```

---

### 🧪 **Method 2: ConfigMap as File in Volume**

1️⃣ **Create a config file** (for example, `default.conf`):

```nginx
server {
    listen 80;
    location / {
        root /usr/share/nginx/html;
        index index.html;
    }
}
```

2️⃣ **Create ConfigMap from File**:

```bash
kubectl create configmap nginx-conf --from-file=default.conf
kubectl get configmap nginx-conf -o yaml
```

3️⃣ **Pod YAML using ConfigMap Volume (`nginxwala.yml`):**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginxwala
spec:
  containers:
    - name: nginxcontainer
      image: nginx
      volumeMounts:
        - name: nginxconfigvolume
          mountPath: /etc/nginx/conf.d/default.conf
          subPath: default.conf
  volumes:
    - name: nginxconfigvolume
      configMap:
        name: nginx-conf
```

4️⃣ **Verify in Pod:**

```bash
kubectl exec -it nginxwala -- /bin/bash
cat /etc/nginx/conf.d/default.conf
```

---

## 2️⃣ **Secrets**

Secrets store **sensitive data** like passwords, tokens, certificates.

**Types of Secrets:**

1. **Generic** – Key-value pairs (passwords, API keys)
2. **docker-registry** – For private image pulling
3. **TLS** – For SSL certificates

---

### 🧪 **Generic Secret Example**

**Create Secret (literal value):**

```bash
kubectl create secret generic mysql-secret --from-literal=mysql-password=Pass@123
kubectl get secret
kubectl describe secret mysql-secret
kubectl get secret mysql-secret -o yaml
```

> Data is base64 encoded in YAML.

---

**Use Secret as Environment Variable (`secretwala.yml`):**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secretwala
spec:
  containers:
    - name: mysqlwalacontainer
      image: mysql:8.0
      env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysql-secret
              key: mysql-password
```

---

**Use Secret as File (Volume Mount):**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secretfilewala
spec:
  containers:
    - name: nginxcontainer
      image: nginx
      volumeMounts:
        - name: secretvolume
          mountPath: "/etc/secretdata"
          readOnly: true
  volumes:
    - name: secretvolume
      secret:
        secretName: mysql-secret
```

> Files are created inside `/etc/secretdata` for each key.

---

### 🧪 **Docker Registry Secret**

For pulling private images:

```bash
kubectl create secret docker-registry mydockersecret \
--docker-username=devops \
--docker-password=MyPassword123 \
--docker-server=https://index.docker.io/v1/ \
--docker-email=myemail@example.com
```

Verify:

```bash
kubectl describe secret mydockersecret
kubectl get secret mydockersecret -o yaml
```

Use in Pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: privateimagepod
spec:
  imagePullSecrets:
    - name: mydockersecret
  containers:
    - name: myapp
      image: devops/myprivateimage:latest
```

---

### 🧪 **TLS Secret (SSL Certificates)**

```bash
kubectl create secret tls my-tls-secret \
--cert=path/to/tls.crt \
--key=path/to/tls.key
```

Use in Ingress for HTTPS.

---

## 🔹 **Additional Concepts to Remember**

1. **ConfigMap vs Secret**

   | Feature        | ConfigMap     | Secret         |
   | -------------- | ------------- | -------------- |
   | Data type      | Plain text    | Base64 encoded |
   | Sensitive info | ❌ No          | ✅ Yes          |
   | Storage        | etcd (master) | etcd (master)  |

2. **Updating ConfigMap/Secret**

   ```bash
   kubectl edit configmap my-config
   kubectl rollout restart deployment my-deploy
   ```

3. **Immutable ConfigMap/Secret**

   ```bash
   kubectl create configmap myconfig --from-literal=key=value --immutable
   ```

4. **List all in namespace**

   ```bash
   kubectl get cm,secrets
   ```

---

### ✅ **Practice Folder Structure**

```
configwala/
├── mysqlwala.yml
├── nginxwala.yml
├── secretwala.yml
├── default.conf
```

---

This **Day09** guide is now fully corrected, detailed, and includes:

* Environment variable & volume usage
* ConfigMap & Secret creation
* Docker registry & TLS secrets
* Best practices for real-world Kubernetes apps

---

