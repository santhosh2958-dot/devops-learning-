# 🐳 Docker.md – Complete Docker Commands & Practical Guide
## Beginner to Advanced | Linux | AWS EC2 | DevOps

> All 23 requested Docker topics and the all-in-one command reference in a single Markdown file.

![Docker](https://www.docker.com/wp-content/uploads/2022/03/Moby-logo.png)

---

## Table of Contents

1. Docker fundamentals
2. Understand the sample `docker run` command
3. Docker command structure and options
4. Installation and service management
5. Images and registries
6. Containers: create, list, start, stop, restart, remove
7. `docker run` options – detailed reference
8. Entering containers: `exec`, `attach`, `cp`
9. Logs, inspect, processes and troubleshooting
10. Dockerfile and image build
11. Ports and networking
12. Volumes and bind mounts
13. Environment variables, resource limits and restart policies
14. Docker Compose
15. Docker system, disk usage and cleanup
16. Docker security
17. Practical exercises
18. Daily-use command cheat sheet
19. Interview questions and answers
20. Important command differences

---

# 1. Docker fundamentals

- **Docker image:** A packaged, read-only template containing application files and dependencies.
- **Docker container:** An instance created from an image. It has a writable container layer while it exists.
- **Dockerfile:** Text instructions used to build an image.
- **Volume:** Docker-managed persistent storage.
- **Bind mount:** Maps a specific host directory or file into a container.
- **Port mapping:** Connects a host port to a container port.
- **Registry:** A service that stores and distributes images, such as Docker Hub.
- **Docker daemon:** Background service (`dockerd`) that manages Docker objects.

### Simple architecture

```text
Docker CLI (docker command)
          |
          v
Docker Engine API
          |
          v
Docker daemon (dockerd)
    |       |       |       |
  Images Containers Networks Volumes
          |
          v
     Image Registry
       (Docker Hub)
```

### Image vs container

| Image | Container |
|---|---|
| Template used to create containers | Instance created from an image |
| Read-only image layers | Adds a writable container layer |
| Can be used by many containers | Has its own lifecycle and configuration |
| `docker images` | `docker ps -a` |

---

# 2. Understand the sample command in the terminal screenshot

The command shown is:

```bash
docker run -it -d -v v1:/data --name c5 -p 9012:80 08282c28ff42
```

The image ID `08282c28ff42` appears to be the image selected from the image listing. Docker accepts an image name or image ID.

### What every part means

| Part | Meaning |
|---|---|
| `docker` | Docker command-line client |
| `run` | Create a new container and start it |
| `-i` | Keep standard input open |
| `-t` | Allocate a pseudo-terminal (TTY) |
| `-d` | Run in detached/background mode |
| `-v v1:/data` | Mount Docker volume `v1` inside the container at `/data` |
| `--name c5` | Give the container the name `c5` |
| `-p 9012:80` | Map host TCP port `9012` to container TCP port `80` |
| `08282c28ff42` | Image ID used to create the container |

### Important: use spaces between options

Correct command:

```bash
docker run -it -d -v v1:/data --name c5 -p 9012:80 08282c28ff42
```

If the volume does not already exist, Docker normally creates a named volume when using `-v v1:/data`.

### Recommended variations

For a background web server, `-d` is usually enough:

```bash
docker run -d \
  --name c5 \
  --mount source=v1,target=/data \
  -p 9012:80 \
  08282c28ff42
```

For an interactive terminal, use `-it` without `-d`:

```bash
docker run -it --name c5 08282c28ff42 bash
```

The last example assumes the image contains Bash. If not, try `sh`.

### Verify the container

```bash
docker ps
docker ps -a
docker inspect c5
docker port c5
docker logs c5
docker volume ls
docker volume inspect v1
```

### Open a shell in the running container

```bash
docker exec -it c5 bash
```

If Bash is not installed:

```bash
docker exec -it c5 sh
```

### Test the web port

Open this from a browser if the application inside the container listens on port 80:

```text
http://EC2-PUBLIC-IP:9012
```

For an EC2 instance, also allow inbound TCP `9012` in its security group. Make sure the application actually listens on container port `80`.

---

# 3. Docker command structure and options

General structure:

```bash
docker [GLOBAL OPTIONS] COMMAND [SUBCOMMAND] [OPTIONS] [ARGUMENTS]
```

Examples:

```bash
docker --version
docker info
docker image ls
docker container ls -a
docker volume ls
docker network ls
```

### Useful global options

| Option | Meaning |
|---|---|
| `-H` / `--host` | Connect to a specified Docker daemon endpoint |
| `-c` / `--context` | Use a Docker context |
| `--config` | Use a specified Docker client configuration directory |
| `--debug` | Enable debug output |
| `--help` | Show help |
| `-v` / `--version` | Show version |

Get help for any command:

```bash
docker --help
docker run --help
docker volume --help
docker network --help
docker compose --help
```

---

# 4. Installation and service management

## Ubuntu / Debian

The distribution package is commonly named `docker.io`. For the latest Docker Engine features, follow Docker's official installation instructions for the distribution.

```bash
sudo apt update
sudo apt install docker.io -y
```

## RHEL / CentOS / Fedora

Package names and installation steps vary by distribution and version. On supported systems, Docker Engine packages are commonly installed from Docker's official repository. Do not assume that a package named simply `docker` is available.

## Check installation

```bash
docker --version
docker version
docker info
which docker
```

## Manage the Docker service with systemd

```bash
sudo systemctl status docker
sudo systemctl start docker
sudo systemctl stop docker
sudo systemctl restart docker
sudo systemctl enable docker
sudo systemctl disable docker
sudo systemctl is-active docker
sudo systemctl is-enabled docker
```

## Test Docker

```bash
sudo docker run hello-world
```

## Run Docker without typing sudo (Linux)

```bash
sudo usermod -aG docker "$USER"
```

Log out and log back in for the group change to take effect. Membership in the `docker` group grants root-level privileges on the host; only add trusted users.

---

# 5. Docker images and registries

## List and inspect images

```bash
docker images
docker image ls
docker images -a
docker image ls -q
docker image inspect nginx
docker image history nginx
docker image ls --digests
```

## Download images

```bash
docker pull nginx
docker pull nginx:latest
docker pull ubuntu
docker pull ubuntu:24.04
docker pull redis:7
```

`latest` is a tag, not a guarantee that the image is the newest or best choice for production. Prefer a deliberate version tag or digest for repeatable deployments.

## Search Docker Hub

```bash
docker search nginx
docker search ubuntu
```

## Tag images

```bash
docker tag nginx mynginx:v1
docker tag myapp:v1 YOUR_DOCKERHUB_USERNAME/myapp:v1
```

## Push an image

```bash
docker login
docker push YOUR_DOCKERHUB_USERNAME/myapp:v1
docker logout
```

Do not put passwords or access tokens directly in shell commands or Dockerfiles. Use a secure credential method.

## Save and load images

```bash
# Save an image to a TAR archive
docker save -o nginx-image.tar nginx:latest

# Load the archive on another Docker host
docker load -i nginx-image.tar
```

## Export and import a container filesystem

```bash
# Export a container filesystem (not the same as saving an image)
docker export -o container-filesystem.tar CONTAINER_NAME

# Import a filesystem archive as an image
docker import container-filesystem.tar imported-image:v1
```

`save/load` preserve image layers and tags. `export/import` work with a container filesystem and do not preserve the full image metadata/history.

## Remove images

```bash
docker rmi IMAGE
docker image rm IMAGE
docker rmi IMAGE_ID
docker image prune
docker image prune -a
```

An image cannot normally be removed while a container still uses it unless forced. Check containers before removing images.

---

# 6. Docker containers – all essential lifecycle commands

## List containers

```bash
docker ps                 # Running containers
docker ps -a              # All containers
docker ps -q              # IDs of running containers
docker ps -aq             # IDs of all containers
docker ps -l              # Latest created container
docker ps -n 5            # Last five created containers
docker container ls
docker container ls -a
docker container ls -q
```

## Create a container without starting it

```bash
docker create --name web1 nginx
```

## Create and start a container

```bash
docker run --name web1 nginx
```

## Run in background

```bash
docker run -d --name web1 nginx
```

## Start, stop and restart

```bash
docker start web1
docker stop web1
docker restart web1
```

## Pause and resume

```bash
docker pause web1
docker unpause web1
```

## Force stop

```bash
docker kill web1
```

## Remove containers

```bash
docker rm web1
docker rm -f web1
docker container prune
```

`docker rm` normally requires a stopped container. `docker rm -f` forcefully stops and removes it.

## Rename and inspect

```bash
docker rename oldname newname
docker inspect web1
docker inspect --format '{{.State.Status}}' web1
docker inspect --format '{{.Config.Image}}' web1
docker inspect --format '{{.NetworkSettings.IPAddress}}' web1
```

## Other container commands

```bash
docker top web1
docker stats
docker stats web1
docker stats --no-stream
docker port web1
docker diff web1
docker wait web1
docker update --restart unless-stopped web1
```

---

# 7. `docker run` – detailed option reference

General syntax:

```bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

## Frequently used options

| Option | Meaning | Example |
|---|---|---|
| `-d` | Detached/background mode | `-d` |
| `-i` | Keep STDIN open | `-i` |
| `-t` | Allocate a TTY | `-t` |
| `--name` | Set container name | `--name web1` |
| `-p` | Publish a port | `-p 8080:80` |
| `-P` | Publish exposed ports to random host ports | `-P` |
| `-v` | Mount a volume or bind mount | `-v data:/data` |
| `--mount` | Explicit mount syntax | `--mount source=data,target=/data` |
| `-e` | Set environment variable | `-e APP_ENV=prod` |
| `--env-file` | Read environment variables from file | `--env-file .env` |
| `--network` | Select network | `--network app-net` |
| `--restart` | Set restart policy | `--restart unless-stopped` |
| `--rm` | Remove container when it exits | `--rm` |
| `-w` | Set working directory | `-w /app` |
| `-u` | Set user | `-u 1000:1000` |
| `--entrypoint` | Override image entrypoint | `--entrypoint sh` |
| `--memory` | Set memory limit | `--memory 512m` |
| `--cpus` | Set CPU limit | `--cpus 1` |
| `--read-only` | Use read-only root filesystem | `--read-only` |
| `--privileged` | Give extended privileges | Avoid unless required |
| `--hostname` | Set container hostname | `--hostname app01` |
| `--ip` | Set static IP on a suitable user-defined network | `--ip 172.20.0.10` |
| `--platform` | Select platform | `--platform linux/amd64` |
| `--pull` | Set image pull behavior | `--pull always` |

## Run examples

```bash
# Simple container
docker run nginx

# Detached container
docker run -d nginx

# Named container
docker run -d --name web1 nginx

# Interactive Ubuntu shell
docker run -it --name ubuntu1 ubuntu bash

# Auto-remove after exit
docker run --rm ubuntu echo "Hello Docker"

# Publish host port 8080 to container port 80
docker run -d --name web1 -p 8080:80 nginx

# Publish on all host interfaces explicitly
docker run -d -p 0.0.0.0:8080:80 nginx

# Publish only on host localhost
docker run -d -p 127.0.0.1:8080:80 nginx

# Publish all ports exposed by the image to random host ports
docker run -d -P nginx

# Mount named volume
docker run -d --name data1 -v mydata:/data ubuntu sleep infinity

# Bind mount a host directory
docker run -d --name web2 -v /home/ubuntu/html:/usr/share/nginx/html:ro nginx

# Environment variable
docker run -d --name app1 -e APP_ENV=production nginx

# Environment file
docker run -d --name app2 --env-file .env nginx

# Select a network
docker run -d --name web3 --network bridge nginx

# Restart policy
docker run -d --name web4 --restart unless-stopped nginx

# Memory and CPU limits
docker run -d --name limited --memory 512m --cpus 1 nginx

# Set hostname
docker run -d --name hosttest --hostname app01 nginx

# Run as a specific user
docker run --rm -u 1000:1000 ubuntu id
```

### Port mapping format

```bash
-p HOST_PORT:CONTAINER_PORT
```

Example:

```bash
docker run -d --name site -p 9012:80 nginx
```

- Host port: `9012`
- Container port: `80`

### Volume format using `-v`

```bash
-v VOLUME_NAME:CONTAINER_PATH
-v HOST_PATH:CONTAINER_PATH
```

Examples:

```bash
-v v1:/data
-v /home/user/data:/data
-v /home/user/config:/etc/config:ro
```

`:ro` means read-only inside the container.

### Explicit mount syntax

```bash
docker run -d \
  --name volume-demo \
  --mount type=volume,source=v1,target=/data \
  nginx
```

Bind mount:

```bash
docker run -d \
  --name bind-demo \
  --mount type=bind,source=/home/ubuntu/site,target=/usr/share/nginx/html,readonly \
  nginx
```

---

# 8. Entering containers: exec, attach and copy

## `docker exec`

Runs a new process inside an already running container.

```bash
docker exec CONTAINER command
docker exec -it web1 bash
docker exec -it web1 sh
docker exec -u root -it web1 bash
docker exec web1 hostname
docker exec web1 whoami
docker exec web1 pwd
docker exec web1 ls -l
docker exec web1 cat /etc/os-release
docker exec web1 df -h
```

## `docker attach`

Connects the terminal to the container's main process.

```bash
docker attach web1
```

For a normally attached interactive terminal, `Ctrl+P` followed by `Ctrl+Q` detaches without stopping the container. `Ctrl+C` may send an interrupt to the main process and can stop the application.

## `docker cp`

```bash
# Copy host file into container
docker cp index.html web1:/usr/share/nginx/html/

# Copy container file to host
docker cp web1:/etc/hosts ./hosts

# Copy a directory
docker cp ./website web1:/tmp/
```

## `docker commit`

Create an image from a container's current filesystem changes:

```bash
docker commit web1 myweb-backup:v1
```

Prefer Dockerfiles for repeatable, reviewable image builds. A commit does not include data stored in mounted volumes.

---

# 9. Logs, inspect, processes and troubleshooting

## Logs

```bash
docker logs web1
docker logs -f web1
docker logs --tail 50 web1
docker logs --tail 100 -f web1
docker logs --since 10m web1
docker logs --since 2026-01-01T10:00:00 web1
docker logs -t web1
```

## Inspect details

```bash
docker inspect web1
docker inspect --type container web1
docker inspect --format '{{.State.Status}}' web1
docker inspect --format '{{.State.ExitCode}}' web1
docker inspect --format '{{.Config.Image}}' web1
docker inspect --format '{{json .Mounts}}' web1
```

## Processes and resource use

```bash
docker top web1
docker stats
docker stats web1
docker stats --no-stream
```

## Docker daemon logs on systemd Linux

```bash
sudo journalctl -u docker
sudo journalctl -u docker -n 100
sudo journalctl -u docker -f
```

## Useful host checks

```bash
df -h
free -h
uptime
top
ps aux
sudo ss -tulpn
ip addr
ip route
```

## Common problems

### Docker daemon not running

```bash
sudo systemctl status docker
sudo systemctl start docker
sudo journalctl -u docker -n 100
```

### Container exited

```bash
docker ps -a
docker logs CONTAINER
docker inspect CONTAINER
```

### Port already in use

```bash
sudo ss -tulpn
docker ps
docker port CONTAINER
```

Choose a different host port if needed:

```bash
docker run -d -p 9013:80 nginx
```

### Container name already exists

```bash
docker ps -a
docker rename oldname newname
# Or remove the old container if it is no longer needed:
docker rm oldname
```

### Image not found

```bash
docker image ls
docker pull IMAGE_NAME:TAG
```

### Cannot access application

Check each layer:

```bash
docker ps
docker logs CONTAINER
docker port CONTAINER
docker inspect CONTAINER
sudo ss -tulpn
```

Also check the host firewall, cloud security group, application listen address, and whether the application is listening on the expected container port.

---

# 10. Dockerfile and image build

A Dockerfile contains instructions for building an image.

## Common Dockerfile instructions

| Instruction | Purpose |
|---|---|
| `FROM` | Select base image |
| `RUN` | Execute build-time command |
| `CMD` | Default command or arguments |
| `ENTRYPOINT` | Main executable |
| `COPY` | Copy files into image |
| `ADD` | Copy files; also supports URL and archive handling |
| `WORKDIR` | Set working directory |
| `ENV` | Set environment variable |
| `ARG` | Build-time variable |
| `EXPOSE` | Document intended listening port |
| `USER` | Set user for later build steps/runtime |
| `VOLUME` | Declare a mount point |
| `LABEL` | Add metadata |
| `HEALTHCHECK` | Define container health check |
| `SHELL` | Set shell for shell-form instructions |
| `STOPSIGNAL` | Set stop signal |

## Example Dockerfile

```dockerfile
FROM nginx:stable

LABEL maintainer="DevOps Team"

COPY index.html /usr/share/nginx/html/index.html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

Create an `index.html` file in the same folder:

```html
<h1>Hello from Docker!</h1>
<p>My first Docker web application.</p>
```

Build and run:

```bash
docker build -t mywebsite:v1 .
docker images
docker run -d --name mywebsite -p 8080:80 mywebsite:v1
docker ps
docker logs mywebsite
```

## Build commands

```bash
docker build -t myapp:v1 .
docker build -t myapp:latest .
docker build -f Dockerfile -t myapp:v1 .
docker build --no-cache -t myapp:v1 .
docker build --pull -t myapp:v1 .
docker image history myapp:v1
```

## `.dockerignore`

Example:

```text
.git
.venv
__pycache__
*.log
.env
node_modules
```

A `.dockerignore` file excludes matching files from the build context. Do not include secrets in an image or build context.

## Multi-stage build concept

Use multiple `FROM` instructions to build an application in one stage and copy only required runtime artifacts into a smaller final image.

---

# 11. Ports and networking

## Port mapping

```bash
docker run -d --name web1 -p 8080:80 nginx
docker port web1
```

## Network commands

```bash
docker network ls
docker network create app-net
docker network inspect app-net
docker network connect app-net web1
docker network disconnect app-net web1
docker network rm app-net
docker network prune
```

## Run a container on a user-defined network

```bash
docker network create app-net

docker run -d --name web1 --network app-net nginx

docker run -d --name web2 --network app-net nginx
```

On a user-defined bridge network, containers can normally discover each other by container name.

## Test container-to-container connectivity

```bash
docker exec web1 getent hosts web2
```

The tools available inside an image vary. Install diagnostic tools in a temporary test container if needed.

## Network drivers

- `bridge`: Common for standalone containers on one Docker host.
- `host`: Uses the host network namespace; port publishing is not used in the usual way.
- `none`: No external network connectivity configured by Docker.
- `overlay`: Used for multi-host container networking, commonly with Swarm.
- `macvlan`: Gives containers MAC addresses on a physical network.

---

# 12. Volumes and bind mounts

Volumes persist data independently of a container's lifecycle.

## Volume commands

```bash
docker volume ls
docker volume create v1
docker volume inspect v1
docker volume rm v1
docker volume prune
```

## Create a container with a named volume

```bash
docker run -d --name data-container -v v1:/data ubuntu sleep infinity
```

## Check the mount

```bash
docker inspect data-container
docker exec data-container df -h
```

## Write data

```bash
docker exec data-container sh -c 'echo Docker-data > /data/test.txt'
docker exec data-container cat /data/test.txt
```

## Remove the container and create another using the same volume

```bash
docker rm -f data-container

docker run -d --name data-container2 -v v1:/data ubuntu sleep infinity

docker exec data-container2 cat /data/test.txt
```

The file remains because it is stored in the named volume, not only in the removed container's writable layer.

## Bind mount example

```bash
mkdir -p /home/ubuntu/site
echo "Hello from host" > /home/ubuntu/site/index.html

docker run -d \
  --name bind-web \
  -p 8080:80 \
  -v /home/ubuntu/site:/usr/share/nginx/html:ro \
  nginx
```

## Volume vs bind mount

| Volume | Bind mount |
|---|---|
| Managed by Docker | Uses a host path |
| Created with `docker volume create` or by a run mount | Host path must be selected |
| Useful for persistent application data | Useful for sharing host files and development |
| `-v v1:/data` | `-v /host/path:/data` |

Do not use `docker volume prune` until you have checked which volumes are unused and whether they contain data you need.

---

# 13. Environment variables, resource limits and restart policies

## Environment variables

```bash
docker run -d --name app1 -e APP_ENV=production nginx
docker exec app1 printenv
```

Use an environment file:

```bash
docker run -d --name app2 --env-file .env nginx
```

Example `.env`:

```text
APP_ENV=production
APP_PORT=8080
```

Do not store passwords or other secrets in a committed `.env` file.

## Restart policies

```bash
docker run -d --restart no nginx
docker run -d --restart always nginx
docker run -d --restart on-failure nginx
docker run -d --restart unless-stopped nginx
```

Update an existing container:

```bash
docker update --restart unless-stopped web1
```

## Memory and CPU limits

```bash
docker run -d --name app1 --memory 512m nginx
docker run -d --name app2 --cpus 1 nginx
docker run -d --name app3 --memory 512m --cpus 1 nginx
```

Monitor:

```bash
docker stats
docker inspect app3
```

---

# 14. Docker Compose

Docker Compose manages applications described in a YAML file.

## Common Compose commands

```bash
docker compose version
docker compose up
docker compose up -d
docker compose down
docker compose stop
docker compose start
docker compose restart
docker compose ps
docker compose logs
docker compose logs -f
docker compose pull
docker compose build
docker compose config
docker compose images
docker compose top
docker compose exec web sh
docker compose run --rm web sh
```

## Example `compose.yaml`

```yaml
services:
  web:
    image: nginx:stable
    container_name: compose-web
    ports:
      - "8080:80"
    restart: unless-stopped

  redis:
    image: redis:7
    container_name: compose-redis
    restart: unless-stopped
```

Start and verify:

```bash
docker compose up -d
docker compose ps
docker compose logs -f
```

Stop and remove Compose-created containers and networks:

```bash
docker compose down
```

Volumes are not removed by default. `docker compose down -v` removes declared named and anonymous volumes associated with the Compose project; use carefully.

---

# 15. Docker system, disk usage and cleanup

## Disk usage

```bash
docker system df
docker system df -v
```

## Cleanup commands

```bash
docker container prune
docker image prune
docker image prune -a
docker network prune
docker volume prune
docker builder prune
docker system prune
docker system prune -a
```

## Cleanup all unused resources including unused volumes

```bash
docker system prune -a --volumes
```

**Warning:** Cleanup commands delete Docker resources. In particular, volume cleanup may permanently delete data. Inspect resources and confirm backups before cleanup.

## System events and version information

```bash
docker system events
docker version
docker info
docker context ls
docker context show
docker context inspect
```

---

# 16. Docker security

Important practices:

1. Use trusted base images.
2. Prefer specific image versions or digests for repeatable deployments.
3. Avoid running as root unless required.
4. Do not store passwords or tokens in Dockerfiles.
5. Do not expose unnecessary ports.
6. Limit CPU and memory.
7. Use read-only mounts where appropriate.
8. Avoid `--privileged` unless there is a clear requirement.
9. Keep Docker Engine and images updated.
10. Scan images for vulnerabilities.
11. Protect the Docker socket; access to it can grant host-level control.
12. Back up important volume data.

Example read-only root filesystem:

```bash
docker run -d --name readonly-web --read-only nginx
```

Some applications need writable temporary directories or paths, so test before using this setting.

---

# 17. Practical exercises

## Exercise 1 – Pull and run Nginx

```bash
docker pull nginx
docker run -d --name web1 -p 8080:80 nginx
docker ps
docker logs web1
```

## Exercise 2 – Enter the container

```bash
docker exec -it web1 bash
hostname
whoami
pwd
ls
cat /etc/os-release
exit
```

If Bash is unavailable, use `sh`.

## Exercise 3 – Stop, start and restart

```bash
docker stop web1
docker ps -a
docker start web1
docker restart web1
```

## Exercise 4 – Create and use a volume

```bash
docker volume create v1

docker run -d --name c1 \
  -v v1:/data \
  ubuntu sleep infinity

docker exec c1 sh -c 'echo hello > /data/file.txt'
docker exec c1 cat /data/file.txt
docker volume inspect v1
```

## Exercise 5 – Remove container but preserve volume

```bash
docker rm -f c1

docker run -d --name c2 \
  -v v1:/data \
  ubuntu sleep infinity

docker exec c2 cat /data/file.txt
```

## Exercise 6 – Practice the command from the screenshot

```bash
docker run -d \
  --name c5 \
  -v v1:/data \
  -p 9012:80 \
  08282c28ff42
```

Check:

```bash
docker ps -a
docker inspect c5
docker port c5
docker logs c5
docker exec -it c5 sh
```

This assumes the image ID still exists locally and that the image's application listens on port 80. Add `-it` instead of `-d` when you want an interactive foreground session; combining `-it -d` is possible but not usually necessary for a web-server container.

## Exercise 7 – Build your own image

Create a directory:

```bash
mkdir docker-demo
cd docker-demo
```

Create `index.html`:

```html
<h1>Hello from my Docker image!</h1>
```

Create `Dockerfile`:

```dockerfile
FROM nginx:stable
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

Build and run:

```bash
docker build -t mysite:v1 .
docker run -d --name mysite -p 8081:80 mysite:v1
docker ps
docker logs mysite
```

---

# 18. Daily-use Docker command cheat sheet

| Task | Command |
|---|---|
| Version | `docker --version` |
| System information | `docker info` |
| List images | `docker images` |
| Pull image | `docker pull nginx` |
| Running containers | `docker ps` |
| All containers | `docker ps -a` |
| Run background container | `docker run -d nginx` |
| Run named container | `docker run -d --name web1 nginx` |
| Map port | `docker run -d -p 8080:80 nginx` |
| Create container | `docker create nginx` |
| Start | `docker start web1` |
| Stop | `docker stop web1` |
| Restart | `docker restart web1` |
| Force stop | `docker kill web1` |
| Pause | `docker pause web1` |
| Resume | `docker unpause web1` |
| Enter container | `docker exec -it web1 sh` |
| Attach | `docker attach web1` |
| Logs | `docker logs web1` |
| Live logs | `docker logs -f web1` |
| Inspect | `docker inspect web1` |
| Processes | `docker top web1` |
| CPU/memory | `docker stats` |
| Port mapping | `docker port web1` |
| Copy file | `docker cp file.txt web1:/tmp/` |
| Remove container | `docker rm web1` |
| Remove image | `docker rmi nginx` |
| Build image | `docker build -t app:v1 .` |
| Tag image | `docker tag app:v1 user/app:v1` |
| Push image | `docker push user/app:v1` |
| List networks | `docker network ls` |
| Create network | `docker network create app-net` |
| List volumes | `docker volume ls` |
| Create volume | `docker volume create v1` |
| Compose up | `docker compose up -d` |
| Compose down | `docker compose down` |
| Disk usage | `docker system df` |
| Cleanup stopped containers | `docker container prune` |

---

# 19. Docker interview questions and answers

**1. What is Docker?**  
Docker is a platform for building, distributing and running applications in containers.

**2. What is an image?**  
A read-only template used to create containers.

**3. What is a container?**  
An instance of an image with its own runtime configuration and writable layer.

**4. Difference between `docker run` and `docker start`?**  
`run` creates and starts a new container. `start` starts an existing container.

**5. Difference between `docker stop` and `docker kill`?**  
`stop` requests graceful termination and may wait before force-killing. `kill` sends a signal immediately, SIGKILL by default.

**6. Difference between `docker exec` and `docker attach`?**  
`exec` starts a new process. `attach` connects to the main process.

**7. Difference between `docker ps` and `docker ps -a`?**  
`ps` shows running containers; `ps -a` shows all containers.

**8. What is Docker Hub?**  
A registry service for storing and distributing images.

**9. What is a Dockerfile?**  
A text file containing image build instructions.

**10. What is Docker Compose?**  
A tool for defining and managing multi-container applications.

**11. What is a Docker volume?**  
Docker-managed storage that can persist beyond a container's lifecycle.

**12. What is a bind mount?**  
A mount that maps a host file or directory into a container.

**13. What is port mapping?**  
Publishing a host port and forwarding traffic to a container port.

**14. What does `-d` mean?**  
Run the container in detached mode.

**15. What do `-it` mean?**  
`-i` keeps standard input open; `-t` allocates a pseudo-terminal.

**16. What does `-v v1:/data` mean?**  
Mount the Docker volume named `v1` at `/data` inside the container.

**17. What does `--name c5` mean?**  
Assign the container the name `c5`.

**18. What does `-p 9012:80` mean?**  
Map host port 9012 to container port 80.

**19. What is a Docker daemon?**  
The background service that manages Docker objects and requests.

**20. How do you troubleshoot a container?**  
Start with `docker ps -a`, `docker logs`, `docker inspect`, `docker stats` and host service logs.

**21. What is a multi-stage build?**  
A build with multiple stages that can keep build tools out of the final runtime image.

**22. What is a restart policy?**  
A setting that controls whether Docker restarts a container after it exits or the daemon restarts.

**23. What is the difference between `docker save` and `docker export`?**  
`save` archives an image with its layers and metadata; `export` archives a container filesystem.

**24. What is the difference between `docker load` and `docker import`?**  
`load` restores an image archive made with `save`; `import` creates an image from a filesystem archive.

**25. What happens to a named volume when a container is removed?**  
The volume normally remains until explicitly removed or pruned.

---

# 20. Important command differences – quick revision

| Commands | Difference |
|---|---|
| `run` vs `start` | Create + start new container vs start existing container |
| `create` vs `run` | Create only vs create and start |
| `stop` vs `kill` | Graceful stop vs immediate signal |
| `exec` vs `attach` | New process vs connect to main process |
| `ps` vs `ps -a` | Running vs all containers |
| `rm` vs `rmi` | Remove container vs remove image |
| `pull` vs `push` | Download image vs upload image |
| `save` vs `export` | Save image archive vs export container filesystem |
| `load` vs `import` | Restore image archive vs create image from filesystem archive |
| `volume` vs bind mount | Docker-managed storage vs host path mount |
| `-p` vs `-P` | Explicit port mapping vs publish exposed ports on random host ports |
| `-i` vs `-t` | Keep STDIN open vs allocate a terminal |
| `-d` vs `-it` | Background mode vs interactive terminal |
| `CMD` vs `ENTRYPOINT` | Default command/arguments vs main executable |

---

# Docker learning roadmap

```text
Linux
  |
Networking
  |
Git & GitHub
  |
Docker Basics
  |
Docker Images
  |
Docker Containers
  |
Dockerfile
  |
Docker Networking & Volumes
  |
Docker Compose
  |
Kubernetes
  |
Terraform
  |
AWS
  |
CI/CD
```

## Final learning advice

Do not try to memorize every Docker command in one day.

Practice these first:

1. `docker pull`
2. `docker images`
3. `docker run`
4. `docker ps`
5. `docker ps -a`
6. `docker start`
7. `docker stop`
8. `docker restart`
9. `docker exec -it`
10. `docker attach`
11. `docker logs`
12. `docker inspect`
13. `docker rm`
14. `docker rmi`
15. `docker run -v`
16. `docker run -p`
17. `docker network`
18. `docker volume`
19. `docker build`
20. `docker compose up -d`

**Goal:** Understand what every option does, practice on your Ubuntu/RHEL lab, and learn how Docker fits into a real DevOps deployment.
