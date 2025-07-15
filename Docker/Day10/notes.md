---

## 🧱 **Three-Tier Web Architecture Using Docker Compose**

---

### 🧭 What is a Three-Tier Architecture?

A **Three-Tier Web App** separates the application into:

| Tier     | Component                   | Responsibility                             |
| -------- | --------------------------- | ------------------------------------------ |
| Web Tier | Nginx (or Apache)           | Handles HTTP requests, serves static files |
| App Tier | PHP (or Node, Python, etc.) | Processes business logic                   |
| DB Tier  | MySQL (or PostgreSQL)       | Stores application data                    |

This design improves **modularity, scalability, and security**.

---

## ✅ Folder Structure (Best Practice)

```
mywebsite/
├── docker-compose.yml
├── web/
│   ├── Dockerfile (optional)
│   └── myhtmlcode/
│       └── index.html
├── app/
│   ├── Dockerfile
│   └── myphpcode/
│       └── index.php
├── db/
│   ├── Dockerfile
│   ├── init.sql
│   └── init.sh
```

---

## ✅ docker-compose.yml 

```yaml
version: '3.9'

services:
  web:
    image: nginx
    ports:
      - "80:80"
    volumes:
      - ./web/myhtmlcode:/usr/share/nginx/html/
    networks:
      - webnet

  app:
    build:
      context: ./app
    ports:
      - "9000:9000"
    volumes:
      - ./app/myphpcode:/usr/share/nginx/html/
    networks:
      - webnet
      - appnet

  db:
    build:
      context: ./db
    environment:
      - MYSQL_ROOT_PASSWORD=Pass@123
      - MYSQL_DATABASE=appdb
    ports:
      - "3306:3306"
    volumes:
      - databackup:/var/lib/mysql
    networks:
      - appnet

volumes:
  databackup:

networks:
  webnet:
  appnet:
```

---

## ✅ Dockerfile for DB (`db/Dockerfile`) –

```Dockerfile
FROM mysql:latest

ENV MYSQL_ROOT_PASSWORD=Pass@123
COPY init.sql /docker-entrypoint-initdb.d/
COPY init.sh /docker-entrypoint-initdb.d/

EXPOSE 3306
CMD ["mysqld"]
```

---

## ✅ Example `init.sql` (DB Auto Setup)

```sql
CREATE DATABASE appdb;
USE appdb;
CREATE TABLE users (id INT, name VARCHAR(50));
INSERT INTO users VALUES (1, 'Vishal'), (2, 'Ravi');
```

---

## ✅ Dockerfile for App Tier (`app/Dockerfile`) – 

```Dockerfile
FROM php:8.2-fpm

RUN docker-php-ext-install mysqli pdo pdo_mysql gd

WORKDIR /usr/share/nginx/html
```


---

## ✅ Example `index.php` (Inside `app/myphpcode/`)

```php
<?php
$servername = "db";
$username = "root";
$password = "Pass@123";
$dbname = "appdb";

$conn = new mysqli($servername, $username, $password, $dbname);

if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
echo "Connected successfully to MySQL!";
?>
```

---

## ✅ How It All Works

| Tier         | Description                                             |
| ------------ | ------------------------------------------------------- |
| **Web**      | Nginx serves static HTML from `./web/myhtmlcode`        |
| **App**      | PHP-FPM container processes dynamic PHP code            |
| **DB**       | MySQL stores app data, initialized with `init.sql`      |
| **Networks** | Web + App talk via `webnet`, App + DB talk via `appnet` |
| **Volumes**  | `databackup` stores persistent MySQL data               |

---

## ✅ Commands to Run

```bash
docker-compose up --build
```

To stop and remove containers:

```bash
docker-compose down
```

---

## 🧪 Test the Setup

After running `docker-compose up`:

1. Access `http://localhost` – Nginx static site
2. Test `http://localhost:9000/index.php` – PHP connects to MySQL
3. Use `docker exec -it <container> bash` to inspect container behavior

---

## ✅ Summary

| Component      | Tool                  | Notes                     |
| -------------- | --------------------- | ------------------------- |
| Web Tier       | Nginx                 | Serves static files       |
| App Tier       | PHP-FPM               | Executes dynamic code     |
| DB Tier        | MySQL                 | Stores app data           |
| Volumes        | Docker Volume         | Persistent MySQL data     |
| Networks       | Custom                | Webnet, Appnet separation |
| Initialization | `init.sql`, `init.sh` | Auto setup for DB         |

---
