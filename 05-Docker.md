# 🐳 Complete Docker Notes – Beginner to Advanced

> Complete Docker learning guide for Linux, DevOps, AWS, and real-time projects.

---

# 📚 Table of Contents

1. Introduction to Docker
2. Docker Architecture
3. Docker Installation and Verification
4. Docker Basic Commands
5. Docker Images
6. Docker Containers
7. Docker Run Commands
8. Container Start, Stop, Restart, Attach and Exec
9. Docker Logs and Troubleshooting
10. Dockerfile
11. Docker Image Build, Tag and Push
12. Docker Networking
13. Docker Volumes and Storage
14. Docker Compose
15. Docker Registry and Docker Hub
16. Docker System Management and Cleanup
17. Docker Resource Management
18. Docker Security Basics
19. Docker Troubleshooting – Real-Time Commands
20. Complete Docker Hands-on Project
21. Docker Daily-Use Commands Cheat Sheet
22. Docker Interview Questions and Answers

---

# 1. Introduction to Docker

## What is Docker?

Docker is an open-source platform used to build, package, ship, and run applications inside containers.

Docker helps developers run applications in a consistent environment.

### Simple Example

Suppose an application works on your laptop but fails on another server because of different software versions.

Docker solves this problem by packaging the application and its dependencies into a container image.

### Why Docker?

- Run applications consistently.
- Reduce environment-related issues.
- Deploy applications faster.
- Run multiple isolated applications.
- Make application deployment easier.
- Improve DevOps automation.
- Support microservices architecture.

## What is a Container?

A container is a running instance of a Docker image.

## What is an Image?

An image is a template used to create containers.

### Image vs Container

| Docker Image | Docker Container |
|---|---|
| Template | Running instance |
| Read-only layers | Writable container layer |
| Used to create containers | Runs applications |
| Can create multiple containers | Created from an image |
| Example: nginx | Example: myweb |

---

# 2. Docker Architecture

Docker uses a client-server architecture.

### Main Components

1. Docker Client
2. Docker Daemon
3. Docker Images
4. Docker Containers
5. Docker Registry

### Architecture Flow

```text
             USER
               |
               |
         Docker Client
               |
          Docker CLI
               |
               v
         Docker Daemon
           (dockerd)
               |
       -----------------
       |       |       |
       v       v       v
     Images  Containers Networks
       |
       v
   Docker Registry
    (Docker Hub)
```

### Explanation

**Docker Client:** Accepts Docker commands.

**Docker Daemon:** Manages Docker objects such as containers, images, networks and volumes.

**Docker Image:** Application template.

**Docker Container:** Running application environment.

**Docker Registry:** Stores and distributes Docker images.

---

# 3. Docker Installation and Verification

## Ubuntu Installation

```bash
sudo apt update

sudo apt install docker.io -y

sudo systemctl start docker

sudo systemctl enable docker

sudo systemctl status docker
```

Verify installation:

```bash
docker --version

docker version

docker info

docker run hello-world
```

Check Docker service:

```bash
sudo systemctl status docker

sudo systemctl start docker

sudo systemctl stop docker

sudo systemctl restart docker

sudo systemctl enable docker

sudo systemctl disable docker
```

### Important

Docker Engine and Docker Compose plugin installation methods can differ depending on the Linux distribution and repository. Verify the installed components before using them.

---

# 4. Docker Basic Commands

| Command | Explanation |
|---|---|
| `docker --version` | Check Docker version |
| `docker version` | Client and server versions |
| `docker info` | Docker system information |
| `docker help` | Docker help |
| `docker ps` | Running containers |
| `docker images` | List images |
| `docker pull nginx` | Download image |
| `docker run nginx` | Create and start container |
| `docker start ID` | Start container |
| `docker stop ID` | Stop container |
| `docker restart ID` | Restart container |
| `docker rm ID` | Remove container |
| `docker rmi IMAGE` | Remove image |
| `docker logs ID` | Check logs |
| `docker inspect ID` | Detailed information |
| `docker stats` | CPU and memory usage |

---

# 5. Docker Images

Docker images are templates used to create containers.

## Image Commands

```bash
# List all images
docker images

# Alternative command
docker image ls

# Download Nginx image
docker pull nginx

# Download Ubuntu image
docker pull ubuntu

# Download specific version
docker pull nginx:latest

# Inspect image
docker image inspect nginx

# Show image history
docker image history nginx

# Remove image
docker rmi nginx

# Alternative remove command
docker image rm nginx

# Remove unused images
docker image prune

# Remove all unused images
docker image prune -a
```

## Save and Load Images

```bash
# Save image into TAR file
docker save -o nginx.tar nginx

# Load image from TAR file
docker load -i nginx.tar
```

## Image Tagging

```bash
docker tag nginx mynginx:v1

docker images
```

## Image Search

```bash
docker search nginx

docker search ubuntu
```

---

# 6. Docker Containers

Containers are created from Docker images.

## Container Listing Commands

```bash
# Running containers
docker ps

# All containers
docker ps -a

# Only container IDs
docker ps -q

# All container IDs
docker ps -aq

# Recently created container
docker ps -l

# Last 5 containers
docker ps -n 5

# Container IDs and names
docker ps --format "{{.ID}} {{.Names}}"
```

## Create Containers

```bash
# Create container without starting
docker create nginx

# Create and start container
docker run nginx

# Run in background
docker run -d nginx

# Run with container name
docker run -d --name myweb nginx

# Run interactive Ubuntu
docker run -it ubuntu bash

# Run container with automatic removal
docker run --rm ubuntu echo "Hello Docker"
```

## Container Management

```bash
# Start container
docker start myweb

# Stop container
docker stop myweb

# Restart container
docker restart myweb

# Pause container
docker pause myweb

# Resume container
docker unpause myweb

# Force stop container
docker kill myweb

# Remove container
docker rm myweb

# Force remove container
docker rm -f myweb

# Rename container
docker rename myweb webserver

# Inspect container
docker inspect myweb

# Display container processes
docker top myweb

# Show container port mappings
docker port myweb

# Wait for container to exit
docker wait myweb

# Display filesystem changes
docker diff myweb
```

## Difference Between Container Commands

| Command | Meaning |
|---|---|
| `docker run` | Create and start |
| `docker create` | Create only |
| `docker start` | Start existing container |
| `docker stop` | Gracefully stop |
| `docker kill` | Forcefully stop |
| `docker restart` | Restart |
| `docker pause` | Pause processes |
| `docker unpause` | Resume processes |
| `docker rm` | Remove container |

---

# 7. Docker Run Commands

Docker run is one of the most important commands.

## Basic Run

```bash
docker run nginx
```

## Run in Background

```bash
docker run -d nginx
```

## Run with Name

```bash
docker run -d --name myweb nginx
```

## Run with Port Mapping

```bash
docker run -d -p 8080:80 --name myweb nginx
```

Explanation:

- 8080 = Host port
- 80 = Container port

Access:

```text
http://EC2-PUBLIC-IP:8080
```

Remember to allow TCP port 8080 in the EC2 security group if accessing from outside.

## Run with Interactive Terminal

```bash
docker run -it ubuntu bash
```

## Run with Environment Variables

```bash
docker run -d \
--name myapp \
-e APP_ENV=production \
nginx
```

## Run with Restart Policy

```bash
docker run -d \
--name myweb \
--restart unless-stopped \
nginx
```

Restart policies:

- no
- always
- on-failure
- unless-stopped

## Run with Volume

```bash
docker run -d \
--name mydata \
-v appdata:/usr/share/nginx/html \
nginx
```

## Run with Network

```bash
docker run -d \
--name myweb \
--network bridge \
nginx
```

---

# 8. Docker Start, Stop, Attach and Exec

These commands are very important for real-time troubleshooting.

## Start Container

```bash
docker start myweb
```

## Stop Container

```bash
docker stop myweb
```

## Restart Container

```bash
docker restart myweb
```

## Attach to Container

```bash
docker attach myweb
```

Attach connects your terminal to the container's main process.

To detach without stopping a normally attached container, use:

```text
Ctrl + P
Ctrl + Q
```

## Execute Commands Inside Container

```bash
docker exec myweb hostname

docker exec myweb whoami

docker exec myweb pwd

docker exec myweb ls
```

## Enter Container Shell

```bash
docker exec -it myweb bash
```

If Bash is unavailable:

```bash
docker exec -it myweb sh
```

## Enter as Root

```bash
docker exec -u root -it myweb bash
```

## Run Commands Inside Container

```bash
docker exec myweb cat /etc/os-release

docker exec myweb df -h

docker exec myweb free -m

docker exec myweb ps aux
```

## Attach vs Exec

| Attach | Exec |
|---|---|
| Connects to main process | Starts a new process |
| Shares main process input/output | Opens independent command |
| Can affect main process | Does not replace main process |
| `docker attach myweb` | `docker exec -it myweb bash` |

---

# 9. Docker Logs and Troubleshooting

## Logs Commands

```bash
# Display logs
docker logs myweb

# Follow live logs
docker logs -f myweb

# Display last 50 lines
docker logs --tail 50 myweb

# Logs from last 10 minutes
docker logs --since 10m myweb

# Show timestamps
docker logs -t myweb

# Follow last 100 lines
docker logs -f --tail 100 myweb
```

## Inspect Commands

```bash
docker inspect myweb

docker inspect --format '{{.State.Status}}' myweb

docker inspect --format '{{.NetworkSettings.IPAddress}}' myweb

docker inspect --format '{{.Config.Image}}' myweb
```

## Resource Monitoring

```bash
# All running container resource usage
docker stats

# Particular container
docker stats myweb

# Display once instead of continuously
docker stats --no-stream
```

## Process Monitoring

```bash
docker top myweb

docker ps

docker events
```

## Copy Files

```bash
# Host to container
docker cp index.html myweb:/usr/share/nginx/html/

# Container to host
docker cp myweb:/etc/hosts ./hosts
```

---

# 10. Dockerfile

A Dockerfile is a text file containing instructions to build a Docker image.

## Important Dockerfile Instructions

| Instruction | Explanation |
|---|---|
| `FROM` | Base image |
| `RUN` | Execute build-time commands |
| `CMD` | Default command |
| `ENTRYPOINT` | Main executable |
| `COPY` | Copy files |
| `ADD` | Copy files and support additional features |
| `WORKDIR` | Set working directory |
| `ENV` | Set environment variables |
| `EXPOSE` | Document container port |
| `USER` | Set runtime user |
| `ARG` | Build-time variable |
| `VOLUME` | Declare mount point |
| `LABEL` | Add image metadata |
| `HEALTHCHECK` | Configure health check |
| `SHELL` | Change shell used by shell-form instructions |
| `STOPSIGNAL` | Set stop signal |

## Example Dockerfile

```dockerfile
FROM nginx:latest

LABEL maintainer="DevOps Team"

WORKDIR /usr/share/nginx/html

COPY index.html .

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

## Build Image

```bash
docker build -t mywebsite:v1 .
```

## List Image

```bash
docker images
```

## Run Image

```bash
docker run -d -p 8080:80 --name website mywebsite:v1
```

## Check Container

```bash
docker ps
```

## CMD vs ENTRYPOINT

| CMD | ENTRYPOINT |
|---|---|
| Default command or arguments | Main executable |
| Can be overridden easily | Usually remains the executable |
| Provides default behavior | Defines the main execution |

---

# 11. Docker Image Build, Tag and Push

## Build Image

```bash
docker build -t myapp:v1 .
```

## Build Without Cache

```bash
docker build --no-cache -t myapp:v1 .
```

## Build Using Specific Dockerfile

```bash
docker build -f Dockerfile -t myapp:v1 .
```

## Login to Docker Hub

```bash
docker login
```

## Tag Image

```bash
docker tag myapp:v1 username/myapp:v1
```

## Push Image

```bash
docker push username/myapp:v1
```

## Pull Image

```bash
docker pull username/myapp:v1
```

## Logout

```bash
docker logout
```

---

# 12. Docker Networking

Docker networking enables communication between containers and external systems.

## Network Types

| Network | Explanation |
|---|---|
| Bridge | Default standalone container network |
| Host | Shares host network namespace |
| None | No container networking |
| Overlay | Connects containers across Swarm nodes |
| Macvlan | Assigns MAC addresses to containers |

## Network Commands

```bash
# List networks
docker network ls

# Create network
docker network create mynetwork

# Inspect network
docker network inspect mynetwork

# Connect container
docker network connect mynetwork myweb

# Disconnect container
docker network disconnect mynetwork myweb

# Remove network
docker network rm mynetwork

# Remove unused networks
docker network prune
```

## Create Bridge Network

```bash
docker network create dev-network
```

## Run Container on Network

```bash
docker run -dit \
--name app1 \
--network dev-network \
ubuntu
```

## Run Second Container

```bash
docker run -dit \
--name app2 \
--network dev-network \
ubuntu
```

## Test Communication

```bash
docker exec app1 ping -c 4 app2
```

Note: The Ubuntu image may need the `iputils-ping` package installed to use ping.

---

# 13. Docker Volumes and Storage

Volumes are used to persist data outside the container's writable layer.

If a container is removed, its data in a separately managed volume can remain.

## Volume Commands

```bash
# List volumes
docker volume ls

# Create volume
docker volume create mydata

# Inspect volume
docker volume inspect mydata

# Remove volume
docker volume rm mydata

# Remove unused volumes
docker volume prune
```

## Create Container with Volume

```bash
docker run -d \
--name nginx-data \
-v mydata:/usr/share/nginx/html \
nginx
```

## Bind Mount

```bash
docker run -d \
--name web \
-v /home/ubuntu/html:/usr/share/nginx/html \
nginx
```

## Difference Between Volume and Bind Mount

| Volume | Bind Mount |
|---|---|
| Managed by Docker | Uses host filesystem path |
| Easier to manage | Direct host directory access |
| Good for persistent application data | Good for development and file sharing |

---

# 14. Docker Compose

Docker Compose is used to define and manage multiple containers using a YAML file.

## Important Commands

```bash
# Check version
docker compose version

# Start services
docker compose up

# Start in background
docker compose up -d

# Stop services
docker compose stop

# Start existing services
docker compose start

# Restart services
docker compose restart

# Stop and remove Compose resources
docker compose down

# List services
docker compose ps

# Display logs
docker compose logs

# Follow logs
docker compose logs -f

# Pull service images
docker compose pull

# Build images
docker compose build

# Execute command
docker compose exec web bash

# Run one-off command
docker compose run web bash

# Validate configuration
docker compose config

# List service images
docker compose images

# Show service processes
docker compose top
```

## Example docker-compose.yml

```yaml
services:
  web:
    image: nginx:latest
    container_name: nginx-web
    ports:
      - "8080:80"
    restart: unless-stopped

  ubuntu:
    image: ubuntu:latest
    container_name: ubuntu-test
    command: sleep infinity
    restart: unless-stopped
```

Start:

```bash
docker compose up -d
```

Check:

```bash
docker compose ps
```

Logs:

```bash
docker compose logs -f
```

Stop:

```bash
docker compose down
```

---

# 15. Docker Registry and Docker Hub

Docker Registry stores Docker images.

Docker Hub is a public image registry.

## Commands

```bash
docker login

docker logout

docker search nginx

docker pull nginx

docker images

docker tag nginx username/nginx:v1

docker push username/nginx:v1
```

## Complete Push and Pull Flow

```text
Build Image
    |
    v
Tag Image
    |
    v
Docker Login
    |
    v
Docker Push
    |
    v
Docker Hub
    |
    v
Docker Pull
    |
    v
Run Container
```

---

# 16. Docker System Management and Cleanup

## System Commands

```bash
# Docker information
docker system info

# Disk usage
docker system df

# Detailed disk usage
docker system df -v

# Docker events
docker system events

# Remove stopped containers
docker container prune

# Remove unused images
docker image prune

# Remove unused networks
docker network prune

# Remove unused volumes
docker volume prune

# Remove unused resources
docker system prune

# Remove all unused images and resources
docker system prune -a
```

## Important Warning

```bash
docker system prune -a --volumes
```

This is a destructive cleanup command. It can remove unused images, containers, networks and volumes. Never execute it blindly on a production server.

---

# 17. Docker Resource Management

Docker supports CPU and memory limits.

## Memory Limit

```bash
docker run -d \
--name limited-app \
--memory=512m \
nginx
```

## CPU Limit

```bash
docker run -d \
--name cpu-app \
--cpus=1 \
nginx
```

## CPU and Memory Together

```bash
docker run -d \
--name resource-app \
--memory=512m \
--cpus=1 \
nginx
```

## Monitor Resources

```bash
docker stats

docker stats --no-stream

docker inspect resource-app
```

---

# 18. Docker Security Basics

Important security practices:

1. Avoid running containers as root unnecessarily.
2. Use trusted images.
3. Keep images updated.
4. Avoid hardcoding passwords in Dockerfiles.
5. Use environment variables or secrets management appropriately.
6. Limit container CPU and memory.
7. Use read-only filesystems where possible.
8. Avoid exposing unnecessary ports.
9. Do not mount sensitive host directories without a reason.
10. Scan images for vulnerabilities.

## Run Container as Non-Root User

```bash
docker run --user 1000:1000 nginx
```

Note: The application must have the permissions it needs to operate as that user.

---

# 19. Docker Troubleshooting – Real-Time Commands

| Problem | Command |
|---|---|
| Container not running | `docker ps -a` |
| Check container logs | `docker logs CONTAINER` |
| Inspect container | `docker inspect CONTAINER` |
| Check CPU/memory | `docker stats` |
| Check processes | `docker top CONTAINER` |
| Check image | `docker images` |
| Check networks | `docker network ls` |
| Check volumes | `docker volume ls` |
| Check port mapping | `docker port CONTAINER` |
| Check Docker service | `systemctl status docker` |
| Restart Docker service | `sudo systemctl restart docker` |
| Check Docker disk usage | `docker system df` |
| Check Docker daemon logs | `journalctl -u docker -n 100` |
| Follow Docker daemon logs | `journalctl -u docker -f` |

## Common Errors

### Error 1: Cannot Connect to Docker Daemon

Check:

```bash
sudo systemctl status docker

sudo systemctl start docker
```

### Error 2: Container Name Already in Use

Check:

```bash
docker ps -a
```

Remove old container if safe:

```bash
docker rm old-container
```

### Error 3: Port Already Allocated

Check:

```bash
sudo ss -tulpn
```

Use another port:

```bash
docker run -d -p 8081:80 nginx
```

### Error 4: Image Not Found

Check:

```bash
docker images
```

Pull image:

```bash
docker pull nginx
```

### Error 5: Container Exits Immediately

Check:

```bash
docker ps -a

docker logs CONTAINER

docker inspect CONTAINER
```

Remember: A container generally stops when its main process exits.

---

# 20. Complete Docker Hands-on Project

## Project: Deploy Nginx Web Server Using Docker on AWS EC2

### Step 1: Check Docker

```bash
docker --version

sudo systemctl status docker
```

### Step 2: Pull Nginx Image

```bash
docker pull nginx
```

### Step 3: Create Container

```bash
docker run -d \
--name myweb \
-p 8080:80 \
nginx
```

### Step 4: Verify Container

```bash
docker ps

docker ps -a
```

### Step 5: Access Website

```text
http://EC2-PUBLIC-IP:8080
```

### Step 6: Enter Container

```bash
docker exec -it myweb bash
```

Inside the container:

```bash
hostname

whoami

pwd

ls

cat /etc/os-release

exit
```

### Step 7: Check Logs

```bash
docker logs myweb
```

### Step 8: Stop Container

```bash
docker stop myweb
```

### Step 9: Start Container

```bash
docker start myweb
```

### Step 10: Restart Container

```bash
docker restart myweb
```

### Step 11: Remove Container

```bash
docker stop myweb

docker rm myweb
```

---

# 21. Docker Daily-Use Commands Cheat Sheet

| No. | Command | Purpose |
|---:|---|---|
| 1 | `docker --version` | Version |
| 2 | `docker info` | System information |
| 3 | `docker images` | List images |
| 4 | `docker pull nginx` | Pull image |
| 5 | `docker ps` | Running containers |
| 6 | `docker ps -a` | All containers |
| 7 | `docker run -d nginx` | Run container |
| 8 | `docker create nginx` | Create container |
| 9 | `docker start ID` | Start |
| 10 | `docker stop ID` | Stop |
| 11 | `docker restart ID` | Restart |
| 12 | `docker kill ID` | Force stop |
| 13 | `docker pause ID` | Pause |
| 14 | `docker unpause ID` | Resume |
| 15 | `docker attach ID` | Attach |
| 16 | `docker exec -it ID bash` | Enter container |
| 17 | `docker logs ID` | Logs |
| 18 | `docker inspect ID` | Inspect |
| 19 | `docker stats` | Resource monitoring |
| 20 | `docker top ID` | Processes |
| 21 | `docker port ID` | Port mapping |
| 22 | `docker cp` | Copy files |
| 23 | `docker rm ID` | Remove container |
| 24 | `docker rmi IMAGE` | Remove image |
| 25 | `docker build -t app .` | Build image |
| 26 | `docker tag IMAGE TAG` | Tag image |
| 27 | `docker push IMAGE` | Push image |
| 28 | `docker network ls` | List networks |
| 29 | `docker volume ls` | List volumes |
| 30 | `docker compose up -d` | Start Compose |
| 31 | `docker compose down` | Stop Compose |
| 32 | `docker system df` | Disk usage |
| 33 | `docker system prune` | Cleanup |

---

# 22. Docker Interview Questions and Answers

### Q1. What is Docker?

Docker is a containerization platform used to package and run applications in isolated containers.

### Q2. What is a Docker image?

An image is a template used to create containers.

### Q3. What is a Docker container?

A container is a running or stopped instance of an image.

### Q4. Difference between Docker image and container?

An image is the template, whereas a container is an instance created from that image.

### Q5. Difference between docker run and docker start?

`docker run` creates and starts a new container. `docker start` starts an existing stopped container.

### Q6. Difference between docker stop and docker kill?

`docker stop` attempts a graceful shutdown. `docker kill` sends a termination signal immediately by default.

### Q7. Difference between docker exec and docker attach?

`exec` starts a new process inside a container. `attach` connects to the existing main process.

### Q8. Difference between docker ps and docker ps -a?

`docker ps` displays running containers. `docker ps -a` displays all containers.

### Q9. What is Docker Hub?

Docker Hub is an image registry used to store and distribute Docker images.

### Q10. What is a Dockerfile?

A Dockerfile contains instructions for building Docker images.

### Q11. What is Docker Compose?

Docker Compose is used to define and manage multi-container applications.

### Q12. What is Docker networking?

Docker networking enables communication between containers and other systems.

### Q13. What is a Docker volume?

A Docker volume is used to persist application data outside the container's writable layer.

### Q14. How do you troubleshoot a container?

Use:

```bash
docker ps -a

docker logs CONTAINER

docker inspect CONTAINER

docker stats
```

### Q15. How do you check container CPU and memory?

```bash
docker stats
```

### Q16. How do you copy files between host and container?

```bash
docker cp file.txt container:/tmp/
```

### Q17. How do you remove unused containers?

```bash
docker container prune
```

### Q18. What is the difference between CMD and ENTRYPOINT?

CMD specifies default commands or arguments. ENTRYPOINT specifies the main executable.

### Q19. What happens when a container is stopped?

Its main process stops, but the container remains available to restart unless it is removed.

### Q20. Can multiple containers use the same image?

Yes. Multiple containers can be created from the same image.

### Q21. What is a Docker daemon?

The Docker daemon manages Docker objects and processes API requests.

### Q22. What is port mapping?

Port mapping connects a host port to a container port.

Example:

```bash
docker run -d -p 8080:80 nginx
```

### Q23. What is the difference between volume and bind mount?

A volume is managed by Docker, while a bind mount maps a specific host filesystem path.

### Q24. What is a Docker restart policy?

It defines whether Docker should restart a container after it exits or the Docker daemon restarts.

### Q25. What is a multi-stage Docker build?

A multi-stage build uses multiple `FROM` stages to separate build dependencies from the final runtime image, helping create smaller production images.

---

# 23. Docker Learning Roadmap

Follow this order:

| Stage | Topic | Priority |
|---|---|---|
| 1 | Docker Basics | Very High |
| 2 | Docker Images | Very High |
| 3 | Docker Containers | Very High |
| 4 | Run, Start, Stop, Exec, Attach | Very High |
| 5 | Dockerfile | Very High |
| 6 | Docker Networking | High |
| 7 | Docker Volumes | High |
| 8 | Docker Compose | High |
| 9 | Docker Hub | High |
| 10 | Docker Troubleshooting | Very High |
| 11 | Docker Security | Medium |
| 12 | Docker with AWS EC2 | Very High |
| 13 | Docker with Kubernetes | Advanced |
| 14 | Docker in CI/CD Pipeline | Advanced |

---

# Final Notes

Docker is one of the most important tools in DevOps.

Understand these five concepts very clearly:

1. Images – Application templates.
2. Containers – Running application instances.
3. Dockerfile – Instructions to build images.
4. Volumes – Persistent data storage.
5. Networks – Communication between containers.

**Recommended DevOps Learning Path:**

Linux → Networking → Git/GitHub → Docker → Kubernetes → Terraform → AWS → Jenkins/CI/CD.

**Practice every command on your Ubuntu EC2 server. Understanding commands through hands-on practice is more important than memorizing them.**

