
Below is a **comprehensive list of Docker interview questions**, broken into two parts:

---

---

## ✅ **Docker Basics – Detailed Interview Questions & Answers**

---

### **1. What is Docker and how is it different from virtual machines?**

**Answer:**
Docker is a **containerization platform** that allows developers to package applications with all their dependencies into **containers**. These containers are lightweight, portable, and run consistently across different environments.

**Differences from Virtual Machines (VMs):**

| Feature            | Docker (Containers)      | Virtual Machines (VMs)  |
| ------------------ | ------------------------ | ----------------------- |
| **Startup Time**   | Seconds                  | Minutes                 |
| **Size**           | MBs                      | GBs                     |
| **OS Dependency**  | Shares host OS kernel    | Requires full OS per VM |
| **Resource Usage** | Efficient (low overhead) | Heavy                   |

Docker uses **OS-level virtualization**, while VMs use **hardware-level virtualization**.

---

### **2. Explain the Docker architecture (Docker Engine, Daemon, CLI, Images, Containers).**

**Answer:**
Docker architecture consists of the following main components:

* **Docker Engine**: The core component installed on the host machine that runs containers.

  * **Docker Daemon (`dockerd`)**: Background service that manages Docker objects (containers, images, etc.).
  * **Docker CLI**: Command Line Interface used to communicate with the daemon via REST API.
  * **Docker Images**: Read-only templates used to create containers.
  * **Docker Containers**: Running instances of Docker images.
  * **Docker Registry**: Stores Docker images (like Docker Hub or private registries).

**Flow:**

```bash
Docker CLI → Docker Daemon → Image → Container
```

---

### **3. What are the benefits of using Docker?**

**Answer:**

* 🧳 **Portability**: Run the same container anywhere (dev, test, prod).
* 🧹 **Clean environments**: No "it works on my machine" issues.
* ⚡ **Lightweight**: Faster than VMs; uses fewer resources.
* 🔁 **Consistency**: Ensures consistent environment across deployments.
* 🔧 **Isolation**: Each container runs in its own environment.
* 🚀 **Faster CI/CD**: Streamlines DevOps pipelines.

---

### **4. How do you install Docker on Linux?**

**Answer (Ubuntu Example):**

```bash
sudo apt update
sudo apt install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl enable docker
sudo systemctl start docker
```

Check version:

```bash
docker --version
```

---

### **5. What is the role of the Docker daemon?**

**Answer:**
The **Docker Daemon (`dockerd`)** runs as a background process. It:

* Builds, runs, and manages containers.
* Listens for Docker API requests from the Docker CLI or other tools.
* Manages images, containers, volumes, and networks.

You can communicate with it via:

```bash
docker <command>
```

---

### **6. What is the difference between `docker run` and `docker start`?**

| Command        | Purpose                                              |
| -------------- | ---------------------------------------------------- |
| `docker run`   | Creates and **starts a new container** from an image |
| `docker start` | Starts an **already created (stopped)** container    |

```bash
docker run -it ubuntu     # New container
docker start <container_id>  # Restart stopped container
```

---

### **7. What does the `--rm` flag do?**

**Answer:**
The `--rm` flag **automatically removes the container** once it exits.

```bash
docker run --rm ubuntu echo "Temporary container"
```

* Useful for short-lived containers
* Helps keep the system clean
* Does not persist container after execution

---



---

## 🛠️ **Docker Images & Containers – Interview Questions & Answers**

---

### **8. How do you create your own Docker image?**

**Answer:**
You can create a Docker image using a `Dockerfile`. A `Dockerfile` is a script containing a list of instructions that define how the image should be built.

**Steps:**

1. **Write a Dockerfile**

```Dockerfile
# Example for Node.js app
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN npm install
CMD ["node", "app.js"]
```

2. **Build the image**

```bash
docker build -t my-node-app .
```

3. **Run the container**

```bash
docker run -p 3000:3000 my-node-app
```

---

### **9. What’s the difference between a container and an image?**

| Docker Image                | Docker Container                   |
| --------------------------- | ---------------------------------- |
| Blueprint for a container   | Running instance of an image       |
| Static and read-only        | Live and executable                |
| Created with `docker build` | Created with `docker run`          |
| Stored in Docker registry   | Lives in the Docker host (runtime) |

**Example:**

```bash
docker build -t myimage .     # Creates image
docker run myimage            # Runs container from image
```

---

### **10. What is the purpose of the Dockerfile? Explain each instruction (`FROM`, `RUN`, `CMD`, `ENTRYPOINT`, etc.).**

**Answer:**
A `Dockerfile` is a text document with step-by-step instructions to create a Docker image.

| Instruction  | Description                                                                     |
| ------------ | ------------------------------------------------------------------------------- |
| `FROM`       | Sets the base image (e.g., `FROM ubuntu`, `FROM node:18`)                       |
| `WORKDIR`    | Sets the working directory inside the image                                     |
| `COPY`       | Copies files from host to image (e.g., `COPY . /app`)                           |
| `RUN`        | Executes commands during image build (e.g., `RUN apt-get update`)               |
| `CMD`        | Default command when the container starts (e.g., `CMD ["npm", "start"]`)        |
| `ENTRYPOINT` | Specifies the main command and allows arguments (e.g., `ENTRYPOINT ["python"]`) |
| `EXPOSE`     | Documents which port the container listens on                                   |
| `ENV`        | Sets environment variables (e.g., `ENV NODE_ENV=production`)                    |

**Example:**

```Dockerfile
FROM python:3.9
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

---

### **11. What is a base image vs parent image?**

**Answer:**

* **Base Image**: The starting point of your image. It has **no parent**.

  * Example: `FROM scratch` or `FROM ubuntu`

* **Parent Image**: Any image you use as a base in your Dockerfile. It builds **on top of another image**.

  * Example: `FROM node:18-alpine` → `node:18-alpine` is the parent image.

**In short:**
Every parent image is a base for your app, but the base image (like `scratch`) starts from zero.

---

### **12. How do `CMD` and `ENTRYPOINT` differ?**

| CMD                      | ENTRYPOINT                  |
| ------------------------ | --------------------------- |
| Provides default command | Always executed             |
| Can be overridden        | Cannot be easily overridden |
| Used for app commands    | Used for container behavior |

**Example:**

```Dockerfile
FROM ubuntu
ENTRYPOINT ["echo"]
CMD ["Hello"]
```

```bash
docker run myimage World
# Output: echo World
```

* If using only `CMD`, it can be replaced easily at runtime.
* `ENTRYPOINT` is better for fixed commands (e.g., run Python, Nginx).

---

### **13. What is the purpose of `.dockerignore`?**

**Answer:**
`.dockerignore` file excludes files/folders from being copied into the Docker image during build.

Similar to `.gitignore`.

**Benefits:**

* Reduces image size
* Prevents sensitive or unnecessary files from being added
* Speeds up build time

**Example `.dockerignore`:**

```
node_modules
.env
.git
*.log
Dockerfile
```

Used in the same directory where the Dockerfile resides.

---

### ✅ Summary of Key Commands:

| Action        | Command                        |
| ------------- | ------------------------------ |
| Build image   | `docker build -t myimage .`    |
| Run container | `docker run myimage`           |
| View images   | `docker images`                |
| Remove image  | `docker rmi <image_id>`        |
| Tag image     | `docker tag myimage user/repo` |

---




---

## 📦 **Docker Volume & Persistence – Detailed Q\&A**

---

### **14. How does Docker manage data persistence?**

**Answer:**
By default, data written inside a Docker container is **ephemeral**—it’s lost when the container is stopped or removed.

To make data **persistent**, Docker provides three main methods:

1. **Volumes** (Preferred method)

   * Managed by Docker (`docker volume`)
   * Stored in a separate location on the host filesystem
   * Portable and sharable across containers

2. **Bind mounts**

   * Maps a file or directory from the host to the container
   * Used for live code editing or accessing host files

3. **tmpfs mounts**

   * Stored in RAM only, nothing written to disk
   * Used for sensitive or fast-access data (temporary data)

**Example using volume:**

```bash
docker run -v myvolume:/app/data myimage
```

The data inside `/app/data` will persist even if the container is deleted.

---

### **15. What is the difference between `bind mounts`, `volumes`, and `tmpfs`?**

| Feature          | Volumes                         | Bind Mounts                 | tmpfs                     |
| ---------------- | ------------------------------- | --------------------------- | ------------------------- |
| **Managed By**   | Docker Engine                   | OS/Host                     | In-memory (RAM)           |
| **Stored Where** | `/var/lib/docker/volumes/`      | Anywhere on host filesystem | RAM only                  |
| **Use Case**     | Persist data between runs       | Share config/code           | Temporary, sensitive data |
| **Backups**      | Easy with `docker volume` tools | Manual                      | Not persistent            |
| **Performance**  | Better for Docker               | Depends on host FS          | Fastest                   |

**Examples:**

* **Volume:**

  ```bash
  docker run -v myvol:/app/data myimage
  ```

* **Bind Mount:**

  ```bash
  docker run -v /home/user/data:/app/data myimage
  ```

* **tmpfs:**

  ```bash
  docker run --tmpfs /app/temp myimage
  ```

---

### **16. How do you create and attach a volume to a container?**

**Answer:**

👉 **Step 1: Create the volume**

```bash
docker volume create mydata
```

👉 **Step 2: Run container with the volume attached**

```bash
docker run -d -v mydata:/app/data myimage
```

This mounts the `mydata` volume into the container at `/app/data`.

👉 **Step 3: Check volume**

```bash
docker volume ls
docker volume inspect mydata
```

👉 **Optional: Remove the volume**

```bash
docker volume rm mydata
```

🔸 Volumes can be reused across containers, making them perfect for databases and shared configs.

---

### **17. What is the default location of Docker volumes on the host?**

**Answer:**
Docker stores volumes by default in:

```
/var/lib/docker/volumes/
```

Each volume is stored in its own subdirectory:

```
/var/lib/docker/volumes/<volume_name>/_data
```

You can access the volume data directly, but it's **not recommended** to manually modify it. Docker manages this directory.

**To check it:**

```bash
sudo ls /var/lib/docker/volumes/myvolume/_data
```

🔸 You should use `docker cp`, `docker exec`, or shared volumes to work with data safely.

---

## ✅ Recap of Key Commands:

| Task                    | Command                           |
| ----------------------- | --------------------------------- |
| Create volume           | `docker volume create myvol`      |
| List volumes            | `docker volume ls`                |
| Inspect volume          | `docker volume inspect myvol`     |
| Use volume in container | `docker run -v myvol:/path image` |
| Remove volume           | `docker volume rm myvol`          |

---





---

## 🌐 **Docker Networking – Interview Questions & Answers**

---

### **18. What types of Docker networks are available?**

**Answer:**
Docker provides several **built-in network drivers**, each suited to different use cases:

| Network Type | Description                                                                               |
| ------------ | ----------------------------------------------------------------------------------------- |
| **bridge**   | Default for standalone containers on the same host. Good for basic use.                   |
| **host**     | Container shares the host's network stack. No isolation.                                  |
| **none**     | Container has no network. Useful for strict isolation.                                    |
| **overlay**  | Enables communication across multiple Docker hosts (Swarm mode).                          |
| **macvlan**  | Assigns a MAC address to container. Used when container must look like a physical device. |

You can also **create custom bridge networks**, which allow containers to discover each other by name (DNS-based).

---

### **19. How do you connect multiple containers using a bridge network?**

**Answer:**
You can use a **user-defined bridge network** to connect containers so they can communicate via container names.

#### 📌 Steps:

1. **Create a custom bridge network:**

```bash
docker network create mynet
```

2. **Run containers on the same network:**

```bash
docker run -dit --name backend --network mynet busybox
docker run -dit --name frontend --network mynet busybox
```

3. **Test connectivity:**

```bash
docker exec -it frontend ping backend
```

🧠 In a **default bridge** network, you have to use IP addresses.
But in **custom bridge** networks, Docker provides **automatic DNS resolution** by container name.

---

### **20. How do you inspect the network configuration of a container?**

**Answer:**
To check which network(s) a container is connected to and its IP, use:

```bash
docker inspect <container_id or name>
```

Or filter with `jq` or `grep`:

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name
```

You can also inspect a specific network:

```bash
docker network inspect mynet
```

This shows:

* Connected containers
* IP addresses
* Subnets
* Gateway info

---

### **21. What happens if two containers expose the same port?**

**Answer:**
This depends on whether containers are running on the **same network interface and host port**.

* If both containers **map the same container port** but to **different host ports**, it works fine:

```bash
docker run -p 8080:80 nginx
docker run -p 8081:80 nginx
```

* But if you try to map both to the **same host port**, Docker will throw an error:

```bash
docker run -p 8080:80 nginx
docker run -p 8080:80 nginx   # ❌ will fail — port already in use
```

Inside a Docker network (like a custom bridge), containers **can expose the same internal ports** because each has its own IP.

---

## 🧠 Real-world Example: Docker Compose Networking

In a `docker-compose.yml`, all services are automatically on the same custom bridge network, so they can talk to each other by service name:

```yaml
services:
  db:
    image: mysql
  app:
    image: myapp
    depends_on:
      - db
```

The `app` container can reach `db` by just using:

```bash
mysql -h db -u root -p
```

---

## ✅ Recap of Key Commands:

| Task                         | Command                             |
| ---------------------------- | ----------------------------------- |
| List networks                | `docker network ls`                 |
| Create network               | `docker network create mynet`       |
| Run container on network     | `docker run --network mynet ...`    |
| Inspect a network            | `docker network inspect mynet`      |
| Connect/disconnect a network | `docker network connect/disconnect` |

---




---

## 🧱 **Docker Compose – Q\&A with Examples**

---

### **22. What is Docker Compose?**

**Answer:**
**Docker Compose** is a tool used to **define and manage multi-container Docker applications** using a single `docker-compose.yml` file.

🔹 It allows you to:

* Define services (e.g., web app, database, backend) in one file
* Start all services with one command: `docker-compose up`
* Manage dependencies and networking automatically

✅ Benefits:

* Simplifies multi-container apps
* Automates builds and service startup
* Provides shared networking and volumes

---

### **23. How does `docker-compose.yml` work?**

**Answer:**
The `docker-compose.yml` file is a YAML configuration that defines:

* Each container as a **service**
* How the service is **built or pulled**
* Port mappings, environment variables, volumes, networks, etc.

**How it works:**

1. Compose reads the `docker-compose.yml` file
2. Builds images (if needed)
3. Creates a default **bridge network**
4. Starts all containers and attaches them to that network

**Basic Example:**

```yaml
version: '3.9'
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: redis:alpine
```

Then run:

```bash
docker-compose up
```

---

### **24. How do you define multiple services in a Docker Compose file?**

**Answer:**
You define each service under the `services:` section with unique names.

**Example with 3 services:**

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: example
  redis:
    image: redis:latest
```

Here, `app`, `db`, and `redis` are 3 separate services in one Compose file.

---

### **25. What is the difference between `build` and `image` in Docker Compose?**

| Property | `build`                             | `image`                                |
| -------- | ----------------------------------- | -------------------------------------- |
| Purpose  | Build image from local `Dockerfile` | Use pre-built image from Docker Hub    |
| Use Case | Custom app development              | Standard services (MySQL, Nginx, etc.) |
| Example  | `build: .`                          | `image: nginx:latest`                  |

✅ You can also use both together:

```yaml
services:
  app:
    build: .
    image: my-custom-app
```

---

### **26. How do you use environment variables in Docker Compose?**

**Answer:**
You can use environment variables in Compose in multiple ways:

### ✅ Method 1: Inline in `docker-compose.yml`

```yaml
services:
  web:
    image: nginx
    environment:
      - ENV=production
      - PORT=80
```

### ✅ Method 2: Using `.env` file

* Create a file named `.env` in the same directory:

```
ENV=production
PORT=80
```

Then use them in `docker-compose.yml`:

```yaml
services:
  web:
    image: nginx
    environment:
      - ENV=${ENV}
      - PORT=${PORT}
```

### ✅ Method 3: `env_file` directive

```yaml
services:
  app:
    env_file:
      - ./app.env
```

---

## 🌐 **Reverse Proxy with Nginx – Q\&A**

---

### **27. What is a reverse proxy? How did you configure Nginx as one?**

**Answer:**
A **reverse proxy** is a server that sits between clients and backend servers, forwarding client requests to the appropriate service.

🔹 **Why use Nginx as reverse proxy:**

* Load balancing
* SSL termination
* Simplifies access to multiple backend services

### ✅ Example Nginx Configuration:

In `default.conf`:

```nginx
server {
  listen 80;
  location / {
    proxy_pass http://app:3000;
  }
}
```

Here:

* Nginx listens on port 80
* Forwards requests to `app` service on port 3000

### ✅ Use in Dockerfile:

```Dockerfile
FROM nginx:alpine
COPY default.conf /etc/nginx/conf.d/
```

---

### **28. How do you link Nginx to other containers using Docker Compose?**

**Answer:**
All services in a Docker Compose file are connected via a **common network**, so you can **refer to services by name** in the Nginx config.

### ✅ docker-compose.yml:

```yaml
version: '3.9'
services:
  app:
    build: ./app
    expose:
      - "3000"

  nginx:
    build: ./nginx
    ports:
      - "80:80"
    depends_on:
      - app
```

In your Nginx `default.conf`:

```nginx
proxy_pass http://app:3000;
```

Docker Compose creates an internal DNS, so `app` will resolve to the container's IP automatically.

---

## ✅ Summary:

| Task                      | Example                       |
| ------------------------- | ----------------------------- |
| Start app with Compose    | `docker-compose up -d`        |
| Stop services             | `docker-compose down`         |
| Build all services        | `docker-compose build`        |
| View logs                 | `docker-compose logs`         |
| Use service name in Nginx | `proxy_pass http://app:port;` |

---



---

---

## 🔐 **Docker Security – Questions & Detailed Answers**

---

### **29. How can you secure Docker containers?**

**Answer:**
Securing Docker containers involves multiple layers of best practices:

#### ✅ 1. **Use Official or Trusted Images**

* Always pull images from **Docker Hub official repos** or signed internal registries.
* Avoid using `latest` tag; pin versions.

#### ✅ 2. **Least Privilege Principle**

* Avoid running processes inside containers as `root`:

```Dockerfile
USER appuser
```

#### ✅ 3. **Use Read-only Filesystems**

* Run containers with read-only root filesystems:

```bash
docker run --read-only myapp
```

#### ✅ 4. **Use Docker Seccomp, AppArmor, SELinux**

* Limit syscalls using **Seccomp profiles**.
* Restrict access using **AppArmor/SELinux** policies.

#### ✅ 5. **Network Isolation**

* Use custom networks to isolate sensitive services (e.g., internal DBs not on public network).

#### ✅ 6. **Scan Images for Vulnerabilities**

* Use tools like:

  * `docker scan`
  * **Trivy**
  * **Anchore**, **Clair**

---

### **30. What is Docker Content Trust (DCT)?**

**Answer:**
**Docker Content Trust (DCT)** ensures **image integrity and authenticity** using digital signatures.

🔐 When DCT is enabled:

* Docker only allows **signed images** to be pulled or run.
* Prevents tampering from unauthorized sources.

### ✅ Enable DCT:

```bash
export DOCKER_CONTENT_TRUST=1
```

### 📌 Example:

If you try to pull an unsigned image:

```bash
docker pull unsigned-image
# Error: DCT enabled, but no trust data available
```

---

### **31. How can you prevent privilege escalation in Docker containers?**

**Answer:**
Privilege escalation means a user or process inside a container gains access to resources beyond what’s intended.

### ✅ Prevention Methods:

| Method                                  | Description                                          |
| --------------------------------------- | ---------------------------------------------------- |
| `--user` flag / `USER` in Dockerfile    | Avoid root execution inside containers               |
| `--cap-drop ALL`                        | Drop all Linux capabilities and add only needed ones |
| `--read-only`                           | Prevent filesystem tampering                         |
| `--security-opt no-new-privileges:true` | Blocks any attempt to gain extra privileges          |
| Avoid `--privileged` flag               | Only use it for debugging — **very insecure**        |

Example:

```bash
docker run --user 1000:1000 --cap-drop ALL myimage
```

---

## 🎯 **Multi-Stage Docker Builds – Questions & Answers**

---

### **32. What is a multi-stage Docker build? Why is it useful?**

**Answer:**
**Multi-stage builds** allow you to use **multiple `FROM` statements** in a single Dockerfile to:

* Build in one stage
* Copy the result to a lightweight final image

✅ It helps:

* **Reduce image size**
* **Keep only the production code**
* **Avoid shipping build tools like compilers**

---

### **33. How do multi-stage builds help reduce image size?**

**Answer:**
They remove unnecessary tools and dependencies after the build.

### 🛠️ Example:

```Dockerfile
# Stage 1: Build
FROM golang:1.21 AS builder
WORKDIR /app
COPY . .
RUN go build -o myapp

# Stage 2: Run
FROM alpine
COPY --from=builder /app/myapp /myapp
ENTRYPOINT ["/myapp"]
```

✅ Final image:

* Only has the binary `myapp`
* No Go compiler, source code, or temporary files
* Smaller, faster, more secure

---

## 🧪 **Testing & Debugging Containers – Q\&A**

---

### **34. How do you troubleshoot a Docker container that won’t start?**

**Answer:**

1. **Check container logs:**

```bash
docker logs <container_id>
```

2. **Use `docker inspect`** for config and errors:

```bash
docker inspect <container_id>
```

3. **Start a shell inside the container (if running):**

```bash
docker exec -it <container_id> /bin/sh
```

4. **Test image manually:**

```bash
docker run -it myimage /bin/sh
```

5. **Check for port or volume conflicts**

---

### **35. How do you view logs of a container?**

**Answer:**
Use:

```bash
docker logs <container_name or ID>
```

### Common flags:

* `-f`: follow logs (like `tail -f`)
* `--since` and `--until`: filter by time

```bash
docker logs -f --since="10m" myapp
```

> For multi-service apps: `docker-compose logs`

---

### **36. What does the `docker inspect` command do?**

**Answer:**
`docker inspect` returns detailed **low-level JSON metadata** about Docker objects:

* Containers
* Images
* Volumes
* Networks

### Example:

```bash
docker inspect mycontainer
```

### Info you get:

* IP address and hostname
* Mounts
* Ports
* Environment variables
* Exit code
* Health status

### Filter specific data:

```bash
docker inspect -f '{{.State.Status}}' mycontainer
```

---





---

### 🛑 **Container Lifecycle & Management**

**37. What are the different container states?**
Docker containers go through several states:

* **Created** – Container has been created but not started.
* **Running** – The container is actively executing.
* **Paused** – Processes in the container are temporarily frozen (`docker pause`).
* **Exited** – The container has stopped running but is still available.
* **Dead** – Rare state where the container is unusable due to internal issues.

**38. How can you remove all stopped containers?**

```bash
docker container prune
# OR
docker rm $(docker ps -aq -f status=exited)
```

**39. What’s the difference between `docker pause` and `docker stop`?**

* `docker pause` suspends all processes inside the container (like `SIGSTOP`).
* `docker stop` gracefully stops the container by sending `SIGTERM`, then `SIGKILL`.

---

### 📦 **Docker Registry & Hub**

**40. What is Docker Hub?**
Docker Hub is a cloud-based repository where Docker users and partners create, test, store, and distribute container images.

**41. How do you push and pull Docker images?**

```bash
# Pulling from Docker Hub
docker pull nginx:latest

# Pushing to Docker Hub (after login)
docker tag myimage your_dockerhub_username/myimage
docker push your_dockerhub_username/myimage
```

**42. What is the difference between a private and public registry?**

* **Public**: Accessible by anyone (e.g., Docker Hub).
* **Private**: Requires authentication and access control, useful for internal applications (e.g., AWS ECR, GitHub Container Registry).

---

### ⏱️ **Performance & Optimization**

**43. How can you reduce the size of a Docker image?**

* Use **multi-stage builds**.
* Start from smaller base images (e.g., `alpine`).
* Avoid unnecessary files (e.g., `.dockerignore`).
* Combine commands (`RUN apt update && apt install -y ...`).
* Clean up after installs (e.g., `rm -rf /var/lib/apt/lists/*`).

**44. What are some best practices for writing a Dockerfile?**

* Use `.dockerignore` to skip unnecessary files.
* Order `RUN`, `COPY`, and `ADD` commands to benefit from caching.
* Use `CMD` or `ENTRYPOINT` correctly.
* Avoid using root where possible (`USER` directive).
* Use fixed image versions instead of `latest`.

**45. What is layer caching and how does it affect build speed?**
Each Dockerfile instruction creates a layer. Docker caches these layers:

* If a layer hasn't changed, Docker reuses it—**greatly improving build speed**.
* Changing a layer invalidates all layers after it.

---

### 📊 **Docker Stats & Monitoring**

**46. How do you monitor resource usage of containers?**

```bash
docker stats
```

This command shows real-time CPU, memory, and network usage of running containers.

**47. How can you limit CPU and memory usage for a container?**

```bash
docker run --memory="256m" --cpus="1.5" nginx
```

* `--memory`: max memory.
* `--cpus`: limit CPU usage.

---

### 🔁 **Restart Policies**

**48. What are Docker restart policies?**
They define what Docker should do when a container exits.

**49. What is the difference between `no`, `on-failure`, `always`, and `unless-stopped`?**

* `no`: Default; never restart.
* `on-failure`: Restart if the container exits with non-zero code.
* `always`: Restart regardless of exit status.
* `unless-stopped`: Restart unless you manually stop it.

```bash
docker run --restart=always nginx
```

---

### 🧱 **Docker Swarm (Optional – Orchestration)**

**50. What is Docker Swarm? How is it different from Kubernetes?**

* Docker Swarm is Docker’s native clustering and orchestration tool.
* Kubernetes is a more feature-rich, complex, and widely adopted orchestrator.
  **Key Differences**:
  \| Feature           | Docker Swarm    | Kubernetes       |
  \|------------------|------------------|------------------|
  \| Complexity        | Simple           | Complex          |
  \| Scalability       | Medium           | High             |
  \| Networking        | Built-in overlay | Advanced options |
  \| Ecosystem         | Docker native    | Community-wide   |

**51. How do you create a Docker Swarm cluster?**

```bash
# On manager node
docker swarm init

# On worker node (with token)
docker swarm join --token <worker-token> <manager-ip>:2377
```

**52. What are services and tasks in Docker Swarm?**

* **Service**: Describes how containers should behave (e.g., number of replicas).
* **Task**: A running container created by the Swarm as part of a service.

---



---

## 📋 Bonus: Dockerfile Interview Scenario

You may be asked to analyze or fix a Dockerfile. Prepare to:

* Explain what each line does
* Identify optimization issues (e.g., combining `RUN` commands)
* Add multi-stage build or volume
* Create a Dockerfile for a specific app (Node.js, Python, PHP)

---

## 📘 Preparation Tip:

Prepare short answers with real examples from your GitHub repo. Practice hands-on:

```bash
docker ps
docker images
docker logs <container_id>
docker-compose up -d
docker build -t myimage .
```

---


