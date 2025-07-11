
---

## **🧠Docker – Container Naming, Stopping, Deleting & Inspecting**

---

### 🔹 **Naming a Container**

By default, Docker assigns random names to containers. To name a container manually:

```bash
docker run -d -p 80:80 --name mywebsite nginx
```

**Explanation:**

* `-d`: Detached mode (runs in background)
* `-p 80:80`: Maps host port 80 to container port 80
* `--name mywebsite`: Assigns a custom name "mywebsite" to the container
* `nginx`: Uses the nginx image

Now, instead of using the container ID, you can refer to it by `mywebsite`.

---

### 🔹 **List All Containers (Running & Exited)**

```bash
docker container ls -a
```

or

```bash
docker ps -a
```

**Shows:**

* Running and stopped containers
* Their names, IDs, status, ports, and images

---

### 🔹 **Stopping Docker Containers**

#### Stop a specific container:

```bash
docker stop <container_id or container_name>
```

#### Alternate syntax:

```bash
docker container stop <container_id>
```

#### Stop all running containers:

```bash
docker stop $(docker ps -q)
```

* `docker ps -q` gives only the container IDs of running containers
* `$(...)` is command substitution (shell executes this first)

---

### 🔹 **Remove All Containers**

```bash
docker rm $(docker ps -aq)
```

* `docker ps -aq`: Returns all container IDs (running or exited)
* This command removes **all** containers (must stop them first)

---

### 🔹 **How `docker run` Works Internally**

The `docker run` command performs **three steps behind the scenes**:

1. **`docker pull`** – Pulls the image from Docker Hub (if not present locally)
2. **`docker create`** – Creates a container from the image
3. **`docker start`** – Starts the container

So `docker run nginx` = `docker pull nginx` → `docker create nginx` → `docker start`

---

### 🔹 **Inspecting a Container**

```bash
docker inspect <container_id or container_name>
```

**What it shows:**

* Container configuration
* Mounted volumes
* IP address and ports
* Environment variables
* Network settings

🔍 Great for debugging or auditing the container setup.

---

### 🔹 **Removing Docker Images**

```bash
docker rmi <image_id or image_name>
```

⚠️ You **can’t remove** an image if it’s currently used by any container. You must first remove the container(s) using it.

---

### 🔹 **Clean Up Unused Docker Images**

```bash
docker image prune
```

**Purpose:**

* Deletes all **dangling images** (i.e., images not referenced by any container)
* Helps free up disk space

🧼 To clean unused containers, networks, volumes, and images in one go:

```bash
docker system prune
```

---

## ✅ Summary Table

| Command                                         | Description                               |
| ----------------------------------------------- | ----------------------------------------- |
| `docker run -d -p 80:80 --name mywebsite nginx` | Runs nginx in background with custom name |
| `docker ps -a` or `docker container ls -a`      | Lists all containers                      |
| `docker stop <id>`                              | Stops a running container                 |
| `docker stop $(docker ps -q)`                   | Stops all running containers              |
| `docker rm $(docker ps -aq)`                    | Deletes all containers                    |
| `docker inspect <id>`                           | Shows all container metadata              |
| `docker rmi <image_id>`                         | Removes an image                          |
| `docker image prune`                            | Deletes dangling images                   |

---
