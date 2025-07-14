---

## 🧠**Advanced Dockerfile – MySQL + Nginx + Dockerfile Instructions**

---

## 🔸 Part 1: MySQL Dockerfile with `ARG` and `ENV`

### ✅ Your Dockerfile

```Dockerfile
FROM mysql

ARG MYSQL_PASSWORD
ENV MYSQL_ROOT_PASSWORD=${MYSQL_PASSWORD}

COPY init.sql /docker-entrypoint-initdb.d/
COPY init.sh /docker-entrypoint-initdb.d/

EXPOSE 3306
CMD ["mysqld"]
```

### ✅ Key Concepts:

* **`ARG`**: Used at build time (e.g., passing secrets like passwords safely)
* **`ENV`**: Sets environment variables available at **runtime**

📌 **Avoid writing passwords directly in the Dockerfile.** Instead use `ARG`:

```Dockerfile
ARG MYSQL_PASSWORD
ENV MYSQL_ROOT_PASSWORD=${MYSQL_PASSWORD}
```

Then build the image:

```bash
docker build --build-arg MYSQL_PASSWORD=Pass@123 -t mysqlimg .
```

---

### ✅ `init.sql` – Auto Database Setup

```sql
CREATE DATABASE insta;
USE insta;
CREATE TABLE users(id INT, name VARCHAR(29));
INSERT INTO users VALUES(1, 'vishal'), (2, 'ravi');
```

### ✅ `init.sh`

```bash
echo 'I am inside shell script file'
```

Placed in `/docker-entrypoint-initdb.d/` → automatically executed when MySQL starts.

---

### ✅ Run the Container

```bash
docker run -d --name mysqlwala mysqlimg
docker exec -it mysqlwala /bin/bash
mysql -u root -p
```

Password: `Pass@123` (used from `ARG`)

---

## 🔸 Part 2: Nginx Dockerfile with Custom Script

### ✅ Directory setup

```bash
mkdir customsh
cd customsh
```

### ✅ `Dockerfile`

```Dockerfile
FROM nginx

COPY custom.sh /usr/local/bin/custom.sh
RUN chmod +x /usr/local/bin/custom.sh

EXPOSE 80

ENTRYPOINT ["/bin/bash", "-c", "/usr/local/bin/custom.sh && nginx -g 'daemon off;'"]
```

🛠 Fix in your syntax:

* `ENTRYPOINT` line must not have an extra comma or quote

---

### ✅ `custom.sh`

```bash
cd /usr/share/nginx/html
rm index.html
touch index.html
echo "I am in nginx container" > index.html
```

### ✅ Build and Run

```bash
docker build -t mynginximg .
docker run -d -p 80:80 --name mynginx mynginximg
```

✅ Now, visit `http://<EC2-IP>` → you will see:

```
I am in nginx container
```

---

## 🧱 Dockerfile Instructions Explained in Detail

| Instruction                   | Description                                                        | Example                                                            |   |          |
| ----------------------------- | ------------------------------------------------------------------ | ------------------------------------------------------------------ | - | -------- |
| **FROM**                      | Base image to build on                                             | `FROM python:3.10-slim`                                            |   |          |
| **RUN**                       | Executes a shell command during build                              | `RUN apt update && apt install -y nginx`                           |   |          |
| **WORKDIR**                   | Sets the working directory in container                            | `WORKDIR /app`                                                     |   |          |
| **CMD**                       | Default command to run in container (can be overridden)            | `CMD ["node", "index.js"]`                                         |   |          |
| **ENTRYPOINT**                | Runs a command that is not overridden by `docker run`              | `ENTRYPOINT ["python"]`                                            |   |          |
| **ARG**                       | Defines build-time variables (can be passed via `--build-arg`)     | `ARG PASSWORD`                                                     |   |          |
| **ENV**                       | Sets environment variables for runtime                             | `ENV PORT=5000`                                                    |   |          |
| **COPY**                      | Copies files/folders into the container                            | `COPY app.py /app/`                                                |   |          |
| **ADD**                       | Same as COPY but supports remote URLs and auto-extracting archives | `ADD site.tar.gz /var/www/`                                        |   |          |
| **EXPOSE**                    | Documents the port container listens on                            | `EXPOSE 80`                                                        |   |          |
| **LABEL**                     | Adds metadata to the image                                         | `LABEL version="1.0"`                                              |   |          |
| **MAINTAINER** *(deprecated)* | Author information                                                 | `MAINTAINER Vishal`                                                |   |          |
| **ONBUILD**                   | Triggered only when the image is used as a base for another        | `ONBUILD COPY . /app/`                                             |   |          |
| **HEALTHCHECK**               | Define container health check command                              | \`HEALTHCHECK CMD curl --fail [http://localhost](http://localhost) |   | exit 1\` |
| **USER**                      | Specifies the user to run the container as                         | `USER node`                                                        |   |          |
| **VOLUME**                    | Creates a mount point for external storage                         | `VOLUME /data`                                                     |   |          |

---

## 📝 CMD vs ENTRYPOINT – What’s the difference?

| Feature                       | `CMD`                  | `ENTRYPOINT`                       |
| ----------------------------- | ---------------------- | ---------------------------------- |
| Default command?              | ✅ Yes                  | ✅ Yes                              |
| Can be overridden at runtime? | ✅ Yes                  | ❌ No (unless using `--entrypoint`) |
| Example                       | `CMD ["npm", "start"]` | `ENTRYPOINT ["python"]`            |

---

## ✅ Summary

* Use `ARG` to safely pass build-time secrets like passwords.
* Use `COPY init.sql` and `/docker-entrypoint-initdb.d/` to pre-load MySQL DBs.
* You can customize container startup behavior using `ENTRYPOINT` and shell scripts.
* Understand each Dockerfile instruction and when to use it.

---
