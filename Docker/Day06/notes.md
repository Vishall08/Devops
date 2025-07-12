---

## 🧠 **Day 08: Docker – Python App with Flask + Nginx Proxy + Networking**

---

## 🔹 **Goal**

You’ll build and run:

* A **Python Flask app** in a Docker container
* A **custom Nginx container** to reverse proxy requests to Flask
* Use Docker **networks** to connect the two containers

---

## 🔸 Step 1: Setup Flask App in Docker

### ✅ 1. Create Project Directory

```bash
mkdir mypythonapp
cd mypythonapp
```

---

### ✅ 2. Create `Dockerfile` for Python App

```Dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt /app/

RUN pip install -r requirements.txt

COPY ./app/ .

EXPOSE 5000

CMD ["python", "app.py"]
```

**Explanation:**

* `FROM`: Base image with Python 3.9
* `WORKDIR`: Working directory inside container
* `COPY requirements.txt`: Add dependencies file
* `RUN pip install`: Install dependencies (Flask)
* `COPY ./app/ .`: Copy app code from host to container
* `EXPOSE 5000`: Declare container port
* `CMD`: Start the Flask app

---

---

### ✅ 3. Create `requirements.txt`

```text
Flask
```

---

### ✅ 4. Create Flask App in `app.py`

```python
from flask import Flask
import os

app = Flask(__name__)

@app.route('/')
def hello_geek():
    return '<h1>Hello from Flask & Docker</h1>'

@app.route('/hi')
def hell():
    return '<h1>Hiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiiii from Flask & Docker</h1>'

if __name__ == "__main__":
    port = int(os.environ.get('PORT', 5000))
    app.run(debug=True, host='0.0.0.0', port=port)
```

---

### ✅ 5. Build the Docker Image

```bash
docker build -t mypythonimg .
```

Check built images:

```bash
docker images
```

---

### ✅ 6. Run Flask App in Container

```bash
docker run -d -p 80:5000 --name pythonwala mypythonimg
```

**Explanation:**

* `-d`: Run in background
* `-p 80:5000`: Maps host port 80 to container’s port 5000
* `--name`: Name of the container

✅ Visit: `http://<your-EC2-Public-IP>`
You should see the Flask app homepage!

---

## 🔸 Step 2: Add Nginx as Reverse Proxy

### ✅ 1. Run Nginx (optional basic step)

```bash
docker run -d nginx
docker exec -it <container_id> /bin/bash
```

---

### ✅ 2. Create `default.conf` File

This config tells Nginx to forward traffic to the Flask container.

```nginx
server {
    listen 80;

    location / {
        proxy_pass http://pythonwala:5000;
    }
}
```

* `proxy_pass`: Points to container name on the same network

---

### ✅ 3. Create Custom `nginxDockerfile`

```Dockerfile
FROM nginx
COPY default.conf /etc/nginx/conf.d/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

### ✅ 4. Build Custom Nginx Image

```bash
docker build -t majhanginximg -f nginxDockerfile .
docker images
```

---

## 🔸 Step 3: Docker Networking

### 🧠 Why Use Docker Networks?

Containers can communicate via:

1. **Bridge Network (default)** – Connect via IP
2. **Host Network** – Shares host's network stack
3. **None Network** – No network access

We'll use a **custom bridge network** so containers can use **container names instead of IPs**.

---

### ✅ 1. Create Network

```bash
docker network create mynetwork
```

View all networks:

```bash
docker network ls
```

---

### ✅ 2. Run Flask App on the Custom Network

```bash
docker run -d --network mynetwork --name myapp mypythonimg
```

---

### ✅ 3. Run Nginx on Same Network

```bash
docker run -d -p 80:80 --network mynetwork --name mynginx majhanginximg
```

Now, Nginx will forward traffic to the Flask app using the name `myapp`.

---

### ✅ 4. Test in Browser

* Visit: `http://<EC2-Public-IP>/`
* You’ll see: `Hello from Flask & Docker`
* Visit: `http://<EC2-Public-IP>/hi`
* Output: Your `/hi` route response

---

## 🔧 Bonus: Use Docker Inspect

Find the IP address of a container (if needed):

```bash
docker inspect <container_id>
```

Search for the `"IPAddress"` in the JSON output.

---

## ✅ Summary Table

| Step                    | Description                    |
| ----------------------- | ------------------------------ |
| `Dockerfile`            | Build Flask app into image     |
| `requirements.txt`      | Install Flask                  |
| `app.py`                | Simple Python Flask web app    |
| `docker build`          | Builds image                   |
| `docker run`            | Runs app on container          |
| `default.conf`          | Nginx config to proxy to Flask |
| `nginxDockerfile`       | Custom Nginx image             |
| `docker network create` | Makes shared network           |
| `--network mynetwork`   | Connects containers logically  |

---
