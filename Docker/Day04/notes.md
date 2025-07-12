---

## 🧠 **Docker Volumes – Persistent Storage in Containers**

---

### 🔹 Why Use Volumes in Docker?

Docker containers are **ephemeral** by default — meaning all data inside a container is **lost** when the container is stopped or deleted.
**Docker volumes** solve this problem by allowing **persistent data storage** outside the container's filesystem.

---

## 🔸 1. **Host Bind Mounts (Manual Path Mounting)**

### ✅ Example:

```bash
mkdir mybackup
docker run -d --name majhasql -v /home/ec2-user/mybackup:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=Pass@123 mysql
```

**Explanation:**

* `-v /host/path:/container/path`: Mounts the host directory into the container.
* `/home/ec2-user/mybackup`: Directory on host EC2 instance.
* `/var/lib/mysql`: Directory inside MySQL container where data is stored.
* This command creates a MySQL container with data stored on the host (EC2) instance.

### ✅ Steps to Verify:

```bash
docker exec -it majhasql /bin/bash
mysql -u root -p
```

Enter `Pass@123` and run:

```sql
CREATE DATABASE facebook;
USE facebook;
CREATE TABLE posts(id INT, name VARCHAR(100));
INSERT INTO posts VALUES (1, 'ravi');
```

Now, exit the container and check:

```bash
cd /home/ec2-user/mybackup
ls
```

This shows MySQL database files—**proof of successful bind mount**.

---

## 🔄 Reuse the Backup to Start a New Container

### ✅ Steps:

```bash
docker stop majhasql
docker rm majhasql
```

Now reuse the same volume (mybackup) for a new container:

```bash
docker run -d --name tujhasql -v /home/ec2-user/mybackup:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=Pass@123 mysql
docker exec -it tujhasql /bin/bash
mysql -u root -p
```

Run inside MySQL:

```sql
SHOW DATABASES;
USE facebook;
SELECT * FROM posts;
```

✅ The previously created data is **still available**, proving **persistent storage** using host path (bind mount).

---

## 🔸 2. **Docker Named Volumes**

Named volumes are **managed by Docker** (not tied to a specific file path on host). They are better for portability, backups, and volume inspection.

### ✅ Create a named volume:

```bash
docker volume create oursqlvolume
```

Check all volumes:

```bash
docker volume ls
```

Inspect the volume:

```bash
docker volume inspect oursqlvolume
```

It will show the actual path (e.g., `/var/lib/docker/volumes/oursqlvolume/_data`)

---

### ✅ Use named volume with MySQL:

```bash
docker run -d --name sqlwithnamedvolume -v oursqlvolume:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=Pass@123 mysql
```

* Container stores its MySQL data inside Docker-managed volume.
* If the container is deleted, the volume can be reused with another container.

---

## 🔸 3. **Anonymous Volumes**

When you use `-v /container/path` **without** specifying a host path or volume name, Docker creates an **anonymous** volume.

Example:

```bash
docker run -d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=Pass@123 mysql
```

* These volumes are created with random names.
* Harder to track and reuse.
* You can see them via:

```bash
docker volume ls
```

---

## 🔧 **Volume Management Commands**

| Command                        | Description                                |
| ------------------------------ | ------------------------------------------ |
| `docker volume create <name>`  | Create a named volume                      |
| `docker volume ls`             | List all Docker volumes                    |
| `docker volume inspect <name>` | View metadata & mount path                 |
| `docker volume rm <name>`      | Remove a named volume (must not be in use) |
| `docker volume prune`          | Delete **all unused** volumes              |

---

## ✅ Summary Table

| Type of Volume       | Command Example                 | Description                        |
| -------------------- | ------------------------------- | ---------------------------------- |
| **Bind Mount**       | `-v /host/path:/container/path` | Host-managed path (e.g., EC2 path) |
| **Named Volume**     | `-v myvol:/container/path`      | Docker-managed persistent storage  |
| **Anonymous Volume** | `-v /container/path`            | Docker creates unnamed volume      |

---

## ✅ Best Practices

* Use **bind mounts** for local development and backups.
* Use **named volumes** for production-grade containers for easy portability and backup.
* Avoid **anonymous volumes** unless necessary.

---
