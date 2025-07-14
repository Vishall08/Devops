---

## 🧠Docker Networking + Node.js App + Nginx Proxy

---

## 🔸 Part 1: Docker Networking (Bridge, Host, Custom, CIDR)

### ✅ Step 1: Inspect Docker Networks

```bash
docker images
docker run -d --name pythonwala mypythonimg
docker ps
```

* `mypythonimg`: previously created Flask app image

### View Available Networks:

```bash
docker network ls
```

You’ll see:

* `bridge` (default network)
* `host`
* `none`
* Your custom networks (if any)

---

### ✅ Inspect Bridge Network:

```bash
docker network inspect bridge
```

This will show:

* Network ID
* Subnet
* Connected containers
* IP addresses assigned to each container

---

### ✅ Inspect Container Networking:

```bash
docker inspect pythonwala
```

Look for the `"Networks"` section inside JSON.
It shows:

* Container's internal IP
* Network it's attached to

---

### ✅ Connect a Container to a Custom Network

First, create a custom network if not already:

```bash
docker network create mynetwork
```

Then attach a running container to this network:

```bash
docker network connect mynetwork pythonwala
```

Check it:

```bash
docker network inspect mynetwork
```

Now both **bridge** and **mynetwork** are attached to the container.

---

### ✅ Disconnect a Container from Network

```bash
docker network disconnect mynetwork pythonwala
```

---

### ✅ Remove a Network

```bash
docker network rm mynetwork
```

⚠️ You can only remove a network if **no containers are attached**.

---

### ✅ Create Custom Network with CIDR Block

```bash
docker network create yournet --subnet 10.0.0.0/16
```

* CIDR allows you to control IP ranges.

Inspect it:

```bash
docker network inspect yournet
```

### Assign Container to Custom Network:

```bash
docker network connect yournet pythonwala
```

---

### ✅ Host Network

```bash
docker run -d --network host nginx
```

* Shares the host’s network interface (no port mapping needed)
* Works only on Linux (not supported on Docker Desktop for Mac/Windows)

📝 If you use `-p`, Docker defaults to **bridge** networking.

---

## 🔸 Part 2: Build a Node.js App in Docker

### ✅ Create Project Files

```bash
mkdir mynodeapp
cd mynodeapp
```

---

### ✅ Dockerfile

```Dockerfile
FROM node

WORKDIR /usr/src/app

COPY package.json .  
COPY . .  

RUN npm install

EXPOSE 3000

CMD ["node", "index.js"]
```

📝 **Fix**:

* Use `CMD ["node", "index.js"]` instead of `npm`

---

### ✅ `package.json`

```json
{
  "name": "node-app",
  "description": "hello",
  "version": "0.0.1",
  "private": true,
  "dependencies": {
    "express": "3.12.0"
  },
  "devDependencies": {
    "mocha": "10.2.0",
    "supertest": "6.3.3"
  }
}
```

---

### ✅ `index.js`

```js
var express = require('express');
var app = express();

app.get('/', function(req, res) {
  res.send('node app');
});

app.listen(process.env.PORT || 3000);
module.exports = app;
```

---

### ✅ Build Node Image

```bash
docker build -t mynodeimg .
```

Check image:

```bash
docker images
```

---

### ✅ Run Node App

```bash
docker run -d -p 80:3000 --name nodewala mynodeimg
```

* `-p 80:3000`: Maps host port 80 to container’s port 3000
* Access it at `http://<your-EC2-IP>`

---

## 🔸 Part 3: Add Nginx as Reverse Proxy for Node App

### ✅ Reuse or Create `default.conf`

```nginx
server {
    listen 80;

    location / {
        proxy_pass http://<container-ip>:3000;
    }
}
```

* Replace `<container-ip>` with the actual IP from:

```bash
docker inspect nodewala
```

---

### ✅ nginxDockerfile

```Dockerfile
FROM nginx
COPY default.conf /etc/nginx/conf.d/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

### ✅ Build Nginx Image for Node App

```bash
docker build -t nodenginx -f nginxDockerfile .
```

---

### ✅ Run Nginx Container

```bash
docker run -d -p 80:80 --name nginxnode nodenginx
```

**Now visit:** `http://<your-EC2-IP>`
It should display: `node app`
→ routed from Nginx to Node container.

---

## ✅ Summary Table

| Command                          | Purpose                             |
| -------------------------------- | ----------------------------------- |
| `docker network ls`              | List all networks                   |
| `docker network create --subnet` | Create custom network with IP range |
| `docker network connect`         | Add container to a network          |
| `docker network disconnect`      | Remove container from network       |
| `docker build -t <name>`         | Build image from Dockerfile         |
| `docker run -p`                  | Run container with port mapping     |
| `docker inspect`                 | Get container details including IP  |

---
