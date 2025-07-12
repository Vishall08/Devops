---

## 🧠 **WordPress in Docker & Dockerfile Image Creation**

---

## 🔹 **Part 1: Running WordPress using Docker**

WordPress needs two main services:

* A **MySQL database** (backend)
* A **WordPress PHP + Apache server** (frontend)

---

### ✅ Step 1: Run MySQL Container for WordPress

```bash
docker run -d \
  -e MYSQL_ROOT_PASSWORD=Pass@123 \
  -e MYSQL_DATABASE=wordpressdb \
  -v /home/ec2-user/wordpressbackup:/var/lib/mysql \
  --name wpsql \
  mysql
```

**Explanation:**

* `-e MYSQL_ROOT_PASSWORD=Pass@123`: Sets the root password
* `-e MYSQL_DATABASE=wordpressdb`: Creates a DB for WordPress
* `-v`: Mounts a host directory for DB backup
* `--name wpsql`: Container name
* `mysql`: Uses official MySQL image

✅ Check it’s running:

```bash
docker ps
```

---

### ✅ Step 2: Run WordPress Container

Before running WordPress, always refer to official environment variables.
🔗 **Docker Hub WordPress Documentation**:
👉 [https://hub.docker.com/\_/wordpress](https://hub.docker.com/_/wordpress)

Now run the WordPress container:

```bash
docker run -d -p 80:80 \
  -e WORDPRESS_DB_HOST=wpsql \
  -e WORDPRESS_DB_USER=root \
  -e WORDPRESS_DB_PASSWORD=Pass@123 \
  -e WORDPRESS_DB_NAME=wordpressdb \
  --link wpsql:mysql \
  --name wordpress \
  wordpress
```

**Explanation:**

* `--link`: Links to MySQL container (`wpsql`)
* `-p 80:80`: Maps container’s port 80 to host port 80
* Uses environment variables expected by WordPress image

✅ Check:

```bash
docker ps
```

---

### ✅ Step 3: Access WordPress in Browser

* Get your **EC2 public IP**
* Open browser: `http://<your-ec2-public-ip>`
* You should see the WordPress setup page.
* Fill in the site title, admin username, and password.
* Complete installation.

---

### ⚠️ Common Errors:

* Typos in environment variables (e.g., `WORDRESS_DB_USER` instead of `WORDPRESS_DB_USER`)
* Wrong passwords or mismatched DB name
* Docker container crashing (check logs: `docker logs wordpress`)

---

## 🔹 **Part 2: Dockerfile – Creating Your Own Custom Image**

### 🔸 What is a Dockerfile?

A **Dockerfile** is a text file with instructions to build a custom Docker image step-by-step.

---

### ✅ Example: Build Nginx Web Server from Ubuntu

```Dockerfile
# Use Ubuntu base image
FROM ubuntu

# Update and install nginx
RUN apt update -y
RUN apt install nginx -y

# Replace default index.html
RUN rm /var/www/html/index.html
RUN bash -c 'echo "hi....." > /var/www/html/index.html'

# Start nginx in foreground (so container doesn't exit)
CMD ["nginx", "-g", "daemon off;"]
```

---

### ✅ Build Docker Image:

```bash
docker build -t mynginximg .
```

* `-t`: Tags the image with a name
* `.`: Refers to current directory (should contain Dockerfile)

#### Alternative tags:

```bash
docker build -t myimg:1.0 .
docker build -t myimg:beta .
docker build -f myfile -t customimg .
```

---

### ✅ Run the Container:

```bash
docker run -d -p 90:80 mynginximg
```

Now browse: `http://<ec2-public-ip>:90`

**Make sure to open port 90** in your EC2 instance **security group**.

---

## 🔧 Dockerfile Command Summary

| Command        | Purpose                                 |
| -------------- | --------------------------------------- |
| `FROM`         | Base image                              |
| `RUN`          | Execute a shell command while building  |
| `CMD`          | Default command to run inside container |
| `COPY` / `ADD` | Add files to image                      |
| `WORKDIR`      | Set working directory                   |
| `EXPOSE`       | Inform about exposed port               |

---

## ✅ Summary

### WordPress Setup:

* Create MySQL container with DB and root password
* Link it to WordPress container
* Use environment variables correctly
* Visit EC2 IP to install WordPress

### Dockerfile Usage:

* Define custom image steps (e.g., Nginx setup)
* Build image with `docker build`
* Run container with exposed port

---
