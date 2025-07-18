
---

# 📘 **Kubernetes Installation & Cluster Setup**

---

## 🌐 **What Will You Learn Today?**

✅ Kubernetes Cluster Architecture
✅ Minimum System Requirements
✅ Installing Docker, kubeadm, kubelet, and kubectl
✅ Initializing a Kubernetes Cluster
✅ Joining Worker Nodes
✅ Installing Network Plugins (Flannel, Calico)
✅ Verifying Cluster Status
✅ Essential Post-Setup Commands
✅ Bonus: Understanding kubeconfig, taints, and troubleshooting

---

## 🧱 **Cluster Architecture Overview**

A Kubernetes **cluster** consists of:

| Role   | Components                                      |
| ------ | ----------------------------------------------- |
| Master | API Server, etcd, Controller Manager, Scheduler |
| Worker | kubelet, kube-proxy, container runtime          |

**Cluster = 1 Master Node + N Worker Nodes**

---

## 💻 **System Requirements (for Practice using kubeadm)**

| Component | Requirement                                         |
| --------- | --------------------------------------------------- |
| RAM       | **4 GB** or more per machine                        |
| CPU       | **2 CPUs** minimum                                  |
| OS        | Ubuntu 20.04 / CentOS / Debian                      |
| Access    | Root or sudo privileges                             |
| Network   | Internet connection and communication between nodes |
| Swap      | Must be **disabled**                                |

📌 Each node (master + workers) must have:

* Docker or containerd installed
* kubeadm, kubelet, and kubectl installed

---

## 🧰 **Step-by-Step Installation**

### 🔧 **1. Install Docker or Container Runtime on All Nodes**

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
```

---

### 🔧 **2. Install Kubernetes Components on All Nodes**

```bash
sudo apt update && sudo apt install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
```

Add K8s repository:

```bash
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | \
sudo tee /etc/apt/sources.list.d/kubernetes.list
```

Install tools:

```bash
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

---

### 🧹 **3. Disable Swap (Required!)**

```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

---

## 🎯 **Master Node Configuration**

### 🛠️ **1. Initialize the Cluster**

```bash
sudo kubeadm init --pod-network-cidr=192.168.0.0/16
```

🔐 Save the **`kubeadm join`** command output for adding worker nodes.

### 📂 **2. Configure kubectl for your User**

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### ✅ Now verify:

```bash
kubectl get nodes
kubectl get pods -A
```

---

## 🌐 **Installing a Network Plugin (Choose One)**

**Required for pod-to-pod communication.**

### 📦 Option 1: Flannel

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

### 📦 Option 2: Calico

```bash
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

✅ Now, check that the master node becomes `Ready`.

---

## 🤝 **Worker Node Setup**

On each worker node, run the **`kubeadm join`** command copied from the master node:

```bash
sudo kubeadm join <MASTER-IP>:6443 --token <TOKEN> --discovery-token-ca-cert-hash sha256:<HASH>
```

> If expired, regenerate token using:

```bash
kubeadm token create --print-join-command
```

---

## ✅ **Post-Installation: Verify Cluster**

### 📦 View Cluster Nodes

```bash
kubectl get nodes
```

### 📦 View All Pods in All Namespaces

```bash
kubectl get pods -A
```

### 📦 Get Cluster Info

```bash
kubectl cluster-info
```

---

## 🔐 **Important Kubernetes Concepts Introduced**

### 🧩 kubeadm

* Tool to set up and manage K8s clusters easily
* Doesn't install Kubernetes from scratch, but bootstraps it

### 🔐 kubeconfig

* Stores authentication and cluster access settings
* Located at `~/.kube/config`

### 🚫 Taints and Tolerations

* By default, master nodes are **tainted** (no pods scheduled on them)
* You can remove taint to use master as a worker in single-node clusters:

```bash
kubectl taint nodes <node-name> node-role.kubernetes.io/control-plane-
```

---

## 🚑 **Troubleshooting Tips**

| Problem                 | Solution                                                            |
| ----------------------- | ------------------------------------------------------------------- |
| Nodes not `Ready`       | Check network plugin logs                                           |
| kubeadm join failed     | Recreate token using `kubeadm token create --print-join-command`    |
| Pods stuck in `Pending` | Networking not installed or misconfigured                           |
| Swap is enabled         | Disable swap (`swapoff -a`)                                         |
| Wrong docker version    | Stick to supported versions (docker 20.x or containerd recommended) |

---

## 🧪 **Bonus: Try a Test Deployment**

```bash
kubectl create deployment nginx --image=nginx
kubectl expose deployment nginx --port=80 --type=NodePort
kubectl get svc
```

Access your app via:

```
http://<NodeIP>:<NodePort>
```

---

## 📝 **Summary Table**

| Step | Action                                              |
| ---- | --------------------------------------------------- |
| 1    | Install Docker + Kubernetes components on all nodes |
| 2    | Disable swap                                        |
| 3    | `kubeadm init` on master                            |
| 4    | Setup `kubectl`                                     |
| 5    | Install Flannel/Calico                              |
| 6    | Run `kubeadm join` on workers                       |
| 7    | Validate with `kubectl get nodes`                   |

---


