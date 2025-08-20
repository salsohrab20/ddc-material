# 🐳 Docker & Container Technology - Notes

## 🚀 Introduction

* Goal: Understand container technology, focusing on Docker.
* We'll compare containers with Virtual Machines (VMs).
* Misconceptions early on can hinder learning—so foundational clarity is key.

---

## 🧱 Why We Need Containers

### 🧩 Service Isolation

* Services like Tomcat, NGINX, MySQL need to be isolated.
* In traditional setups, each service runs on a separate VM.
* Isolation avoids:

    * Resource contention (RAM/CPU).
    * Conflicting dependencies (libraries, configs).

### 💰 Drawbacks of VMs

* Each VM requires:

    * Full OS (licensing, boot time, maintenance).
    * Over-provisioned resources for high availability.
* Results in:

    * High **CAPEX** (Capital Expenditure).
    * High **OPEX** (Operational Expenditure).
* Portability exists, but VMs are large and heavy.

---

## 📦 Containers: The Better Way

### 🔍 What is a Container?

* **Containers isolate processes**, not full systems.
* No full OS inside, just what the app needs (binaries, libraries).
* Use host OS kernel (not separate OS per container).
* Lightweight, fast startup, highly portable.

### 🧠 How it Works

* **"Kernel trick"**: Uses Linux namespaces, cgroups.
* Each container is:

    * A **process** isolated in a **directory**.
    * Given access to only its own files and resources.
    * Can be assigned virtual IPs.
* Efficiently shares host OS resources.

---

## 🖥️ VMs vs Containers

| Feature        | Virtual Machines        | Containers                   |
| -------------- | ----------------------- | ---------------------------- |
| OS Requirement | Each VM has its own OS  | Share host OS kernel         |
| Resource Usage | Heavy                   | Lightweight                  |
| Boot Time      | Slow                    | Fast                         |
| Portability    | Moderate (large images) | High (small, standard units) |
| Isolation Type | Hardware-level          | Process-level (OS-level)     |

> ❗ Technically, containers are not virtualization—they offer **process isolation**, not OS virtualization.

---

## 🐳 What is Docker?

* **Docker = Tooling + Engine** to manage containers.
* **Docker Inc.**: Company behind Docker, evolved from DotCloud (a PaaS).
* Made containers easier via:

    * Docker Engine
    * Docker CLI
    * Docker Compose
    * **Docker Images**

### 🎯 Purpose of Docker

* Simplifies container creation, deployment, and management.
* Uses standardized **images** for consistent dev/test/prod environments.
* Open-source, widely adopted, efficient.

---

## 🛠️ Docker Internals

* **Docker Engine**:

    * Daemon (`dockerd`) running on host OS.
    * Connect via CLI or REST API.
* **Docker Images**:

    * Blueprint for containers (includes app + dependencies).
    * Pulled from Docker Hub or custom registries.

---

## 🖼️ Docker Architecture vs VM Architecture

```plaintext
[VM Architecture]                         [Docker Architecture]
---------------------------------         -------------------------------
| Hardware               |                | Hardware                   |
| Host OS               |                | Host OS                   |
| Hypervisor            |                | Docker Engine             |
| VM OS | VM OS | VM OS |                | Container | Container |... |
| App  | App  | App     |                | App      | App              |
---------------------------------         -------------------------------
```

* **VMs**: Full OS per instance → more overhead.
* **Docker**: Shared OS kernel → more efficient.

---

## 🛡️ Benefits of Docker

* **Portability**: Run same image on any platform with Docker.
* **Speed**: Near-instant startup.
* **Efficiency**: Lower RAM/CPU usage.
* **Security**: Strong isolation between containers.
* **Standardization**: Image-based development and deployment.

---

## 🧪 Getting Hands-On (Coming Up)

### ⚙️ Setup

* Launch an EC2 instance (Ubuntu).
* Install Docker Engine.
* Run Linux containers on it.

> ❗ Note: Containers use host OS kernel.

* Linux containers need Linux host.
* Windows containers need Windows host.
* **Docker Desktop** on Windows runs Linux containers by spinning up a hidden Linux VM.

---

## 🕰️ Docker History (Quick Recap)

* **Before Docker**: Linux containers (LXC) existed but were hard to manage.
* **DotCloud (PaaS)** used LXC internally → failed as business.
* Released tooling as open source → **Docker** was born (\~2013).
* Standardized image-based container system revolutionized dev & ops.

---

## 📘 Key Takeaways

* Containers provide **process-level isolation** using host OS.
* Docker is the most popular **container runtime environment**.
* Containers are **not mini VMs**.
* Docker simplifies:

    * Creating containers
    * Managing dependencies
    * Shipping and deploying applications

---

# 🚀 Docker Setup on EC2 (Ubuntu 18.04)

## 📌 Objective

Launch an EC2 instance and install **Docker Engine** on **Ubuntu 18.04**, following official documentation.

---

## 🖥️ EC2 Instance Setup

### ✅ Instance Configuration

* **AMI**: Ubuntu 18.04 LTS
* **Instance Type**: `t2.micro` (Free tier eligible)
* **Name**: `docker-engine`
* **Key Pair**: Create a new or use existing
* **Security Group**:

    * Inbound Rules:

        * **All traffic** from **My IP** (for accessing containers on various ports)

---

## 🧪 Docker Installation (via Official Docs)

### 📚 Reference: [Docker Docs - Install on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

### 🔧 Step-by-Step Commands

1. **Update packages & install dependencies**

   ```bash
   sudo apt-get update
   sudo apt-get install ca-certificates curl gnupg lsb-release
   ```

2. **Add Docker’s GPG key**

   ```bash
   sudo mkdir -m 0755 -p /etc/apt/keyrings
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
     sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
   ```

3. **Add Docker repository**

   ```bash
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
     https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

4. **Install Docker Engine**

   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```

5. **Verify Docker is running**

   ```bash
   sudo systemctl status docker
   ```

---

## 👤 Configure Docker for Non-root User

By default, only the `root` user can run Docker commands.

### Add current user (e.g., `ubuntu`) to the `docker` group:

```bash
sudo usermod -aG docker ubuntu
```

> ⚠️ **Log out and back in** for group changes to take effect.

### Confirm access:

```bash
docker images
```

---

## ✅ Test Docker Installation

### Run a test container

```bash
docker run hello-world
```

* Pulls the `hello-world` image from Docker Hub
* Prints a confirmation message
* Container then exits (short-lived)

### Check containers:

* Running containers:

  ```bash
  docker ps
  ```

* All containers (including exited):

  ```bash
  docker ps -a
  ```

> Note: Docker automatically assigns fun names like `modest_query` to unnamed containers.

---

## 🔄 Docker Container Types

| Type         | Description                                      |
| ------------ | ------------------------------------------------ |
| Short-lived  | Run and exit after task (e.g., logging, scripts) |
| Long-running | Stay active (e.g., Apache, Tomcat, MySQL)        |

---

## 📝 Summary

* EC2 instance set up with Ubuntu 18.04
* Docker CE (Community Edition) installed
* Non-root user configured to use Docker
* Installation tested using `hello-world` container

---

## 🎯 Next Steps

* Explore common Docker CLI commands
* Work with custom images and containers
* Create Dockerfiles and multi-container apps using Docker Compose

---

Here’s a refined and structured **continuation of your Docker notes**, summarizing image/container management and essential Docker CLI commands — formatted for `.md` style.

---

# 🐳 Docker Basics – Image & Container Commands

## 📦 Image Management

### 🔍 List Local Images

```bash
docker images
```

### 🗑️ Remove Docker Images

1. Remove image by name or ID:

   ```bash
   docker rmi <image_name_or_id>
   ```

2. Example:

   ```bash
   docker rmi hello-world
   docker rmi <image_id_1> <image_id_2>
   ```

> ⚠️ **Note**: You **must remove containers** that were created from an image **before removing the image itself**.

---

## 📋 Container Management

### ▶️ Run a Container

```bash
docker run <image_name>
```

### 🚀 List Running Containers

```bash
docker ps
```

### 📜 List All Containers (including stopped)

```bash
docker ps -a
```

### 🧑‍💻 Run Commands in a Running Container

```bash
docker exec -it <container_id_or_name> <command>
```

* Example:

  ```bash
  docker exec -it my_container bash
  ```

### 📎 Attach to a Running Container (interactive)

```bash
docker attach <container_id_or_name>
```

### ⏹️ Stop a Running Container

```bash
docker stop <container_id_or_name>
```

### 🔁 Restart a Container

```bash
docker restart <container_id_or_name>
```

### 🧹 Remove a Container

```bash
docker rm <container_id_or_name>
```

---

## 🕵️ Inspecting

### 🔍 View Detailed Container Info

```bash
docker inspect <container_id_or_name>
```

> We'll explore `docker inspect` in detail in the next section.

---

## 📚 Resources

* 📖 [Docker CLI Command Reference](https://docs.docker.com/engine/reference/commandline/docker/)
* 🎓 Practice frequently to get comfortable with the CLI.

---

## ✅ Summary

* Cleaned up local environment (removed all containers and images).
* Practiced:

    * Running containers
    * Listing containers/images
    * Managing container lifecycle
    * Removing images (with dependency consideration)



# 🐳 Docker Logs & Troubleshooting

## 📓 What Are Docker Logs?

Docker logs are **standard output (stdout)** and **standard error (stderr)** of the primary process running inside a container. These logs can help with:

* Debugging issues during container startup
* Understanding application behavior
* Monitoring output of long-running or short-lived processes

---

## 🧪 Docker Logs Basics

### ▶️ Run a Container (e.g., NGINX)

```bash
docker pull nginx
docker run -d -P nginx
```

### 🧾 View Container Logs

```bash
docker logs <container_name_or_id>
```

> Displays output from the commands defined in the image’s `ENTRYPOINT` and `CMD`.

### 🧐 Example Output

If running the NGINX container:

* You’ll see logs from the NGINX startup process.
* These logs are normally hidden when the container is in `detached` mode (`-d`).

---

## 📌 Foreground vs Detached Mode

### Run in Foreground (No `-d`)

```bash
docker run -P nginx
```

* Logs are shown directly in your terminal.
* Pressing `Ctrl+C` will **stop the container** and **terminate the process**.

---

## ⚠️ Troubleshooting Containers That Exit Immediately

### 🧵 Example: MySQL

```bash
docker run -d mysql:5.7
```

* Container exits immediately.
* Use `docker ps -a` to see exited containers.

### 🔍 View Logs of Failed Container

```bash
docker logs <container_id>
```

**Example Output:**

```
Error: Database is uninitialized and password option is not specified
```

The log clearly explains the error: you must specify the `MYSQL_ROOT_PASSWORD` environment variable.

---

## ✅ Fixing the Issue: Use `-e` Flag

```bash
docker run -d -e MYSQL_ROOT_PASSWORD=my-secret mysql:5.7
```

Now the container runs successfully.

---

## 🧠 Key Concepts Recap

* `docker logs` shows output from the container’s **entrypoint and command**.
* Logs are useful for **debugging custom images** and **misconfigured containers**.
* Always check logs when a container fails to start.
* Logs only capture **stdout/stderr** from the main process, not system logs.

---

## 🧹 Cleanup Commands

Before moving on:

```bash
# Stop all containers
docker stop $(docker ps -aq)

# Remove all containers
docker rm $(docker ps -aq)

# Remove all images
docker rmi $(docker images -q)
```

---

## 📚 Additional Resources

* 🔗 [Docker Logs Documentation](https://docs.docker.com/engine/reference/commandline/logs/)
* 🛠️ Use `docker inspect` to check what commands an image runs:

  ```bash
  docker inspect <image_name>
  ```

Look for `Entrypoint` and `Cmd` fields in the JSON.
---

# 🐳 Docker Volumes & Persistent Data

Containers are **disposable** and **stateless** by nature. Any data stored inside a container will be lost when the container is stopped or removed.

To persist data outside the container, Docker provides two key mechanisms:

---

## ⚠️ Why Data Persistence Matters

* Containers are **volatile** and **ephemeral**.
* Docker containers are meant to be **replaced, not modified**.
* If a container is deleted, its internal filesystem is lost.
* **Stateful apps** like **MySQL** require persistent storage.

---

## 🧰 Docker Persistence Options

### 1. **Volumes** (Preferred)

* Managed by Docker.
* Stored under `/var/lib/docker/volumes`.
* Best for production and long-term data.
* Easily reusable by new containers.

### 2. **Bind Mounts**

* You specify a host directory to mount inside the container.
* More flexible and visible.
* Useful for development (e.g., syncing code).

---

## 🛠️ Volume vs Bind Mount

| Feature           | Volume                         | Bind Mount                          |
| ----------------- | ------------------------------ | ----------------------------------- |
| Managed by Docker | ✅ Yes                          | ❌ No                                |
| Host directory    | Hidden (`/var/lib/docker/...`) | You choose (`/home/user/data`)      |
| Use case          | Persistent container data      | Live code sync, host-container data |
| Performance       | Better in Docker Desktop       | Depends on FS & Docker driver       |

---

## 🐘 Example: MySQL with Bind Mount

### 🔍 Get MySQL Official Image

```bash
docker pull mysql:5.7
```

### 🔎 Inspect Image

```bash
docker inspect mysql:5.7
```

* Data stored at: `/var/lib/mysql`
* Ports exposed: `3306`, `33060`
* CMD: `mysqld`
* Environment variables: `MYSQL_ROOT_PASSWORD`

### 📂 Create Host Directory

```bash
mkdir ~/vprodbdata
```

### 🚀 Run MySQL with Bind Mount

```bash
docker run -d \
  --name vprodb \
  -e MYSQL_ROOT_PASSWORD=secretpass \
  -p 3030:3306 \
  -v /home/ubuntu/vprodbdata:/var/lib/mysql \
  mysql:5.7
```

> ✅ MySQL now stores data in `/home/ubuntu/vprodbdata`.

### 📁 Verify Bind Mount

```bash
ls ~/vprodbdata
```

* Data from container will appear here.

---

## 📦 Docker Volumes (Preferred Method)

### 🔧 Create Docker Volume

```bash
docker volume create mydbdata
```

### 📄 List Volumes

```bash
docker volume ls
```

### 🚀 Run MySQL with Volume

```bash
docker run -d \
  --name vprodb \
  -e MYSQL_ROOT_PASSWORD=secretpass \
  -p 3030:3306 \
  -v mydbdata:/var/lib/mysql \
  mysql:5.7
```

### 📂 Volume Storage Location

Volumes are stored in:

```
/var/lib/docker/volumes/mydbdata/_data
```

You can inspect contents from the host.

---

## 🔍 Inspect Container with `docker inspect`

```bash
docker inspect vprodb
```

### Useful Fields:

* `Mounts`: Shows volume or bind mount details.
* `HostConfig.PortBindings`: Port mappings.
* `Config.Env`: Environment variables.
* `NetworkSettings.IPAddress`: Internal container IP.
* `LogPath`: Log file path.

---

## 🛠️ Connect to MySQL via Container IP

```bash
# Get internal container IP
docker inspect vprodb | grep IPAddress

# Connect to MySQL
mysql -h <container-ip> -u root -p
```

> 🔑 Use the password set with `MYSQL_ROOT_PASSWORD`

---

## 🧹 Cleanup

```bash
# Stop and remove container
docker stop vprodb
docker rm vprodb

# Remove image
docker rmi mysql:5.7

# Optional: Remove volume
docker volume rm mydbdata
```

---

## ✅ Summary

* Containers are **stateless**; volumes are key to **persisting data**.
* **Bind mounts** are great for development.
* **Volumes** are better for persistent application data.
* Use `docker inspect` to discover:

    * Volume paths
    * Ports
    * Environment variables
    * Entrypoint and CMD
* Logs can be viewed with:

  ```bash
  docker logs <container_name>
  ```

---

# 🛠️ Building and Publishing Docker Images

In this lesson, we learn how to:

* Build a Docker image from a custom `Dockerfile`
* Containerize a web application
* Push the image to Docker Hub
* Pull and run it anywhere

---

## 📦 Why Build Docker Images?

So far, we've used **official images** from Docker Hub. But for real-world needs, we often:

* Start from a base image
* Install additional packages
* Copy app code/configs
* Set environment variables
* Define entry points

✅ This is **similar to creating a custom AMI on AWS**.

---

## 🧾 Dockerfile Basics

A `Dockerfile` is a text file with **instructions** to build a Docker image.

### 🔧 Common Dockerfile Instructions

| Instruction  | Description                                                     |
| ------------ | --------------------------------------------------------------- |
| `FROM`       | Base image (e.g., `ubuntu`, `nginx`)                            |
| `LABEL`      | Metadata (e.g., `author`, `project`)                            |
| `RUN`        | Execute commands (e.g., `apt install`)                          |
| `COPY`       | Copy files from host to image                                   |
| `ADD`        | Like `COPY` but supports remote URLs and auto-extracts archives |
| `CMD`        | Default command to run (`docker run`)                           |
| `ENTRYPOINT` | Similar to `CMD`, has higher priority                           |
| `EXPOSE`     | Informs Docker that the container listens on given ports        |
| `VOLUME`     | Define mount points for persistence                             |
| `ENV`        | Set environment variables                                       |
| `USER`       | Define which user runs the container                            |
| `WORKDIR`    | Set default working directory                                   |
| `ARG`        | Build-time variables                                            |
| `ONBUILD`    | Instructions to trigger when used as base image                 |

---

## 🌐 Web App Example: Containerizing a Static Website

We’ll host a static HTML website using `apache2` inside an `ubuntu` container.

### 🔗 Get Template

* Website template: [tooplate.com](https://www.tooplate.com)
* Selected template: **Nanofolio**

```bash
mkdir -p images/nano && cd images/nano

# Download and unzip
wget <link-to-nanofolio-template>.zip
sudo apt install unzip -y
unzip nanofolio.zip

# Create a tarball of the web files
tar -czvf nano.tar.gz *

# Move tarball to working directory and cleanup
mv nano.tar.gz ..
cd ..
rm -rf nano/
```

---

## 📝 Writing the Dockerfile

```Dockerfile
# Dockerfile
FROM ubuntu:latest

LABEL author="yourname"
LABEL project="nano"

# Avoid interactive prompts
ENV DEBIAN_FRONTEND=noninteractive

# Install packages
RUN apt update
RUN apt install apache2 -y

# Set working directory
WORKDIR /var/www/html

# Add our web content (auto-extracts)
ADD nano.tar.gz /var/www/html

# Expose port 80
EXPOSE 80

# Define volume for Apache logs
VOLUME ["/var/log/apache2"]

# Start Apache in foreground
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

---

## 🛠️ Build the Image

```bash
docker build -t nanoimage:v1 .
```

> If build fails due to `tzdata` or any prompts, ensure `ENV DEBIAN_FRONTEND=noninteractive` is set early in the Dockerfile.

---

## 🚀 Run the Container

```bash
docker run -d --name nanoweb -p 9080:80 nanoimage:v1
```

Visit: `http://<host-ip>:9080`

---

## 🧼 Troubleshooting & Cleanup

```bash
# Stop and remove container
docker stop nanoweb
docker rm nanoweb

# Remove image
docker rmi nanoimage:v1
```

---

## ☁️ Push to Docker Hub

### 1. ✅ Login

```bash
docker login
```

### 2. 🏷️ Tag Image Properly

```bash
docker tag nanoimage:v1 <dockerhub-username>/nanoimage:v2
```

E.g.:

```bash
docker tag nanoimage:v1 kubeimran/nanoimage:v2
```

### 3. 📤 Push to Docker Hub

```bash
docker push kubeimran/nanoimage:v2
```

* If image doesn't exist on Docker Hub, it will be **created automatically**.
* Docker will reuse Ubuntu base layers (faster push).

---

## 🌍 Pull and Run from Anywhere

```bash
docker run -d --name nanoweb -p 9080:80 kubeimran/nanoimage:v2
```

> Anyone can pull and run the same container!

---

## ✅ Summary

| Task          | Command                                         |
| ------------- | ----------------------------------------------- |
| Create Image  | `docker build -t name:tag .`                    |
| Tag Image     | `docker tag name:tag user/name:tag`             |
| Push Image    | `docker push user/name:tag`                     |
| Run Container | `docker run -d -p host:container user/name:tag` |

---

## 🎯 You’ve Learned

* Writing and structuring a Dockerfile
* Building a custom image from Ubuntu
* Running a web app in a container
* Publishing an image to Docker Hub
* Pulling and reusing containers anywhere

---

# 🧭 Dockerfile Instructions: `CMD` vs `ENTRYPOINT`

In this lecture, we clarify the often-confusing difference between `CMD` and `ENTRYPOINT` in Dockerfiles by looking at **3 different examples**.

---

## 🧠 Basic Concepts

### 🔹 `CMD`

* Sets **default command or arguments** for the container.
* Can be **overridden** by user when running the container.
* If both `ENTRYPOINT` and `CMD` exist, `CMD` provides **arguments** to `ENTRYPOINT`.

### 🔹 `ENTRYPOINT`

* Sets the **fixed command** that **always runs**.
* If used alone without arguments, the user **must** supply arguments when running the container.
* Has **higher priority** than `CMD`.

---

## 📁 Directory Structure

```
entry-cmd/
├── CMD/
│   └── Dockerfile
├── ENTRY/
│   └── Dockerfile
└── ENTRY_CMD/
    └── Dockerfile
```

---

## 📘 1. CMD Example

### `CMD/Dockerfile`

```Dockerfile
FROM ubuntu:latest
CMD ["echo", "hello"]
```

### 🔧 Build & Run

```bash
docker build -t printer CMD/
docker run printer
```

### ✅ Output

```
hello
```

---

## 📘 2. ENTRYPOINT Example

### `ENTRY/Dockerfile`

```Dockerfile
FROM ubuntu:latest
ENTRYPOINT ["echo"]
```

### 🔧 Build & Run

```bash
docker build -t printer:v2 ENTRY/
docker run printer:v2            # Empty output
docker run printer:v2 hello      # Output: hello
```

### ✅ Behavior

* `ENTRYPOINT` sets the command (`echo`)
* User must provide the argument

---

## 📘 3. ENTRYPOINT + CMD Example

### `ENTRY_CMD/Dockerfile`

```Dockerfile
FROM ubuntu:latest
ENTRYPOINT ["echo"]
CMD ["hello"]
```

### 🔧 Build & Run

```bash
docker build -t printer:v3 ENTRY_CMD/
docker run printer:v3
docker run printer:v3 "hello world"
```

### ✅ Output

```
# Default
hello

# Overridden CMD
hello world
```

---

## 🔄 Summary Table

| Scenario | ENTRYPOINT   | CMD                   | Behavior                                                     |
| -------- | ------------ | --------------------- | ------------------------------------------------------------ |
| 1        | ❌            | ✅ `["echo", "hello"]` | Runs `echo hello`                                            |
| 2        | ✅ `["echo"]` | ❌                     | Requires runtime argument: `docker run image hello`          |
| 3        | ✅ `["echo"]` | ✅ `["hello"]`         | Default is `echo hello`; can override: `docker run image hi` |

---

## 🧠 Key Takeaways

* `ENTRYPOINT` defines **what to run**
* `CMD` defines **default arguments**
* If both are present:

    * `ENTRYPOINT` = command
    * `CMD` = arguments (can be overridden)

```bash
# Dockerfile
ENTRYPOINT ["mybinary"]
CMD ["default-arg"]
# docker run image new-arg
# Runs: mybinary new-arg
```

---

## 🧪 Practice

Download the three `Dockerfile`s and test them yourself to fully grasp:

* How Docker interprets each instruction
* When to use `CMD` vs `ENTRYPOINT`
* How to override arguments at runtime

---

# 🐳 Docker Compose: Simplifying Multi-Container Applications

## 📌 What is Docker Compose?

**Docker Compose** is a tool for defining and running multi-container Docker applications using a YAML file.

> Think of it like **Vagrant** but for **Docker containers**.
> Instead of managing containers manually with `docker run`, Compose lets you automate everything using a single file.

---

## 🧩 Why Docker Compose?

* Run **multiple containers** together (e.g., app + DB + Redis)
* Avoid mistakes from long `docker run` commands
* Reproducible environments (great for development and CI/CD)
* Store Compose file in version control
* Treat infrastructure as code

---

## ⚙️ Docker Compose Installation

### 🔧 On Linux:

```bash
sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version
```

---

## 🏗️ Compose Project Structure

### 📁 Example directory:

```
myproject/
├── app.py
├── Dockerfile
├── requirements.txt
└── docker-compose.yml
```

---

## 🐍 Flask Example App

### `app.py`:

```python
import time
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)

def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as e:
            if retries == 0:
                raise e
            retries -= 1
            time.sleep(0.5)

@app.route('/')
def hello():
    count = get_hit_count()
    return f'Hello World! I have been seen {count} times.\n'
```

### `requirements.txt`:

```
flask
redis
```

---

## 🐳 Dockerfile

```Dockerfile
FROM python:3.9-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0

RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

COPY . .
EXPOSE 5000
CMD ["flask", "run"]
```

---

## 🧾 docker-compose.yml

```yaml
version: "3"
services:
  web:
    build: .
    ports:
      - "8000:5000"
    volumes:
      - .:/code
    environment:
      - FLASK_ENV=development

  redis:
    image: "redis:alpine"
```

---

## 🚀 Run the App

```bash
docker-compose up
```

* **Foreground mode**: Shows logs and real-time activity.
* **Ctrl + C** to stop.

### To run in the background (detached mode):

```bash
docker-compose up -d
```

---

## 🛠 Useful Commands

| Command                | Description                              |
| ---------------------- | ---------------------------------------- |
| `docker-compose up`    | Start all services                       |
| `docker-compose up -d` | Run in detached mode                     |
| `docker-compose down`  | Stop and remove all containers, networks |
| `docker-compose ps`    | List running containers                  |
| `docker-compose top`   | Show running processes                   |
| `docker-compose logs`  | View logs from services                  |
| `docker-compose build` | Build/rebuild services                   |
| `docker-compose stop`  | Stop services without removing them      |

---

## 🔁 Hot Reload

* Modify `app.py`
* If running in bind mount mode (`.:/code`), changes reflect instantly in container

---

## 🧹 Cleanup

```bash
docker-compose down
docker image ls     # check images
docker volume ls    # if you’ve created volumes
```

---

## 🧠 Key Concepts

| Concept      | Description                                                          |
| ------------ | -------------------------------------------------------------------- |
| **Service**  | A container managed by Compose                                       |
| **Build**    | Builds an image using Dockerfile                                     |
| **Image**    | Pre-built container blueprint                                        |
| **Volumes**  | Persist or mount host directories                                    |
| **Ports**    | Maps container ports to host machine                                 |
| **Networks** | Compose creates a default bridge network for services to communicate |

---

## 📦 Next Step: Containerizing a Real Project

Now that you understand Docker Compose, you're ready to:

* Write Dockerfiles for your actual applications
* Define how they connect in `docker-compose.yml`
* Run them together with `docker-compose up`

---

## ✅ TL;DR

* Compose simplifies multi-container apps
* Uses `docker-compose.yml` to define infrastructure
* Run everything with a single command
* Makes your containers reproducible and easy to manage

---

# 🐳 Multistage Dockerfile – Optimizing Docker Images

## 🎯 What Is a Multistage Dockerfile?

A **Multistage Dockerfile** allows you to use **multiple `FROM` statements** in a single Dockerfile to **separate the build environment** from the final image.
This results in:

* Smaller image size
* Cleaner, production-ready containers
* Fully automated build process (no manual artifact creation)

---

## 🤔 Why Do We Need It?

Let’s consider a **Java/Maven app**:

* You use Maven to generate a `.war` or `.jar` artifact
* If you **build the artifact inside the same image**, it bloats the image with:

    * Source code
    * Build tools (Maven, JDK, etc.)
    * Dependencies
* If you **build the artifact outside**, it requires manual steps
  (not CI/CD friendly)

### 🔥 Multistage builds solve both problems:

* You can **build the artifact inside a temporary build stage**
* Only copy the final output (`.war`, `.jar`) to the final image

---

## 🛠️ Step-by-Step Example

### ✅ Step 1: Clone the Repo

```bash
git clone -b vp-rem https://github.com/your/repo.git
cd repo/docker-files/app
```

---

## 📄 The Traditional (Single-Stage) Dockerfile Problem

```Dockerfile
FROM tomcat:8-jre11
COPY ./target/app.war /usr/local/tomcat/webapps/
```

⚠️ Requires you to manually build the artifact first using:

```bash
mvn install
```

### 🚫 Not Ideal:

* Manual steps
* Not automated
* No reproducibility
* Large image if build is included in same image

---

## ✅ The Multistage Dockerfile

```Dockerfile
### --- Stage 1: Build the Artifact ---
FROM openjdk:8-jdk AS build_image

RUN apt-get update && \
    apt-get install -y maven git && \
    git clone -b vp-rem https://github.com/your/repo.git && \
    cd repo && \
    mvn install

### --- Stage 2: Create Runtime Image ---
FROM tomcat:8-jre11

RUN rm -rf /usr/local/tomcat/webapps/*

COPY --from=build_image /repo/target/app.war /usr/local/tomcat/webapps/

EXPOSE 8080
CMD ["catalina.sh", "run"]
```

---

## ⚙️ Build the Image

```bash
docker build -t app-image:v1 .
```

* Stage 1 downloads dependencies and builds artifact
* Stage 2 is lightweight and contains only the `.war` file and Tomcat

---

## 📦 Check Image Sizes

```bash
docker images
```

| Image                               | Size     |
| ----------------------------------- | -------- |
| `app-image:v1`                      | \~374 MB |
| `<none>` (intermediate build image) | \~1+ GB  |

✅ Production image is significantly smaller
✅ Clean, efficient, and automated

---

## 💡 Key Benefits

| Feature            | Benefit                                                      |
| ------------------ | ------------------------------------------------------------ |
| **Automation**     | No manual artifact creation                                  |
| **Smaller Size**   | No dev tools, source code, or build artifacts in final image |
| **Best Practice**  | Recommended for production environments                      |
| **CI/CD Friendly** | Easily used in automated pipelines                           |

---

## 📌 When to Use Multistage Dockerfiles

* Any project that needs to **compile, transpile, or bundle** code before running (e.g., Java, Go, Node.js, Angular)
* Whenever you want to **separate build from runtime**
* When working in **production-grade projects** where image size and security matter

---

## 🧠 Summary

* Use multistage Dockerfiles to build once, copy only what you need
* Drastically reduces image size and improves clarity
* The first `FROM` stage builds the artifact
* The second (final) `FROM` contains only the minimal runtime environment

---

## 🧪 Homework / Practice

* ✅ Create a multistage Dockerfile in your own project
* ✅ Compare image sizes with and without multistage
* ✅ Automate artifact generation and container run with `docker-compose`

---


