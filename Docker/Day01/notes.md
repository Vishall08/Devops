---
## **Docker**

---

### 🔹 **What is Docker?**

Docker is a **platform** for developing, shipping, and running applications inside containers. It simplifies the process of application deployment by isolating the app from its environment.

---

### 🔹 **Docker Components**

* **Image**: A lightweight, standalone, executable package that includes everything needed to run an application (code, libraries, environment variables, etc.)
* **Container**: A runtime instance of a Docker image.
* **Docker Hub**: A cloud-based registry where Docker images are stored and shared.
* **Docker Engine**: The core software that hosts and runs the containers.

---

### 🔹 **Basic Docker Commands**

| Command                                    | Description                                             |
| ------------------------------------------ | ------------------------------------------------------- |
| `sudo yum install docker`                  | Install Docker on CentOS/RHEL                           |
| `sudo service docker start`                | Start the Docker service                                |
| `docker pull nginx`                        | Pull the official nginx image from Docker Hub           |
| `docker images`                            | List all downloaded images                              |
| `docker run -d nginx`                      | Run the nginx container in detached mode                |
| `docker ps`                                | List running containers                                 |
| `docker run -d -it nginx`                  | Run container in detached and interactive terminal mode |
| `docker exec -it <container_id> /bin/bash` | Enter a running container shell                         |

---

### 🔹 **Understanding Nginx Docker Image**

* Lightweight OS inside container (Debian/Alpine Linux)
* Pre-installed nginx server
* Useful for web serving, reverse proxy, and load balancing

---



