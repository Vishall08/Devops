---

## 🧠 **Docker Image Sharing, Docker Hub, AWS ECR, and MySQL in Docker**

---

### 🔹 **Docker Hub**

**Docker Hub** is a cloud-based registry provided by Docker for storing, sharing, and managing Docker images.

#### ✅ Steps to Push an Image to Docker Hub:

1. **Create Docker Hub Account:**
   Go to [hub.docker.com](https://hub.docker.com) and sign up.

2. **Create Personal Access Token:**

   * Go to your account > **Account Settings**
   * Navigate to **Security** > **Personal Access Tokens**
   * Click **"Create Token"**
   * Set:

     * Token name
     * Expiry
     * Required permissions (e.g., read/write)
   * Copy and save the token securely

3. **Login to Docker Hub from CLI:**

   ```bash
   docker login -u your_dockerhub_username
   ```

   When prompted, **paste the token** as the password.

4. **Tag Your Local Docker Image:**

   ```bash
   docker tag mywebsite devops/mywebsite
   ```

   * Format: `docker tag SOURCE_IMAGE TARGET_IMAGE`
   * Replace `devops` with your Docker Hub username if different

5. **Push the Image to Docker Hub:**

   ```bash
   docker push devops/mywebsite
   ```

6. **Pull the Image on Another System:**

   ```bash
   docker pull devops/mywebsite
   ```

---

### 🔹 **Docker Image Save and Load (Offline Sharing)**

Sometimes, you want to **share an image without Docker Hub**, especially in restricted environments.

#### ✅ Save Docker Image to `.tar` File:

```bash
docker save -o myweb.tar nginx
```

* `-o myweb.tar`: Output file
* `nginx`: The name of the image

#### ✅ Load Docker Image from `.tar` File:

```bash
docker load -i myweb.tar
```

* This command creates the image locally from the tar file.

---

### 🔹 **AWS ECR (Elastic Container Registry)**

ECR is Amazon's managed Docker container registry.

#### ✅ Prerequisites:

* AWS CLI installed and configured
* IAM user with `AmazonEC2ContainerRegistryFullAccess` policy

#### ✅ Steps to Push Docker Image to ECR:

1. **Create a repository in ECR** (via AWS Console or CLI)

2. **Login to ECR via CLI:**

   ```bash
   aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com
   ```

3. **Tag the Docker Image:**

   ```bash
   docker tag mywebsite <aws_account_id>.dkr.ecr.<region>.amazonaws.com/mywebsite
   ```

4. **Push the Image to ECR:**

   ```bash
   docker push <aws_account_id>.dkr.ecr.<region>.amazonaws.com/mywebsite
   ```

5. **Pull from ECR (from any configured AWS CLI system):**

   ```bash
   docker pull <aws_account_id>.dkr.ecr.<region>.amazonaws.com/mywebsite
   ```

---

### 🔹 **Flags in Docker Commands**

| Short Form | Long Form   | Example                     |
| ---------- | ----------- | --------------------------- |
| `-d`       | `--detach`  | `docker run -d nginx`       |
| `-e`       | `--env`     | `docker run -e VAR=value`   |
| `-p`       | `--publish` | `docker run -p 80:80 nginx` |

Use `--help` after any command for more flags:

```bash
docker run --help
```

---

### 🔹 **Running MySQL in Docker**

Running databases like MySQL in containers is convenient for testing but should be used cautiously in production (due to persistence, security, and scalability concerns).

#### ❗ Disadvantages of Running MySQL in Docker (esp. in Production):

1. Data persistence issues unless volumes are used
2. Security: Exposing root password via `-e` variables is risky
3. Performance overhead with large datasets
4. Loss of data if container is deleted without volumes
5. Not suitable for clustering/replication without advanced setup

#### ✅ Command to Run MySQL Container:

```bash
docker run -d --name mysql-container -e MYSQL_ROOT_PASSWORD=Pass@123 mysql
```

* `-d`: Detached mode
* `--name mysql-container`: Assigns container a name
* `-e MYSQL_ROOT_PASSWORD=Pass@123`: Sets root password for MySQL

#### ✅ Access the Container Shell:

```bash
docker exec -it mysql-container /bin/bash
```

#### ✅ Login to MySQL inside Container:

```bash
mysql -u root -p
```

* Enter the password you provided: `Pass@123`

---

## ✅ Summary Table

| Command                                          | Description                           |
| ------------------------------------------------ | ------------------------------------- |
| `docker login -u <username>`                     | Login to Docker Hub                   |
| `docker tag source target`                       | Tag local image for Docker Hub or ECR |
| `docker push <image>`                            | Push image to registry                |
| `docker pull <image>`                            | Pull image from registry              |
| `docker save -o file.tar <image>`                | Save image as tar                     |
| `docker load -i file.tar`                        | Load image from tar                   |
| `docker run -d -e MYSQL_ROOT_PASSWORD=... mysql` | Start MySQL container                 |
| `docker exec -it <container> /bin/bash`          | Access container shell                |
| `mysql -u root -p`                               | Login to MySQL within container       |

---
