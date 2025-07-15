---

## 🧠 **Docker Multi-Stage Build + Docker Compose**

---

## 🔹 Part 1: Hiding Layers with Multi-Stage Builds

### ❓ Why Hide Layers?

Every `RUN`, `COPY`, or `ADD` instruction in a Dockerfile creates a **layer** in your image. These layers:

* Increase image size
* May contain unnecessary files (like build tools, test files, secrets)
* Can expose security or build-time artifacts

---

### ✅ Solution: **Multi-Stage Builds**

Multi-stage builds use multiple `FROM` statements to separate build and production stages. Final images contain **only the needed artifacts**, **hiding all intermediate layers**.

---

### ✅ Example

```Dockerfile
# Stage 1: Build Stage
FROM node AS build
WORKDIR /usr/src/app
COPY package.json .          # Copy dependencies file
COPY . .                     # Copy all app files
RUN npm install              # Install all dependencies
CMD ["node", "index.js"]     # Dev CMD (not used in prod)

# Stage 2: Production Stage
FROM node AS production
WORKDIR /usr/src/app
COPY --from=build /usr/src/app ./dist
COPY --from=build /usr/src/app/package*.json ./

RUN npm install --only=production

EXPOSE 3000
CMD ["node", "dist/index.js"]
```

### 🔍 Explanation:

* First `FROM node AS build`: Downloads dev dependencies & builds the app
* Second `FROM node AS production`: Gets only required files (e.g. from `./dist`)
* `COPY --from=build`: Copies files from Stage 1 to Stage 2
* Final image is **small**, clean, and secure

---

### ✅ Build Image

```bash
docker build -t nodeimg .
```

Use `docker images` to verify image size (notice it's smaller than a full build).

---

## 🔹 Part 2: Docker Compose – WordPress + MySQL

### ❓ What is Docker Compose?

**Docker Compose** allows you to manage multi-container applications using a YAML file (`docker-compose.yml`). You define:

* Services (containers)
* Networks
* Volumes
* Dependencies

---

### ✅ Example Compose File (`wpwala.yml`)

```yaml
version: '3.9'

services: 
  db:
    image: mysql
    ports:
      - "3306:3306"
    volumes:
      - myvol:/var/lib/mysql
    networks:
      - mynet
    environment:
      - MYSQL_ROOT_PASSWORD=Pass@123
      - MYSQL_DATABASE=wordpressdb

  website:
    image: wordpress
    ports:
      - "80:80"
    networks:
      - mynet
    environment:
      - WORDPRESS_DB_HOST=db
      - WORDPRESS_DB_USER=root
      - WORDPRESS_DB_PASSWORD=Pass@123
      - WORDPRESS_DB_NAME=wordpressdb

networks:
  mynet:
    driver: bridge

volumes:
  myvol:
```

---

### ✅ Explanation of Compose Syntax

| Key            | Description                              |
| -------------- | ---------------------------------------- |
| `services:`    | Defines each container                   |
| `image:`       | Image used (pulled or local)             |
| `ports:`       | Port mapping: `"host:container"`         |
| `volumes:`     | Persistent storage                       |
| `environment:` | Environment variables                    |
| `networks:`    | Assigns network for containers           |
| `driver:`      | Network type (e.g., `bridge`, `overlay`) |

---

---

### ✅ Run Docker Compose

```bash
docker-compose -f wpwala.yml up
```

* Creates network `mynet`
* Creates volume `myvol`
* Pulls WordPress and MySQL images
* Starts containers

---

### ✅ Stop and Remove Everything

```bash
docker-compose -f wpwala.yml down
```

* Stops services
* Removes containers
* Keeps volumes and images unless `--volumes` is added:

```bash
docker-compose down --volumes
```

---

## 🧱 BONUS: Useful Concepts

### 🔸 1. **`.dockerignore` File**

Prevents unwanted files from being added to the image (like `.git`, `node_modules`):

```
node_modules
.git
*.log
Dockerfile
```

Place in same directory as Dockerfile.

---

### 🔸 2. **Compose Scaling**

Use Compose to scale containers:

```bash
docker-compose up --scale website=3
```

Useful for **load balancing** (when used with reverse proxy like Nginx or Traefik).

---

### 🔸 3. **Service Dependency**

You can delay start using `depends_on`:

```yaml
services:
  app:
    depends_on:
      - db
```

Note: `depends_on` doesn’t wait for DB to be *ready*, only for the container to *start*. Use **health checks** for that.

---

### 🔸 4. **Health Checks**

```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 30s
  timeout: 10s
  retries: 3
```

---

### 🔸 5. **Compose Logs**

To see live logs of all services:

```bash
docker-compose logs -f
```

---

### ✅ Summary

| Tool/Concept             | Purpose                           |
| ------------------------ | --------------------------------- |
| Multi-stage Build        | Reduce image size, remove secrets |
| Docker Compose           | Define & run multi-container apps |
| `docker-compose up/down` | Start/stop entire stack           |
| YAML `-`                 | List items (ports, env variables) |
| `.dockerignore`          | Exclude files from image          |
| Health Checks            | Wait for services before running  |

---
