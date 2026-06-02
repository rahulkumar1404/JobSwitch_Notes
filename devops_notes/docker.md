# 🐳 Docker — Complete Notes (Zero to Advance)
### Hindi Explanation + English Interview Answers

> 🏠 [Back to Index](./README.md) | ☸️ [Kubernetes Notes](./Kubernetes-Complete-Notes.md)

---

## Table of Contents
- [Chapter 1: Docker Overview](#chapter-1-docker-overview)
- [Chapter 2: Docker vs Virtual Machine](#chapter-2-docker-vs-virtual-machine)
- [Chapter 3: Docker Architecture](#chapter-3-docker-architecture)
- [Chapter 4: Docker Installation](#chapter-4-docker-installation)
- [Chapter 5: Docker Images — Complete Guide](#chapter-5-docker-images--complete-guide)
- [Chapter 6: Docker Container Lifecycle](#chapter-6-docker-container-lifecycle)
- [Chapter 7: Dockerfile — All Instructions](#chapter-7-dockerfile--all-instructions)
- [Chapter 8: Multi-Stage Builds](#chapter-8-multi-stage-builds)
- [Chapter 9: Docker BuildKit & Build Cache](#chapter-9-docker-buildkit--build-cache)
- [Chapter 10: Docker Volumes](#chapter-10-docker-volumes)
- [Chapter 11: Docker Networking](#chapter-11-docker-networking)
- [Chapter 12: Docker Compose](#chapter-12-docker-compose)
- [Chapter 13: Docker Hub](#chapter-13-docker-hub)
- [Chapter 14: Docker Private Registry](#chapter-14-docker-private-registry)
- [Chapter 15: Docker Swarm](#chapter-15-docker-swarm)
- [Chapter 16: Docker Services](#chapter-16-docker-services)
- [Chapter 17: Docker Stacks](#chapter-17-docker-stacks)
- [Chapter 18: Docker Machine & Context](#chapter-18-docker-machine--context)
- [Chapter 19: Docker Logging Drivers](#chapter-19-docker-logging-drivers)
- [Chapter 20: Docker Security & Content Trust](#chapter-20-docker-security--content-trust)
- [Chapter 21: Docker Resource Management](#chapter-21-docker-resource-management)
- [Chapter 22: Docker in CI/CD](#chapter-22-docker-in-cicd)
- [Chapter 23: Docker Troubleshooting](#chapter-23-docker-troubleshooting)
- [Chapter 24: Docker Best Practices](#chapter-24-docker-best-practices)
- [Chapter 25: Docker Interview Q&A](#chapter-25-docker-interview-qa)
- [Chapter 26: Quick Reference Cheat Sheet](#chapter-26-quick-reference-cheat-sheet)

---

## Chapter 1: Docker Overview

> 🔗 Related: [Architecture](#chapter-3-docker-architecture) | [vs VM](#chapter-2-docker-vs-virtual-machine)

### 🇮🇳 Hindi Explanation
Docker ek **containerization platform** hai. Tumhari application ek jagah chalti hai lekin doosri jagah nahi chalti — ye "it works on my machine" problem Docker solve karta hai.

Docker ek **container** banata hai jisme application ke saath uske saare dependencies (libraries, configs, OS packages) pack ho jaate hain. Ye container kahi bhi chalao — laptop, server, cloud — same behavior milega.

**Real life analogy:** Jaise ek **shipping container** mein saamaan pack karte ho — saamaan wahi rahega chahe ship se jaye, truck se jaye ya plane se.

### Key Terms
| Term | Meaning |
|------|---------|
| **Image** | Application ka blueprint (read-only template) |
| **Container** | Image ka running instance |
| **Dockerfile** | Image banane ki recipe |
| **Registry** | Images ka store (Docker Hub) |
| **Daemon** | Background service jo containers chalata hai |

### 🎯 Interview Answer
**Q: What is Docker and why do we use it?**

Docker is an open-source containerization platform that packages applications with all their dependencies into containers. Containers share the host OS kernel making them lightweight (MBs vs GBs for VMs) and fast to start (seconds vs minutes). We use Docker to: eliminate environment inconsistencies, enable microservices architecture, simplify CI/CD pipelines, and ensure reproducible builds across dev/staging/production.

---

## Chapter 2: Docker vs Virtual Machine

> 🔗 Related: [Docker Architecture](#chapter-3-docker-architecture) | [Docker Overview](#chapter-1-docker-overview)

### 🇮🇳 Hindi Explanation
**Virtual Machine (VM):** Hypervisor pe poora OS run karta hai — heavy (GBs), slow boot (minutes), zyada RAM use karta hai.

**Docker Container:** Host OS ka kernel share karta hai, sirf app + dependencies pack karta hai — light (MBs), fast start (seconds).

```
VM Architecture:           Docker Architecture:
┌─────────────────┐        ┌─────────────────┐
│   App A  App B  │        │  App A   App B  │
│   Bins   Bins   │        │  Bins    Bins   │
│   OS A   OS B   │        ├─────────────────┤
├─────────────────┤        │  Docker Engine  │
│   Hypervisor    │        ├─────────────────┤
├─────────────────┤        │    Host OS      │
│   Host OS       │        ├─────────────────┤
├─────────────────┤        │  Infrastructure │
│  Infrastructure │        └─────────────────┘
└─────────────────┘
```

| Feature | VM | Docker |
|---|---|---|
| Size | GBs | MBs |
| Boot Time | Minutes | Seconds |
| OS | Full Guest OS | Shared Host Kernel |
| Isolation | Complete (Hypervisor) | Process-level (cgroups/namespaces) |
| Performance | Overhead | Near-native |
| Portability | Less portable | Highly portable |
| Use Case | Full OS isolation | App containerization |

### 🎯 Interview Answer
**Q: Docker vs VM — what's the difference?**

VMs run a complete OS on a hypervisor — each VM has its own kernel, making them resource-heavy. Docker containers share the host OS kernel and only package the app and its dependencies. This makes containers: faster to start (seconds vs minutes), smaller (MBs vs GBs), more efficient (run 10x more containers vs VMs on same hardware). VMs provide stronger isolation (suitable for running different OSes). Docker provides better density and speed — ideal for microservices. In practice, containers often run inside VMs in production for both security and density benefits.

---

## Chapter 3: Docker Architecture

> 🔗 Related: [Docker Overview](#chapter-1-docker-overview) | [Docker Images](#chapter-5-docker-images--complete-guide)

### 🇮🇳 Hindi Explanation
Docker **client-server architecture** follow karta hai:

1. **Docker Client** — Tumhara terminal (`docker` CLI). Commands type karte ho yahan.
2. **Docker Daemon (dockerd)** — Background mein chalne wala server. Client ke commands execute karta hai.
3. **Docker Registry** — Images ka storage. Docker Hub (public), ya private registry.
4. **containerd** — Low-level container runtime jo daemon ke neeche kaam karta hai.
5. **runc** — Actual containers create karta hai (OCI standard).

```
┌─────────────────────────────────────────────────────────┐
│  Docker Client (CLI)                                    │
│  docker build | docker pull | docker run                │
└──────────────────────────┬──────────────────────────────┘
                           │ REST API (Unix socket/TCP)
┌──────────────────────────▼──────────────────────────────┐
│  Docker Daemon (dockerd)                                │
│  ┌─────────────┐  ┌──────────────┐  ┌───────────────┐  │
│  │   Images    │  │  Containers  │  │   Networks    │  │
│  │  Manager    │  │   Manager    │  │   Manager     │  │
│  └─────────────┘  └──────────────┘  └───────────────┘  │
│  ┌──────────────────────────────────────────────────┐   │
│  │  containerd → containerd-shim → runc             │   │
│  └──────────────────────────────────────────────────┘   │
└──────────────────────────┬──────────────────────────────┘
                           │
┌──────────────────────────▼──────────────────────────────┐
│  Docker Registry (Docker Hub / Private)                 │
│  Images stored as layers                                │
└─────────────────────────────────────────────────────────┘
```

### Docker Objects
| Object | Description |
|--------|-------------|
| **Image** | Read-only layered filesystem template |
| **Container** | Writable layer on top of image |
| **Volume** | Persistent data storage |
| **Network** | Communication between containers |
| **Secret** | Sensitive data management |
| **Config** | Non-sensitive config data (Swarm) |

### 🎯 Interview Answer
**Q: Explain Docker Architecture.**

Docker uses client-server architecture. The **Docker CLI** sends commands via REST API to **Docker Daemon (dockerd)** running on the host. The daemon manages the full container lifecycle using **containerd** (high-level runtime) which uses **runc** (low-level OCI runtime) to create actual containers. Images are stored in and pulled from **Docker Registries** (Docker Hub is default). Key components: **Images** (read-only layered templates), **Containers** (running image instances with a writable layer), **Volumes** (persistent storage), **Networks** (container communication). Communication happens via Unix socket (`/var/run/docker.sock`) locally or TCP for remote.

---

## Chapter 4: Docker Installation

> 🔗 Related: [Basic Commands](#chapter-6-docker-container-lifecycle) | [Docker Architecture](#chapter-3-docker-architecture)

### 🇮🇳 Hindi Explanation
Docker alag-alag OS pe alag tarike se install hota hai. Linux pe Docker Engine directly install hota hai. Mac/Windows pe Docker Desktop use karte hain.

### Linux (Ubuntu/Debian)
```bash
# Method 1: Official script (fastest)
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Method 2: Manual installation
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg

# GPG key add karo
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Repository add karo
echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list

sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io \
  docker-buildx-plugin docker-compose-plugin

# Post-installation
sudo systemctl start docker
sudo systemctl enable docker

# Sudo ke bina Docker use karo
sudo usermod -aG docker $USER
newgrp docker                          # Group apply karo (re-login nahi chahiye)

# Verify
docker version
docker run hello-world
```

### Linux (CentOS/RHEL)
```bash
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo \
  https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce docker-ce-cli containerd.io
sudo systemctl start docker && sudo systemctl enable docker
```

### Docker Daemon Configuration
```json
// /etc/docker/daemon.json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "20m",
    "max-file": "5"
  },
  "default-ulimits": {
    "nofile": { "Hard": 64000, "Soft": 64000 }
  },
  "storage-driver": "overlay2",
  "insecure-registries": ["myregistry.local:5000"],
  "registry-mirrors": ["https://mirror.example.com"],
  "live-restore": true,
  "userland-proxy": false
}
```
```bash
# Daemon restart karo config change ke baad
sudo systemctl daemon-reload
sudo systemctl restart docker
```

---

## Chapter 5: Docker Images — Complete Guide

> 🔗 Related: [Dockerfile](#chapter-7-dockerfile--all-instructions) | [Docker Hub](#chapter-13-docker-hub) | [Multi-Stage Builds](#chapter-8-multi-stage-builds)

### 🇮🇳 Hindi Explanation
**Docker Image** ek **read-only layered filesystem** hai. Har `RUN`, `COPY`, `ADD` instruction ek nayi layer banati hai. Layers **Union Filesystem** (OverlayFS) se stack hoti hain.

**Image Layers benefit:** Agar do images same base image share karte hain toh wo layer sirf ek baar download hogi — disk space aur bandwidth bachti hai.

```
Image Layers (Bottom to Top):
┌─────────────────────┐  ← Your App Layer (COPY . .)
├─────────────────────┤  ← npm install Layer (RUN npm install)
├─────────────────────┤  ← package.json Layer (COPY package.json)
├─────────────────────┤  ← Node.js Layer (FROM node:18)
└─────────────────────┘  ← Base OS Layer (scratch/alpine)
```

### Image Commands — Complete Reference
```bash
# ============================================
# PULL & SEARCH
# ============================================
docker pull nginx                      # Latest tag pull karo
docker pull nginx:1.25-alpine          # Specific tag
docker pull ubuntu:22.04
docker pull myregistry.com/myapp:v1.0  # Private registry se

docker search nginx                    # Docker Hub search
docker search --filter=is-official=true nginx
docker search --filter=stars=1000 nginx
docker search --limit 5 node

# ============================================
# LIST & INSPECT
# ============================================
docker images                          # All images
docker image ls                        # Same
docker images -a                       # Intermediate layers bhi dikhao
docker images --digests                # SHA256 digest dikhao
docker images --no-trunc               # Full image ID
docker images -q                       # Sirf IDs

docker image inspect nginx             # Full JSON details
docker image inspect --format='{{.Config.Env}}' nginx  # Specific field
docker image history nginx             # Layer history
docker image history --no-trunc nginx  # Full commands

# ============================================
# TAG & PUSH
# ============================================
docker tag nginx:latest mynginx:v1.0   # Image tag karo
docker tag myapp:latest myrepo/myapp:v2.1.0

docker push myrepo/myapp:v2.1.0        # Registry pe push
docker push myrepo/myapp               # All tags push

# ============================================
# SAVE, LOAD, EXPORT, IMPORT
# ============================================
# save/load — Image ko file mein (layers ke saath)
docker save nginx:latest > nginx.tar
docker save -o myapp.tar myapp:v1 myapp:v2  # Multiple images
docker load < nginx.tar
docker load -i myapp.tar

# export/import — Container filesystem (layers nahi)
docker export container_id > container.tar
docker import container.tar myimage:latest
docker import --change "CMD [\"nginx\",\"-g\",\"daemon off;\"]" \
  container.tar myimage:latest

# Difference:
# save/load  = Image ke saath metadata + layers (Docker to Docker)
# export/import = Sirf filesystem (no history, no layers)

# ============================================
# COMMIT — Container se Image banana
# ============================================
docker run -it ubuntu bash
# Inside: apt-get install -y curl vim
# exit
docker commit container_id myubuntu:with-tools
docker commit -m "Added curl and vim" -a "YourName" \
  container_id myubuntu:v1.0
# Note: Commit avoid karo production mein — Dockerfile use karo

# ============================================
# REMOVE
# ============================================
docker rmi nginx                       # Image remove karo
docker rmi nginx:latest nginx:alpine   # Multiple images
docker rmi -f image_id                 # Force remove
docker image rm $(docker images -q)    # Saari images remove
docker image prune                     # Dangling images remove (untagged)
docker image prune -a                  # All unused images
docker image prune -a --filter "until=24h"  # 24 ghante purani

# ============================================
# BUILD
# ============================================
docker build -t myapp:1.0 .
docker build -t myapp:1.0 -f Dockerfile.prod .
docker build --no-cache -t myapp:1.0 .
docker build --build-arg NODE_ENV=production -t myapp .
docker build --target builder -t myapp-builder .  # Specific stage
docker build --platform linux/amd64 -t myapp .    # Specific platform
```

### Image Naming Convention
```
registry/repository:tag
│         │           └── Version (default: latest)
│         └──────────── Image name
└──────────────────────── Registry URL (default: docker.io)

Examples:
docker.io/library/nginx:1.25        → Official image
docker.io/username/myapp:v1.0       → User image
myregistry.com:5000/team/app:v2.1   → Private registry
```

---

## Chapter 6: Docker Container Lifecycle

> 🔗 Related: [Docker Images](#chapter-5-docker-images--complete-guide) | [Docker Volumes](#chapter-10-docker-volumes) | [Docker Networking](#chapter-11-docker-networking)

### 🇮🇳 Hindi Explanation
Container ke alag-alag **states** hote hain: created → running → paused → stopped → removed.

```
Container States:
                 docker start
created ──────────────────────► running
   ▲                              │  │
   │ docker create                │  │ docker pause
   │                              ▼  │
   │                           paused◄┘
   │                              │
   │                              │ docker unpause
   │                              ▼
   │                           running
   │                              │
   │                              │ docker stop / docker kill
   │                              ▼
   └──────────────────────── exited/stopped
                                  │
                                  │ docker rm
                                  ▼
                               deleted
```

### Container Commands — Complete Reference
```bash
# ============================================
# CREATE & RUN
# ============================================
docker create nginx                    # Container banao (start mat karo)
docker start container_id              # Start karo

docker run nginx                       # Pull + Create + Start (foreground)
docker run -d nginx                    # Detached (background)
docker run -it ubuntu bash             # Interactive + TTY
docker run --name myapp nginx          # Custom naam
docker run --rm nginx                  # Exit pe auto-delete
docker run -d --restart=always nginx   # Always restart

# Port mapping
docker run -p 8080:80 nginx            # host_port:container_port
docker run -p 127.0.0.1:8080:80 nginx  # Specific host IP pe bind
docker run -p 8080:80/tcp -p 8081:81/udp nginx  # Protocol specify
docker run -P nginx                    # Random host ports assign

# Environment variables
docker run -e NODE_ENV=production myapp
docker run --env-file .env myapp

# Working directory
docker run -w /app myapp

# User specify karo
docker run -u 1000:1000 myapp
docker run -u appuser myapp
docker run --user root myapp

# Hostname
docker run --hostname mycontainer nginx

# DNS
docker run --dns 8.8.8.8 nginx
docker run --add-host myhost:192.168.1.100 nginx

# ============================================
# MANAGE RUNNING CONTAINERS
# ============================================
docker ps                              # Running containers
docker ps -a                           # All containers
docker ps -q                           # Sirf IDs
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"
docker ps --filter status=running
docker ps --filter name=myapp
docker ps --filter ancestor=nginx      # Specific image se bane

# Stop karo
docker stop container_id               # SIGTERM bhejo (graceful, 10s wait)
docker stop -t 30 container_id         # 30 second wait karo
docker kill container_id               # SIGKILL (immediate)
docker kill --signal=SIGHUP container_id  # Custom signal

# Pause/Unpause (cgroup freezer use karta hai)
docker pause container_id
docker unpause container_id

# Restart
docker restart container_id
docker restart -t 5 container_id       # 5 sec ke baad

# ============================================
# INTERACT WITH CONTAINERS
# ============================================
# exec — Chalta container mein naya process chalao
docker exec -it container_id bash
docker exec -it container_id sh        # bash nahi hai toh
docker exec container_id ls /app
docker exec -it -e MYVAR=hello container_id bash
docker exec -u root container_id bash  # Root se

# attach — Container ke main process se attach karo
docker attach container_id
# Detach karo bina stop kare: Ctrl+P then Ctrl+Q

# copy files
docker cp container_id:/app/file.txt ./file.txt  # Container se host
docker cp ./file.txt container_id:/app/           # Host se container

# diff — Container mein kya changes aye
docker diff container_id
# A = Added, C = Changed, D = Deleted

# ============================================
# INSPECT & MONITOR
# ============================================
docker inspect container_id
docker inspect --format='{{.NetworkSettings.IPAddress}}' container_id
docker inspect --format='{{.State.Status}}' container_id
docker inspect --format='{{json .Config}}' container_id

docker logs container_id
docker logs -f container_id            # Follow (live)
docker logs --tail=50 container_id     # Last 50 lines
docker logs --since="2024-01-01" container_id
docker logs --since=1h container_id    # Last 1 hour
docker logs -t container_id            # Timestamps ke saath

docker stats                           # All containers live stats
docker stats container_id              # Specific container
docker stats --no-stream               # One-time snapshot
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}"

docker top container_id                # Container processes
docker top container_id aux            # Detailed process info
docker port container_id               # Port mappings

docker events                          # Real-time Docker events
docker events --filter type=container
docker events --filter event=die
docker events --since="1h"

# ============================================
# REMOVE
# ============================================
docker rm container_id
docker rm -f container_id              # Force (running ko bhi)
docker rm -v container_id              # Volumes bhi delete karo
docker rm $(docker ps -aq)             # Saare stopped containers
docker container prune                 # All stopped containers
docker container prune --filter "until=24h"

# ============================================
# RENAME & UPDATE
# ============================================
docker rename old_name new_name
docker update --memory=512m --cpus=1 container_id
docker update --restart=unless-stopped container_id
```

### Container Restart Policies
| Policy | Behavior |
|--------|----------|
| `no` | Never restart (default) |
| `always` | Always restart, even on Docker daemon restart |
| `on-failure` | Only on non-zero exit code |
| `on-failure:3` | Max 3 times on failure |
| `unless-stopped` | Always restart except manually stopped |

### 🎯 Interview Answer
**Q: What is the difference between docker exec and docker attach?**

`docker exec` creates a **new process** inside the running container — great for debugging (run bash, check files, etc.) without interfering with the main process. `docker attach` **connects your terminal to the main process** (PID 1) of the container — if you exit, the container stops. For debugging always prefer `docker exec -it container_id bash`. Use `Ctrl+P, Ctrl+Q` to detach from `docker attach` without stopping the container.

---

## Chapter 7: Dockerfile — All Instructions

> 🔗 Related: [Multi-Stage Builds](#chapter-8-multi-stage-builds) | [BuildKit](#chapter-9-docker-buildkit--build-cache) | [Best Practices](#chapter-24-docker-best-practices)

### 🇮🇳 Hindi Explanation
**Dockerfile** instructions ki list hai jisse Docker image baanti hai. Har instruction ek **layer** banati hai. `.dockerignore` file batati hai kya **build context** mein include nahi karna.

### .dockerignore
```
# .dockerignore file (ZAROORI — build context chhota karo)
node_modules/
.git/
.gitignore
.env
*.log
*.md
dist/
build/
coverage/
.DS_Store
Dockerfile*
docker-compose*
.dockerignore
tests/
docs/
```

### Dockerfile — All Instructions
```dockerfile
# ============================================
# FROM — Base image (har Dockerfile ka pehla instruction)
# ============================================
FROM ubuntu:22.04
FROM node:18-alpine                    # Alpine = minimal Linux
FROM node:18-alpine AS builder         # Named stage (multi-stage)
FROM scratch                           # Empty image (Go binaries ke liye)
FROM python:3.11-slim-bookworm         # Slim = minimal packages

# ============================================
# LABEL — Metadata (OCI standard)
# ============================================
LABEL maintainer="you@example.com"
LABEL org.opencontainers.image.title="My App"
LABEL org.opencontainers.image.version="1.0.0"
LABEL org.opencontainers.image.description="My application"
LABEL org.opencontainers.image.source="https://github.com/user/repo"

# ============================================
# ARG — Build-time variables (runtime mein nahi milte)
# ============================================
ARG NODE_VERSION=18
ARG APP_PORT=3000
ARG BUILD_DATE
ARG GIT_COMMIT

# ARG before FROM = global (sirf FROM mein use)
ARG BASE_IMAGE=node:18-alpine
FROM ${BASE_IMAGE}
# FROM ke baad ARG dobara declare karo
ARG NODE_VERSION

# Use karo: docker build --build-arg NODE_VERSION=20 .
# SECURITY: Secrets ARG mein mat daalo — docker history mein dikhte hain!

# ============================================
# ENV — Environment variables (runtime mein bhi available)
# ============================================
ENV NODE_ENV=production
ENV PORT=3000 \
    APP_HOME=/app \
    LOG_LEVEL=info

# ARG se ENV set karo
ARG APP_VERSION
ENV APP_VERSION=${APP_VERSION:-1.0.0}

# ============================================
# WORKDIR — Working directory set karo
# ============================================
WORKDIR /app
# Agar directory nahi hai toh create ho jaati hai
# Multiple WORKDIR ho sakte hain (relative path support)
WORKDIR /app/backend
WORKDIR logs                           # → /app/backend/logs

# ============================================
# COPY — Files copy karo build context se
# ============================================
COPY . .                               # Sab kuch
COPY package.json package-lock.json ./
COPY src/ ./src/
COPY --chown=node:node . .             # Ownership set karo
COPY --chmod=755 entrypoint.sh .
COPY *.json ./                         # Wildcard
# Multi-stage se copy:
COPY --from=builder /app/dist ./dist
COPY --from=0 /app/build ./            # Stage number se

# ============================================
# ADD — COPY jaisa + extra features
# ============================================
ADD app.tar.gz /app/                   # Auto-extract tar files
ADD https://example.com/file.txt /app/ # URL se download (avoid karo)
# Best practice: COPY prefer karo ADD se unless extraction chahiye

# ============================================
# RUN — Build time commands chalao
# ============================================
# Shell form (sh -c mein chalega)
RUN apt-get update

# Exec form (preferred, shell nahi chahiye)
RUN ["apt-get", "install", "-y", "curl"]

# Multiple commands ek RUN mein (layers kam karo)
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       curl \
       vim \
       git \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

# Cache mount use karo (BuildKit)
RUN --mount=type=cache,target=/root/.npm \
    npm ci --only=production

# Secret mount (build mein secret use karo, image mein nahi aayega)
RUN --mount=type=secret,id=mypassword \
    cat /run/secrets/mypassword | some-command

# ============================================
# EXPOSE — Port document karo
# ============================================
EXPOSE 3000                            # TCP default
EXPOSE 80/tcp
EXPOSE 53/udp
EXPOSE 3000 8080                       # Multiple ports
# Note: EXPOSE sirf documentation hai — actual publish -p se hota hai

# ============================================
# VOLUME — Mount point declare karo
# ============================================
VOLUME ["/data", "/logs"]
VOLUME /var/lib/postgresql/data
# Container start hone pe automatically volume create hota hai

# ============================================
# USER — Container kis user se run karega
# ============================================
# Root mat use karo production mein!
RUN groupadd -r appgroup && useradd -r -g appgroup appuser
USER appuser
USER appuser:appgroup
USER 1000:1000                         # UID:GID

# ============================================
# CMD — Default command (override ho sakta hai)
# ============================================
# Exec form (PREFERRED)
CMD ["node", "server.js"]
CMD ["npm", "start"]
CMD ["nginx", "-g", "daemon off;"]

# Shell form (shell expansion hoti hai)
CMD echo "Hello $NAME"

# Sirf ek CMD ho sakta hai (last wala count hoga)
# docker run image OTHER_COMMAND se override ho jaata hai

# ============================================
# ENTRYPOINT — Main executable (override mushkil)
# ============================================
ENTRYPOINT ["node"]
CMD ["server.js"]
# docker run image → node server.js
# docker run image app.js → node app.js (CMD override)
# docker run --entrypoint python image → python

ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["postgres"]

# ============================================
# HEALTHCHECK — Container health monitor karo
# ============================================
HEALTHCHECK --interval=30s \
            --timeout=10s \
            --start-period=40s \
            --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1

HEALTHCHECK --interval=5s --timeout=3s \
  CMD pg_isready -U postgres || exit 1

HEALTHCHECK NONE                       # Healthcheck disable karo (parent se)

# ============================================
# SHELL — Default shell change karo
# ============================================
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
# Windows ke liye:
SHELL ["powershell", "-command"]

# ============================================
# STOPSIGNAL — Container stop signal
# ============================================
STOPSIGNAL SIGTERM                     # Default
STOPSIGNAL SIGINT

# ============================================
# ONBUILD — Jab ye image base image bane tab trigger
# ============================================
ONBUILD COPY . /app
ONBUILD RUN npm install

# ============================================
# COMPLETE PRODUCTION DOCKERFILE EXAMPLE
# ============================================
FROM node:18-alpine AS base
LABEL org.opencontainers.image.source="https://github.com/user/app"
WORKDIR /app
ENV NODE_ENV=production PORT=3000

FROM base AS deps
COPY package*.json ./
RUN --mount=type=cache,target=/root/.npm \
    npm ci --only=production && \
    npm cache clean --force

FROM base AS builder
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build && npm run test

FROM base AS production
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
COPY --from=deps --chown=appuser:appgroup /app/node_modules ./node_modules
COPY --from=builder --chown=appuser:appgroup /app/dist ./dist
USER appuser
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1
CMD ["node", "dist/server.js"]
```

### 🎯 Interview Answer
**Q: What is the difference between CMD and ENTRYPOINT?**

Both define what runs when a container starts. **CMD** provides default arguments that can be completely overridden — `docker run image newcommand` replaces CMD entirely. **ENTRYPOINT** defines the executable that always runs — it cannot be easily overridden (only with `--entrypoint` flag). Best practice: use **ENTRYPOINT** for the main executable and **CMD** for default arguments. Example: `ENTRYPOINT ["node"]` + `CMD ["server.js"]` → runs `node server.js` by default. `docker run image app.js` → runs `node app.js` (CMD overridden). This makes containers behave like executables.

**Q: What is the difference between COPY and ADD?**

Both copy files into the image. **COPY** is straightforward — copies files/directories from build context. **ADD** has extra features: automatically extracts `.tar.gz` files and can download from URLs. Best practice: **always prefer COPY** unless you specifically need tar auto-extraction. Using ADD for URLs is unreliable (no caching, security risk). For URLs, use `RUN curl` instead for better cache control.

---

## Chapter 8: Multi-Stage Builds

> 🔗 Related: [Dockerfile](#chapter-7-dockerfile--all-instructions) | [Best Practices](#chapter-24-docker-best-practices)

### 🇮🇳 Hindi Explanation
Build ke liye compiler, test tools, etc. chahiye hote hain lekin production image mein sirf output chahiye hota hai. Multi-stage builds se final image bahut chhoti ban jaati hai — **security improve hoti hai, deploy fast hota hai**.

```dockerfile
# ============================================
# NODE.JS — Multi-Stage
# ============================================
FROM node:18-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci

FROM node:18-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM node:18-alpine AS production
WORKDIR /app
ENV NODE_ENV=production
RUN addgroup -S app && adduser -S app -G app
COPY --from=deps /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
USER app
EXPOSE 3000
CMD ["node", "dist/server.js"]
# Result: ~150MB instead of ~1GB

# ============================================
# GO — Scratch image (smallest possible)
# ============================================
FROM golang:1.21-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -ldflags="-w -s" -o main .

FROM scratch
COPY --from=builder /app/main /main
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
EXPOSE 8080
USER 1000
ENTRYPOINT ["/main"]
# Result: ~10MB instead of ~800MB

# ============================================
# JAVA — Spring Boot
# ============================================
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn package -DskipTests

FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
RUN addgroup -S spring && adduser -S spring -G spring
COPY --from=builder /app/target/*.jar app.jar
USER spring
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]

# ============================================
# PYTHON — Flask App
# ============================================
FROM python:3.11-slim AS builder
WORKDIR /app
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

FROM python:3.11-slim
WORKDIR /app
RUN adduser --disabled-password --gecos '' appuser
COPY --from=builder /root/.local /home/appuser/.local
COPY --chown=appuser:appuser . .
USER appuser
ENV PATH=/home/appuser/.local/bin:$PATH
EXPOSE 5000
CMD ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]

# ============================================
# Specific stage build karo
# ============================================
docker build --target builder -t myapp-builder .
docker build --target deps -t myapp-deps .
docker build --target production -t myapp:latest .
```

### 🎯 Interview Answer
**Q: What are multi-stage builds and why are they important?**

Multi-stage builds use multiple `FROM` instructions in one Dockerfile. Each stage starts fresh, and you selectively `COPY --from=<stage>` only needed artifacts. Benefits: **1) Smaller images** — build tools (compilers, test frameworks) don't end up in production (Go app: 800MB → 10MB); **2) Better security** — smaller attack surface, no dev tools in production; **3) Cleaner builds** — single Dockerfile for entire build pipeline; **4) Faster deployments** — smaller images pull and start faster. Best practice: name your stages and use `docker build --target <stage>` to build specific stages in CI/CD.

---

## Chapter 9: Docker BuildKit & Build Cache

> 🔗 Related: [Dockerfile](#chapter-7-dockerfile--all-instructions) | [Multi-Stage Builds](#chapter-8-multi-stage-builds)

### 🇮🇳 Hindi Explanation
**BuildKit** Docker ka next-generation build engine hai. Ye default hai Docker 23.0+ mein. Ye faster, smarter aur more secure builds provide karta hai.

**Build Cache** — Docker layers cache karta hai. Agar layer nahi badi toh rebuild nahi hogi — **builds fast hoti hain**.

### Build Cache Kaise Kaam Karta Hai
```
Layer 1: FROM node:18-alpine     → Cache hit (same image)
Layer 2: WORKDIR /app            → Cache hit (same command)
Layer 3: COPY package.json ./    → Cache MISS! (file changed)
Layer 4: RUN npm install         → Cache MISS! (layer 3 changed)
Layer 5: COPY . .                → Cache MISS!
Layer 6: RUN npm build           → Cache MISS!

Lesson: Dependencies pehle copy karo, source code baad mein!
```

### Cache Optimization Pattern
```dockerfile
# ❌ BAD — Source change pe npm install dobara hoga
FROM node:18-alpine
WORKDIR /app
COPY . .                           # Sab kuch copy (har change pe invalidate)
RUN npm install                    # Har baar dobara
RUN npm run build

# ✅ GOOD — npm install sirf package.json change pe hoga
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./              # Pehle dependencies
RUN npm ci                         # Ye layer cache rehega
COPY . .                           # Source code baad mein
RUN npm run build
```

### BuildKit Features
```bash
# BuildKit enable karo
export DOCKER_BUILDKIT=1
docker build -t myapp .

# Ya docker-compose mein
export COMPOSE_DOCKER_CLI_BUILD=1

# BuildKit flags
docker buildx build \
  --platform linux/amd64,linux/arm64 \  # Multi-platform
  --cache-from type=registry,ref=myapp:cache \
  --cache-to type=registry,ref=myapp:cache,mode=max \
  --push \
  -t myapp:latest .

# Build progress output
docker build --progress=plain .    # Detailed output
docker build --progress=auto .     # Default
docker build --progress=tty .      # Interactive

# Build secret use karo (image mein nahi aata)
docker build --secret id=mypassword,src=./secret.txt .
# Dockerfile mein:
# RUN --mount=type=secret,id=mypassword cat /run/secrets/mypassword

# SSH forward karo (private git repos ke liye)
docker build --ssh default .
# Dockerfile mein:
# RUN --mount=type=ssh git clone git@github.com:private/repo.git
```

### Cache Control
```bash
# Cache ignore karo
docker build --no-cache -t myapp .

# Specific layer se cache invalidate karo
ARG CACHEBUST=1
RUN curl -o data.json https://api.example.com/data  # Har baar fresh

# External cache use karo (CI/CD mein useful)
docker buildx build \
  --cache-from type=gha \        # GitHub Actions cache
  --cache-to type=gha,mode=max \
  -t myapp .
```

---

## Chapter 10: Docker Volumes

> 🔗 Related: [Docker Compose](#chapter-12-docker-compose) | [Docker Swarm](#chapter-15-docker-swarm)

### 🇮🇳 Hindi Explanation
Container ke andar ka data **ephemeral** hota hai — container delete karo toh data bhi chala jaata hai. **Volumes** se data **persist** karo container lifecycle ke baad bhi.

**3 Storage Types:**

```
1. Named Volumes    → Docker manage karta hai
   /var/lib/docker/volumes/myvolume/_data

2. Bind Mounts      → Host path directly mount karo
   /home/user/myapp → /app (inside container)

3. tmpfs Mounts     → RAM mein (temporary, fast, secure)
   Memory → /tmp (inside container)
```

### Named Volumes — Complete Reference
```bash
# Volume management
docker volume create mydata
docker volume create --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.1.100,rw \
  --opt device=:/path/to/dir \
  nfs-volume

docker volume ls
docker volume ls -f dangling=true      # Unused volumes
docker volume inspect mydata
docker volume rm mydata
docker volume prune                    # Unused volumes delete
docker volume prune --force

# Volume ke saath container run karo
docker run -d \
  --name postgres \
  -v mydata:/var/lib/postgresql/data \
  postgres:15

# Read-only volume
docker run -v mydata:/data:ro nginx
# ro = read-only, rw = read-write (default)
```

### Bind Mounts — Complete Reference
```bash
# Host directory mount karo
docker run -d \
  -v /home/user/myapp:/app \
  -v $(pwd):/app \                     # Current directory
  node:18

# Read-only bind mount
docker run -v /host/config:/app/config:ro nginx

# Multiple mounts
docker run -d \
  -v $(pwd)/src:/app/src \
  -v $(pwd)/config:/app/config:ro \
  -v mydata:/app/data \
  myapp

# Bind mount shorthand (newer syntax)
docker run -d \
  --mount type=bind,source=$(pwd),target=/app \
  node:18

# Named volume (newer syntax)
docker run -d \
  --mount type=volume,source=mydata,target=/data \
  postgres:15
```

### tmpfs Mounts
```bash
docker run -d \
  --tmpfs /tmp:rw,size=100m,mode=1777 \
  nginx

# --mount syntax
docker run -d \
  --mount type=tmpfs,destination=/tmp,tmpfs-size=100m \
  nginx
```

### Volume Backup & Restore
```bash
# Volume backup karo
docker run --rm \
  -v mydata:/source:ro \
  -v $(pwd):/backup \
  alpine tar czf /backup/mydata-backup.tar.gz -C /source .

# Volume restore karo
docker run --rm \
  -v mydata:/target \
  -v $(pwd):/backup \
  alpine tar xzf /backup/mydata-backup.tar.gz -C /target
```

### 🎯 Interview Answer
**Q: What are Docker Volumes and when to use each type?**

Containers have ephemeral storage by default. **Named Volumes**: managed by Docker, stored at `/var/lib/docker/volumes/`, survive container deletion, easily backed up, best for production databases. **Bind Mounts**: map a specific host path to container, perfect for development (hot reload), but path must exist on host and is less portable. **tmpfs Mounts**: stored in RAM, never persisted to disk, best for sensitive temporary data (secrets, tokens) or high-performance temporary storage. Named volumes are recommended for production. Bind mounts for development with live code reload.

---

## Chapter 11: Docker Networking

> 🔗 Related: [Docker Compose](#chapter-12-docker-compose) | [Docker Swarm](#chapter-15-docker-swarm)

### 🇮🇳 Hindi Explanation
Docker containers ko ek dusre se aur bahar ki duniya se communicate karna hota hai. Docker **CNM (Container Network Model)** use karta hai.

**5 Network Drivers:**
- **bridge** — Default, same host containers ke beech
- **host** — Container host ka network use karta hai
- **overlay** — Multiple hosts (Swarm/K8s)
- **macvlan** — Container ko apna MAC address
- **none** — No networking
- **ipvlan** — L2/L3 mode IP address assignment

```
Default bridge network:
┌─────────────────────────────────────────┐
│           docker0 (172.17.0.1)          │
│    ┌──────────────┬──────────────┐       │
│    │  Container1  │  Container2  │       │
│    │ 172.17.0.2   │ 172.17.0.3   │       │
│    └──────────────┴──────────────┘       │
└─────────────────────────────────────────┘

Custom bridge network (DNS resolution!):
┌─────────────────────────────────────────┐
│        mynetwork (172.20.0.1)           │
│  ┌─────────┐  ┌─────────┐               │
│  │  web    │  │   db    │               │
│  │(myapp)  │  │(postgres│               │
│  │ping db ✓│  │         │               │
│  └─────────┘  └─────────┘               │
└─────────────────────────────────────────┘
Note: Default bridge mein DNS nahi milta!
```

### Network Commands — Complete Reference
```bash
# ============================================
# NETWORK MANAGEMENT
# ============================================
docker network ls
docker network inspect bridge
docker network inspect --format='{{range .Containers}}{{.Name}} {{.IPv4Address}}{{"\n"}}{{end}}' bridge

# Custom bridge network create karo
docker network create mynet
docker network create \
  --driver bridge \
  --subnet 172.20.0.0/16 \
  --gateway 172.20.0.1 \
  --ip-range 172.20.240.0/20 \
  --opt "com.docker.network.bridge.name"="my-bridge" \
  mynetwork

# Overlay network (Swarm ke liye)
docker network create --driver overlay --attachable myoverlay

# Macvlan network
docker network create \
  --driver macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 \
  macvlan-net

docker network rm mynet
docker network prune                   # Unused networks delete

# ============================================
# CONTAINER NETWORKING
# ============================================
# Network ke saath run karo
docker run -d --name web --network mynet nginx
docker run -d --name db --network mynet postgres
# Ab 'web' 'db' naam se access kar sakta hai (DNS resolution)

# Multiple networks
docker run -d --name app \
  --network frontend \
  myapp

docker network connect backend app     # Dusra network connect
docker network disconnect frontend app # Network disconnect

# Specific IP assign karo
docker run -d --name web \
  --network mynet \
  --ip 172.20.0.10 \
  nginx

# ============================================
# PORT PUBLISHING
# ============================================
docker run -p 8080:80 nginx            # All interfaces
docker run -p 127.0.0.1:8080:80 nginx  # Localhost only
docker run -p 0.0.0.0:8080:80 nginx    # All interfaces (explicit)
docker run -P nginx                    # Random ports (EXPOSE ke)
docker port container_id               # Port mappings dekho

# ============================================
# HOST NETWORK
# ============================================
docker run --network host nginx
# Container host ka network directly use karta hai
# Port mapping nahi chahiye
# Linux only (Mac/Windows pe kaam nahi karta fully)

# ============================================
# NONE NETWORK
# ============================================
docker run --network none myapp
# Completely isolated — no network access
```

### Container Communication Patterns
```bash
# Same network — naam se communicate karo
docker run -d --name redis --network mynet redis
docker run -d --name app --network mynet \
  -e REDIS_URL=redis://redis:6379 \
  myapp
# 'redis' hostname = container name = DNS entry

# Link (legacy, avoid karo)
docker run -d --name db postgres
docker run -d --link db:database myapp  # db container ko 'database' naam se

# DNS search domain
# Container ke /etc/resolv.conf mein hota hai:
# search mynet
# Isliye 'db' se 'db.mynet' resolve hota hai
```

### 🎯 Interview Answer
**Q: What is the difference between bridge and overlay network in Docker?**

**Bridge network** works on a single Docker host — containers on the same custom bridge network can communicate by container name (Docker provides built-in DNS). Default bridge doesn't have DNS resolution. **Overlay network** works across multiple Docker hosts in a Swarm cluster — it creates a distributed network where containers on different physical machines can communicate as if on the same network using VXLAN tunneling. Overlay requires Swarm mode or `--attachable` flag. Always use custom bridge (not default) for production single-host deployments to get DNS-based service discovery.

---

## Chapter 12: Docker Compose

> 🔗 Related: [Docker Volumes](#chapter-10-docker-volumes) | [Docker Networking](#chapter-11-docker-networking) | [Docker Swarm](#chapter-15-docker-swarm)

### 🇮🇳 Hindi Explanation
**Docker Compose** multiple containers ko ek YAML file mein define karne deta hai. Ek command se poori application start/stop karo. Development aur testing ke liye ideal.

**Compose v2** ab default hai — `docker compose` (bina hyphen). Compose v1 (`docker-compose`) deprecated ho gaya.

### Complete docker-compose.yml
```yaml
version: '3.8'                         # Compose file version

# ============================================
# SERVICES
# ============================================
services:

  # ---- Frontend ----
  frontend:
    build:
      context: ./frontend              # Build context
      dockerfile: Dockerfile.dev       # Custom Dockerfile
      args:
        - REACT_APP_API_URL=http://api:3000
      target: development              # Multi-stage target
      cache_from:
        - myapp-frontend:cache
    image: myapp-frontend:latest
    container_name: frontend
    hostname: frontend
    restart: unless-stopped            # no | always | on-failure | unless-stopped
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
    env_file:
      - .env
      - .env.local
    volumes:
      - ./frontend/src:/app/src        # Hot reload ke liye
      - /app/node_modules              # Anonymous volume (host se override mat karo)
    networks:
      - frontend-net
    depends_on:
      api:
        condition: service_healthy     # service_started | service_healthy | service_completed_successfully
    profiles:
      - dev                            # docker compose --profile dev up

  # ---- API ----
  api:
    build: ./backend
    image: myapp-api:latest
    restart: unless-stopped
    ports:
      - "8080:3000"
    environment:
      NODE_ENV: production
      DB_HOST: postgres
      DB_PORT: 5432
      DB_NAME: mydb
      DB_USER: myuser
      REDIS_URL: redis://cache:6379
    secrets:
      - db_password
      - jwt_secret
    volumes:
      - api_logs:/app/logs
    networks:
      - frontend-net
      - backend-net
    depends_on:
      postgres:
        condition: service_healthy
      cache:
        condition: service_started
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M

  # ---- PostgreSQL ----
  postgres:
    image: postgres:15-alpine
    restart: unless-stopped
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
    secrets:
      - db_password
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./db/init:/docker-entrypoint-initdb.d:ro
    networks:
      - backend-net
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U myuser -d mydb"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s
    ports:
      - "5432:5432"                    # Dev mein expose karo, prod mein mat karo

  # ---- Redis Cache ----
  cache:
    image: redis:7-alpine
    restart: unless-stopped
    command: >
      redis-server
      --requirepass ${REDIS_PASSWORD}
      --maxmemory 256mb
      --maxmemory-policy allkeys-lru
      --appendonly yes
    volumes:
      - redis_data:/data
    networks:
      - backend-net
    healthcheck:
      test: ["CMD", "redis-cli", "--no-auth-warning",
             "-a", "${REDIS_PASSWORD}", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

  # ---- Nginx Reverse Proxy ----
  nginx:
    image: nginx:1.25-alpine
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./ssl:/etc/ssl/certs:ro
      - nginx_logs:/var/log/nginx
    networks:
      - frontend-net
    depends_on:
      - frontend
      - api

# ============================================
# VOLUMES
# ============================================
volumes:
  postgres_data:
    driver: local
  redis_data:
    driver: local
  api_logs:
    driver: local
  nginx_logs:
    driver: local

# ============================================
# NETWORKS
# ============================================
networks:
  frontend-net:
    driver: bridge
  backend-net:
    driver: bridge
    internal: true                     # Bahar se accessible nahi

# ============================================
# SECRETS
# ============================================
secrets:
  db_password:
    file: ./secrets/db_password.txt
  jwt_secret:
    environment: JWT_SECRET            # Environment variable se
```

### Docker Compose Commands — Complete
```bash
# ============================================
# START & STOP
# ============================================
docker compose up                      # Foreground
docker compose up -d                   # Background (detached)
docker compose up --build              # Rebuild images
docker compose up --build --force-recreate  # Force recreate
docker compose up api postgres         # Specific services
docker compose up --scale api=3        # Multiple instances
docker compose --profile dev up        # Profile ke saath

docker compose down                    # Stop + containers remove
docker compose down -v                 # Volumes bhi remove
docker compose down --rmi all          # Images bhi remove
docker compose down --remove-orphans   # Orphan containers remove

docker compose stop                    # Sirf stop (containers keep)
docker compose start                   # Start stopped services
docker compose restart api             # Specific service restart
docker compose pause api
docker compose unpause api

# ============================================
# STATUS & LOGS
# ============================================
docker compose ps                      # Services status
docker compose ps -a                   # All (stopped bhi)
docker compose ls                      # All compose projects

docker compose logs                    # Saari services ke logs
docker compose logs -f                 # Live
docker compose logs -f api postgres    # Specific services
docker compose logs --tail=100 api     # Last 100 lines
docker compose logs -t api             # Timestamps ke saath

# ============================================
# EXEC & RUN
# ============================================
docker compose exec api bash           # Running container mein
docker compose exec -u root api bash
docker compose exec postgres psql -U myuser mydb

docker compose run api npm test        # New container mein command
docker compose run --rm api npm run migrate  # Run + auto remove

# ============================================
# BUILD
# ============================================
docker compose build                   # All services
docker compose build api               # Specific service
docker compose build --no-cache        # Cache ke bina
docker compose build --pull            # Base images fresh pull

# ============================================
# OTHER
# ============================================
docker compose pull                    # Images pull karo
docker compose push                    # Images push karo
docker compose config                  # Parsed config dekho
docker compose config --services       # Service names
docker compose events                  # Real-time events
docker compose top                     # Running processes
docker compose port api 3000           # Published port dekho
docker compose images                  # Used images
docker compose kill api                # SIGKILL
docker compose rm api                  # Stopped containers remove
```

### Override Files
```yaml
# docker-compose.yml (base)
services:
  api:
    image: myapp:latest
    environment:
      - NODE_ENV=production

# docker-compose.override.yml (automatically loaded)
services:
  api:
    build: .
    volumes:
      - .:/app
    environment:
      - NODE_ENV=development

# docker-compose.prod.yml (explicitly specify karo)
# docker compose -f docker-compose.yml -f docker-compose.prod.yml up
```

---

## Chapter 13: Docker Hub

> 🔗 Related: [Docker Images](#chapter-5-docker-images--complete-guide) | [Private Registry](#chapter-14-docker-private-registry)

### 🇮🇳 Hindi Explanation
**Docker Hub** world ka largest container registry hai. Official images, community images, aur private repositories. Free account mein 1 private repo milta hai.

```bash
# ============================================
# DOCKER HUB OPERATIONS
# ============================================
docker login                           # Docker Hub login
docker login -u username               # Username ke saath
docker login --password-stdin          # stdin se password (CI/CD ke liye)
echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin

docker logout
docker logout registry.example.com

# Search
docker search nginx
docker search --filter=is-official=true node
docker search --filter=is-automated=true myapp
docker search --filter=stars=100 python
docker search --limit=10 ubuntu
docker search --format "table {{.Name}}\t{{.Stars}}\t{{.Official}}" nginx

# Tag & Push
docker tag myapp:latest username/myapp:latest
docker tag myapp:latest username/myapp:v1.0.0
docker push username/myapp:latest
docker push username/myapp:v1.0.0
docker push username/myapp            # All tags

# Pull
docker pull username/myapp:latest
docker pull --all-tags username/myapp  # All tags pull

# Hub API (direct)
# List tags:
curl https://hub.docker.com/v2/repositories/library/nginx/tags/?page_size=10

# ============================================
# DOCKER HUB AUTOMATED BUILDS (Webhooks)
# ============================================
# GitHub/GitLab se connected karo
# Har push pe automatically build hota hai

# ============================================
# ORGANIZATIONS & TEAMS
# ============================================
# docker login as org member
# docker push myorg/myapp:latest
```

---

## Chapter 14: Docker Private Registry

> 🔗 Related: [Docker Hub](#chapter-13-docker-hub) | [Docker Security](#chapter-20-docker-security--content-trust)

### 🇮🇳 Hindi Explanation
Company ki private images, internal tools, ya compliance requirements ke liye private registry use karte hain.

**Options:**
- **Docker Registry v2** — Self-hosted (open source)
- **Harbor** — Enterprise grade (scanning, RBAC, replication)
- **AWS ECR** — AWS managed
- **GCP Artifact Registry** — GCP managed
- **Azure Container Registry** — Azure managed
- **Nexus Repository** — Multi-format

```bash
# ============================================
# BASIC PRIVATE REGISTRY
# ============================================
docker run -d \
  -p 5000:5000 \
  --restart=always \
  --name registry \
  -v /opt/registry-data:/var/lib/registry \
  registry:2

# Use karo
docker tag myapp:latest localhost:5000/myapp:latest
docker push localhost:5000/myapp:latest
docker pull localhost:5000/myapp:latest

# Registry mein kya hai?
curl http://localhost:5000/v2/_catalog
curl http://localhost:5000/v2/myapp/tags/list

# ============================================
# SECURE REGISTRY (TLS + Authentication)
# ============================================
# Step 1: Certificate banao
mkdir -p /opt/registry/{certs,auth,data}

openssl req -newkey rsa:4096 -nodes -sha256 \
  -keyout /opt/registry/certs/domain.key \
  -x509 -days 365 \
  -out /opt/registry/certs/domain.crt \
  -subj "/C=IN/ST=MH/L=Mumbai/O=Company/CN=myregistry.company.com"

# Step 2: Basic auth create karo
docker run --entrypoint htpasswd httpd:2 \
  -Bbn admin strongpassword123 > /opt/registry/auth/htpasswd
docker run --entrypoint htpasswd httpd:2 \
  -Bbn developer devpassword >> /opt/registry/auth/htpasswd

# Step 3: Secure registry run karo
docker run -d \
  -p 443:5000 \
  --restart=always \
  --name secure-registry \
  -v /opt/registry/data:/var/lib/registry \
  -v /opt/registry/certs:/certs \
  -v /opt/registry/auth:/auth \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -e REGISTRY_AUTH=htpasswd \
  -e "REGISTRY_AUTH_HTPASSWD_REALM=Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  -e REGISTRY_STORAGE_DELETE_ENABLED=true \
  registry:2

# Login to secure registry
docker login myregistry.company.com
docker tag myapp myregistry.company.com/myapp:v1.0
docker push myregistry.company.com/myapp:v1.0

# ============================================
# AWS ECR
# ============================================
# Login
aws ecr get-login-password --region us-east-1 | \
  docker login --username AWS \
  --password-stdin 123456789.dkr.ecr.us-east-1.amazonaws.com

# Create repository
aws ecr create-repository --repository-name myapp --region us-east-1

# Push
docker tag myapp:latest 123456789.dkr.ecr.us-east-1.amazonaws.com/myapp:latest
docker push 123456789.dkr.ecr.us-east-1.amazonaws.com/myapp:latest

# ============================================
# HARBOR (Enterprise Registry)
# ============================================
# Helm se install karo
helm repo add harbor https://helm.goharbor.io
helm install harbor harbor/harbor \
  --namespace harbor \
  --create-namespace \
  --set externalURL=https://harbor.company.com \
  --set expose.type=ingress \
  --set expose.ingress.host=harbor.company.com \
  --set harborAdminPassword=Harbor12345

# Features: Image scanning, RBAC, Replication, Webhooks, Audit logs
```

---

## Chapter 15: Docker Swarm

> 🔗 Related: [Docker Services](#chapter-16-docker-services) | [Docker Stacks](#chapter-17-docker-stacks) | [Docker Networking](#chapter-11-docker-networking)

### 🇮🇳 Hindi Explanation
**Docker Swarm** Docker ka native **container orchestration** tool hai. Multiple Docker hosts ek cluster ki tarah kaam karte hain.

```
Swarm Cluster Architecture:
┌─────────────────────────────────────────────────────┐
│                  MANAGER NODES                       │
│  ┌───────────┐  ┌───────────┐  ┌───────────┐        │
│  │ Manager 1 │  │ Manager 2 │  │ Manager 3 │        │
│  │ (Leader)  │  │ (Follower)│  │ (Follower)│        │
│  │  Raft DB  │◄►│  Raft DB  │◄►│  Raft DB  │        │
│  └─────┬─────┘  └─────┬─────┘  └─────┬─────┘        │
└────────┼──────────────┼──────────────┼───────────────┘
         │              │              │
┌────────▼──────────────▼──────────────▼───────────────┐
│                  WORKER NODES                         │
│  ┌───────────┐  ┌───────────┐  ┌───────────┐         │
│  │ Worker 1  │  │ Worker 2  │  │ Worker 3  │         │
│  │ [Task][T] │  │ [Task][T] │  │ [Task]    │         │
│  └───────────┘  └───────────┘  └───────────┘         │
└───────────────────────────────────────────────────────┘
```

```bash
# ============================================
# SWARM INITIALIZE
# ============================================
# Manager node pe
docker swarm init --advertise-addr 192.168.1.10
# Output: docker swarm join --token SWMTKN-1-xxx 192.168.1.10:2377

# Token dekho
docker swarm join-token worker         # Worker token
docker swarm join-token manager        # Manager token
docker swarm join-token --rotate worker # Token rotate karo

# Worker node pe
docker swarm join --token SWMTKN-1-xxx 192.168.1.10:2377

# Additional manager
docker swarm join --token SWMTKN-1-yyy 192.168.1.10:2377

# ============================================
# NODE MANAGEMENT
# ============================================
docker node ls                         # Saare nodes
docker node inspect worker-1
docker node inspect --pretty worker-1  # Human-readable
docker node promote worker-1           # Manager banana
docker node demote manager-2           # Worker banana
docker node update --availability drain worker-1     # Maintenance mode
docker node update --availability active worker-1    # Wapas active
docker node update --availability pause worker-1     # New tasks nahi
docker node update --label-add region=us-east worker-1
docker node rm worker-1                # Pehle drain karo

docker swarm leave                     # Node swarm chodh de
docker swarm leave --force             # Manager swarm chhode

# ============================================
# SWARM SECRETS
# ============================================
echo "supersecretpassword" | docker secret create db_password -
docker secret create ssl_cert ./certificate.crt
docker secret create config_file ./config.json

docker secret ls
docker secret inspect db_password
docker secret rm db_password           # Pehle service se hatao

# Service mein secret use karo
docker service create \
  --name postgres \
  --secret db_password \
  --secret source=ssl_cert,target=/etc/ssl/cert.pem,mode=0400 \
  -e POSTGRES_PASSWORD_FILE=/run/secrets/db_password \
  postgres:15
# Secret /run/secrets/secret_name pe mount hoti hai

# ============================================
# SWARM CONFIGS
# ============================================
docker config create nginx_config ./nginx.conf
docker config ls
docker config inspect nginx_config
docker config rm nginx_config

docker service create \
  --name nginx \
  --config source=nginx_config,target=/etc/nginx/nginx.conf,mode=0444 \
  nginx:alpine
```

---

## Chapter 16: Docker Services

> 🔗 Related: [Docker Swarm](#chapter-15-docker-swarm) | [Docker Stacks](#chapter-17-docker-stacks)

### 🇮🇳 Hindi Explanation
**Service** Swarm mein desired state define karta hai — kitni replicas, kaunsi image, network, etc. Swarm automatically ensure karta hai ki desired state maintain ho.

```bash
# ============================================
# SERVICE CREATE
# ============================================
docker service create \
  --name webserver \
  --image nginx:latest \
  --replicas 3 \
  --publish published=80,target=80 \
  --publish published=443,target=443,protocol=tcp \
  --network myoverlay \
  --constraint 'node.role==worker' \
  --constraint 'node.labels.region==us-east' \
  --constraint 'node.hostname!=worker-3' \
  --preference 'spread=node.id' \
  --env NODE_ENV=production \
  --env-file .env \
  --mount type=volume,source=webdata,target=/data \
  --mount type=bind,source=/host/config,target=/config,readonly \
  --secret db_password \
  --config nginx_config \
  --update-delay 10s \
  --update-parallelism 1 \
  --update-failure-action rollback \
  --update-order start-first \
  --rollback-delay 5s \
  --rollback-parallelism 1 \
  --restart-condition on-failure \
  --restart-delay 5s \
  --restart-max-attempts 3 \
  --restart-window 120s \
  --limit-cpu 0.5 \
  --limit-memory 512m \
  --reserve-cpu 0.25 \
  --reserve-memory 256m \
  --health-cmd "curl -f http://localhost/ || exit 1" \
  --health-interval 30s \
  --health-timeout 10s \
  --health-retries 3 \
  --log-driver json-file \
  --log-opt max-size=10m

# ============================================
# SERVICE MODES
# ============================================
# Replicated mode (default) — N replicas
docker service create --name web --replicas 3 nginx

# Global mode — Har node pe exactly 1
docker service create --name monitor --mode global \
  prom/node-exporter

# ============================================
# SERVICE MANAGEMENT
# ============================================
docker service ls                      # All services
docker service ps webserver            # Service tasks
docker service ps --no-trunc webserver # Full details
docker service inspect webserver
docker service inspect --pretty webserver
docker service logs webserver
docker service logs -f webserver

# ============================================
# SERVICE UPDATE (Rolling Update)
# ============================================
docker service update \
  --image nginx:1.25-alpine \
  --update-parallelism 2 \
  --update-delay 20s \
  --update-order start-first \
  webserver

docker service update --replicas 5 webserver
docker service scale webserver=5
docker service scale web=3 api=5 db=1  # Multiple ek saath

docker service update --force webserver  # Same image pe redeploy

# ============================================
# ROLLBACK
# ============================================
docker service rollback webserver      # Previous state
docker service update --rollback webserver

# ============================================
# REMOVE
# ============================================
docker service rm webserver
docker service rm web api db           # Multiple
```

### 🎯 Interview Answer
**Q: What is a Docker Service and how does rolling update work in Swarm?**

A Docker Service defines the desired state in a Swarm cluster — which image, how many replicas, what network, etc. Swarm manager continuously reconciles actual vs desired state. Rolling update: `--update-parallelism` controls how many tasks update simultaneously, `--update-delay` is the wait time between batches, `--update-order start-first` starts new container before stopping old one (zero downtime), `--update-failure-action rollback` auto-rollbacks on failure. This ensures minimal/zero downtime during updates.

---

## Chapter 17: Docker Stacks

> 🔗 Related: [Docker Swarm](#chapter-15-docker-swarm) | [Docker Compose](#chapter-12-docker-compose)

### 🇮🇳 Hindi Explanation
**Stack** multiple services ko ek unit ki tarah Swarm mein deploy karne ka tarika hai. Docker Compose file (v3) use karta hai.

```yaml
# docker-stack.yml
version: '3.8'
services:
  webapp:
    image: myapp:2.0
    ports:
      - "80:3000"
    networks:
      - webnet
    secrets:
      - db_password
    configs:
      - source: app_config
        target: /app/config.json
    deploy:
      mode: replicated
      replicas: 3
      placement:
        constraints:
          - node.role == worker
          - node.labels.disk == ssd
        preferences:
          - spread: node.id
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
      update_config:
        parallelism: 1
        delay: 10s
        order: start-first
        failure_action: rollback
        monitor: 30s
        max_failure_ratio: 0.3
      rollback_config:
        parallelism: 1
        delay: 5s
        failure_action: pause
        order: stop-first
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

  postgres:
    image: postgres:15-alpine
    networks:
      - backend
    secrets:
      - db_password
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    deploy:
      replicas: 1
      placement:
        constraints:
          - node.labels.db == true

networks:
  webnet:
    driver: overlay
    attachable: true
  backend:
    driver: overlay
    internal: true

volumes:
  postgres_data:
    driver: local

secrets:
  db_password:
    external: true                     # docker secret create se banaya

configs:
  app_config:
    external: true                     # docker config create se banaya
```

```bash
# ============================================
# STACK COMMANDS
# ============================================
docker stack deploy -c docker-stack.yml mystack
docker stack deploy -c base.yml -c prod.yml mystack  # Multiple files

docker stack ls                        # All stacks
docker stack services mystack          # Stack services
docker stack ps mystack                # Stack tasks
docker stack ps --no-trunc mystack
docker stack rm mystack                # Stack remove

# Stack update karo (redeploy with new config)
docker stack deploy -c docker-stack.yml mystack  # Same command!
```

---

## Chapter 18: Docker Machine & Context

> 🔗 Related: [Docker Swarm](#chapter-15-docker-swarm)

### Docker Machine (Legacy — Deprecated)
```bash
# Docker Machine (Historical reference — ab use mat karo)
docker-machine create --driver virtualbox myvm1
docker-machine create --driver amazonec2 \
  --amazonec2-region us-east-1 aws-node
docker-machine ls
docker-machine start myvm1
docker-machine stop myvm1
docker-machine ip myvm1
docker-machine ssh myvm1
eval $(docker-machine env myvm1)       # Remote Docker use karo
eval $(docker-machine env -u)          # Local pe wapas
docker-machine rm myvm1
```

### Docker Context (Modern Replacement)
```bash
# Context create karo
docker context create remote-server \
  --description "Production server" \
  --docker "host=ssh://user@192.168.1.100"

docker context create remote-tls \
  --docker "host=tcp://192.168.1.100:2376,\
  ca=/path/ca.pem,cert=/path/cert.pem,key=/path/key.pem"

# Context manage
docker context ls
docker context inspect remote-server
docker context use remote-server      # Switch to remote
docker context use default            # Wapas local
docker context rm remote-server
docker context export remote-server > remote.dockercontext  # Export
docker context import mycontext remote.dockercontext        # Import

# One-time context use
docker --context remote-server ps

# DOCKER_HOST environment variable
export DOCKER_HOST="ssh://user@192.168.1.100"
docker ps                              # Remote pe chalega
unset DOCKER_HOST
```

---

## Chapter 19: Docker Logging Drivers

> 🔗 Related: [Docker Best Practices](#chapter-24-docker-best-practices)

### 🇮🇳 Hindi Explanation
Containers ke logs ko alag-alag systems mein route karne ke liye **logging drivers** use karte hain. Production mein centralized logging zaroori hai.

```bash
# Available Drivers:
# json-file  — Default, local JSON files
# syslog     — Syslog server
# journald   — systemd journal (Linux)
# gelf       — Graylog Extended Log Format
# fluentd    — Fluentd / Fluent Bit
# awslogs    — AWS CloudWatch
# splunk     — Splunk
# gcplogs    — Google Cloud Logging
# logentries — Logentries service
# etwlogs    — Windows ETW (Windows only)
# none       — Logging disable karo

# ============================================
# JSON-FILE (Default)
# ============================================
docker run \
  --log-driver json-file \
  --log-opt max-size=10m \             # Max file size
  --log-opt max-file=3 \              # Max rotated files (total: 30MB)
  --log-opt compress=true \           # Compress rotated files
  --log-opt labels=env,app \          # Labels include karo
  --log-opt env=NODE_ENV \            # Env vars include karo
  nginx

# ============================================
# FLUENTD
# ============================================
docker run \
  --log-driver fluentd \
  --log-opt fluentd-address=localhost:24224 \
  --log-opt tag="myapp.{{.Name}}.{{.ID}}" \
  --log-opt fluentd-async=true \
  --log-opt fluentd-buffer-limit=8388608 \
  nginx

# ============================================
# AWS CLOUDWATCH
# ============================================
docker run \
  --log-driver awslogs \
  --log-opt awslogs-region=us-east-1 \
  --log-opt awslogs-group=/myapp/production \
  --log-opt awslogs-stream=api-server \
  --log-opt awslogs-create-group=true \
  --log-opt awslogs-multiline-pattern='^(ERROR|WARN|INFO)' \
  myapp

# ============================================
# SYSLOG
# ============================================
docker run \
  --log-driver syslog \
  --log-opt syslog-address=udp://192.168.1.100:514 \
  --log-opt syslog-facility=daemon \
  --log-opt tag="myapp" \
  nginx

# ============================================
# NONE (Logging disable)
# ============================================
docker run --log-driver none nginx
# docker logs command kaam nahi karega

# ============================================
# GLOBAL DEFAULT (/etc/docker/daemon.json)
# ============================================
```
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "20m",
    "max-file": "5",
    "compress": "true"
  }
}
```

### 🎯 Interview Answer
**Q: What logging drivers should you use in production Docker?**

For production: avoid default `json-file` for large-scale — it doesn't support log aggregation. Use **fluentd/fluent-bit** to ship logs to centralized systems (ELK, Loki), **awslogs** for CloudWatch on AWS, **gcplogs** for GCP, or **splunk** for enterprise. Always configure `max-size` and `max-file` on json-file to prevent disk exhaustion. Configure via `/etc/docker/daemon.json` for global defaults. In Kubernetes, usually skip Docker logging drivers and use DaemonSet-based log collection (Fluent Bit) instead.

---

## Chapter 20: Docker Security & Content Trust

> 🔗 Related: [Docker Best Practices](#chapter-24-docker-best-practices) | [Private Registry](#chapter-14-docker-private-registry)

### 🇮🇳 Hindi Explanation
Docker security kai layers mein hoti hai — image security, runtime security, network security, secret management.

```bash
# ============================================
# DOCKER CONTENT TRUST (Image Signing)
# ============================================
export DOCKER_CONTENT_TRUST=1          # Enable globally
docker push username/myapp:1.0         # Automatically sign
docker pull username/myapp:1.0         # Only signed images pull
docker trust inspect --pretty username/myapp

docker trust signer add \
  --key colleague.pub \
  colleague \
  username/myapp                        # Signer add karo

docker trust revoke username/myapp:1.0  # Trust revoke karo

export DOCKER_CONTENT_TRUST=0           # Disable

# ============================================
# IMAGE SCANNING
# ============================================
# Docker Scout
docker scout cves myapp:1.0
docker scout cves --format sarif myapp:1.0  # SARIF format
docker scout recommendations myapp:1.0
docker scout compare myapp:1.0 myapp:2.0
docker scout quickview myapp:1.0

# Trivy (most popular open-source)
trivy image myapp:1.0
trivy image --severity CRITICAL,HIGH myapp:1.0
trivy image --format json --output results.json myapp:1.0
trivy image --ignore-unfixed myapp:1.0
trivy fs .                             # Filesystem scan
trivy config .                         # Dockerfile/compose scan

# Snyk
snyk container test myapp:1.0
snyk container monitor myapp:1.0

# ============================================
# RUNTIME SECURITY
# ============================================
# Read-only root filesystem
docker run --read-only \
  -v /tmp:/tmp \                       # tmp write allow
  --tmpfs /run \                       # tmpfs for /run
  nginx

# Capabilities drop karo
docker run \
  --cap-drop ALL \
  --cap-add NET_BIND_SERVICE \         # Sirf specific cap add karo
  nginx

# Seccomp profile
docker run \
  --security-opt seccomp=/path/to/profile.json \
  nginx

# AppArmor profile
docker run \
  --security-opt apparmor=docker-nginx \
  nginx

# No new privileges
docker run \
  --security-opt no-new-privileges:true \
  nginx

# User namespace remapping
# /etc/docker/daemon.json mein:
# "userns-remap": "default"

# Non-root user
docker run -u 1000:1000 nginx

# ============================================
# SECRETS MANAGEMENT
# ============================================
# KABHI MAT karo:
# -e DATABASE_PASSWORD=mysecret        ← Inspect se dikh jaata hai
# ENV DATABASE_PASSWORD=mysecret       ← Image history mein rehta hai

# Sahi tarike:

# 1. Runtime environment variable (CI/CD inject karo)
docker run -e DATABASE_PASSWORD=$DB_PASS myapp

# 2. Docker Secrets (Swarm)
docker secret create db_pass ./secret.txt
docker service create --secret db_pass myapp

# 3. Volume mounted secrets
docker run -v /secure/secrets:/run/secrets:ro myapp

# 4. Vault / AWS Secrets Manager / GCP Secret Manager
# Application startup pe fetch karo

# ============================================
# NETWORK SECURITY
# ============================================
# Internal network (bahar accessible nahi)
docker network create --internal backend-net

# Published ports limit karo
docker run -p 127.0.0.1:8080:80 nginx  # Localhost only

# User-defined bridge (isolation)
docker network create myapp-net
docker run --network myapp-net app1
docker run --network myapp-net app2
# Default bridge use mat karo
```

---

## Chapter 21: Docker Resource Management

> 🔗 Related: [Docker Container Lifecycle](#chapter-6-docker-container-lifecycle) | [Best Practices](#chapter-24-docker-best-practices)

```bash
# ============================================
# CPU MANAGEMENT
# ============================================
docker run \
  --cpus="1.5" \                       # Max 1.5 CPU cores
  --cpu-shares=512 \                   # Relative weight (default 1024)
  --cpu-period=100000 \                # CFS period (microseconds)
  --cpu-quota=50000 \                  # CFS quota (50% of period)
  --cpuset-cpus="0,1" \               # Specific CPU cores
  --cpuset-mems="0" \                 # NUMA node
  nginx

# ============================================
# MEMORY MANAGEMENT
# ============================================
docker run \
  --memory="512m" \                    # Hard limit (OOMKilled if exceeded)
  --memory-swap="1g" \                 # RAM + Swap total (1g-512m=512m swap)
  --memory-swap="-1" \                 # Unlimited swap
  --memory-reservation="256m" \       # Soft limit (eviction hint)
  --memory-swappiness=60 \            # Swap tendency (0-100)
  --kernel-memory="256m" \            # Kernel memory limit
  --oom-kill-disable \                # OOM killer disable (dangerous!)
  --oom-score-adj=-500 \             # OOM kill priority
  nginx

# ============================================
# COMBINED PRODUCTION EXAMPLE
# ============================================
docker run -d \
  --name api-server \
  --cpus="2" \
  --cpu-shares=1024 \
  --memory="1g" \
  --memory-swap="2g" \
  --memory-reservation="512m" \
  --restart=unless-stopped \
  myapp:latest

# ============================================
# MONITORING
# ============================================
docker stats                           # Live all containers
docker stats --no-stream               # One-time
docker stats --format \
  "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.MemPerc}}\t{{.NetIO}}\t{{.BlockIO}}"

docker top container_id                # Processes
docker top container_id aux

# Update running container
docker update \
  --cpus="2" \
  --memory="2g" \
  --restart=unless-stopped \
  container_id

# ============================================
# ULIMITS
# ============================================
docker run \
  --ulimit nofile=1024:1024 \          # Open files limit
  --ulimit nproc=3 \                   # Max processes
  --ulimit stack=67108864 \            # Stack size
  nginx

# Global default (/etc/docker/daemon.json)
# "default-ulimits": { "nofile": { "Hard": 64000, "Soft": 64000 } }
```

---

## Chapter 22: Docker in CI/CD

> 🔗 Related: [Dockerfile](#chapter-7-dockerfile--all-instructions) | [Private Registry](#chapter-14-docker-private-registry) | [Multi-Stage Builds](#chapter-8-multi-stage-builds)

### 🇮🇳 Hindi Explanation
CI/CD pipeline mein Docker use karke consistent, reproducible builds aur deployments ho sakte hain.

### GitHub Actions
```yaml
# .github/workflows/docker.yml
name: Docker Build & Push

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Login to Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: |
            ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=ref,event=branch
            type=ref,event=pr
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
            type=sha

      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          platforms: linux/amd64,linux/arm64
          push: ${{ github.event_name != 'pull_request' }}
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
          cache-from: type=gha
          cache-to: type=gha,mode=max
          build-args: |
            BUILD_DATE=${{ github.event.head_commit.timestamp }}
            GIT_COMMIT=${{ github.sha }}

      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}:latest
          format: table
          exit-code: 1
          severity: CRITICAL,HIGH
```

### Makefile (Local CI)
```makefile
# Makefile
IMAGE_NAME = myapp
TAG ?= latest
REGISTRY = myregistry.company.com

.PHONY: build test push deploy clean

build:
	docker build \
	  --build-arg BUILD_DATE=$$(date -u +"%Y-%m-%dT%H:%M:%SZ") \
	  --build-arg GIT_COMMIT=$$(git rev-parse --short HEAD) \
	  -t $(IMAGE_NAME):$(TAG) \
	  -t $(IMAGE_NAME):$$(git rev-parse --short HEAD) \
	  .

test:
	docker run --rm $(IMAGE_NAME):$(TAG) npm test

lint:
	docker run --rm -v $(PWD):/app $(IMAGE_NAME):$(TAG) npm run lint

push: build
	docker tag $(IMAGE_NAME):$(TAG) $(REGISTRY)/$(IMAGE_NAME):$(TAG)
	docker push $(REGISTRY)/$(IMAGE_NAME):$(TAG)

deploy:
	docker stack deploy -c docker-stack.yml myapp

clean:
	docker image prune -f
	docker container prune -f
```

---

## Chapter 23: Docker Troubleshooting

> 🔗 Related: [Container Lifecycle](#chapter-6-docker-container-lifecycle)

```bash
# ============================================
# COMMON ISSUES & DEBUG
# ============================================

# --- Container nahi start ho raha ---
docker logs container_id               # Error message
docker inspect container_id | python3 -m json.tool
docker inspect --format='{{.State}}' container_id
docker events --filter container=myapp --since=1h

# --- ImagePullBackOff ---
docker pull myimage:tag               # Manually pull karo
docker inspect myimage:tag            # Image exists?
# Registry credentials check karo
cat ~/.docker/config.json

# --- CrashLoopBackOff ---
docker logs container_id --previous   # Previous crash ke logs
docker run -it --entrypoint sh myimage  # Shell se debug karo

# --- Port already in use ---
sudo lsof -i :8080 | grep LISTEN
sudo ss -tlnp | grep 8080
sudo fuser -k 8080/tcp                # Process kill karo

# --- No space left on device ---
docker system df                       # Disk usage
docker system df -v                    # Detailed
docker system prune -a --volumes       # Sab cleanup
df -h /var/lib/docker                  # Docker directory disk usage

# --- Permission denied ---
ls -la /var/run/docker.sock
sudo chmod 666 /var/run/docker.sock    # Temporary fix
sudo usermod -aG docker $USER          # Permanent fix
newgrp docker

# --- Container network connectivity ---
docker exec container_id ping 8.8.8.8
docker exec container_id curl -v http://other-container:port
docker exec container_id nslookup other-container
docker exec container_id cat /etc/resolv.conf
docker exec container_id netstat -tlnp

# --- Memory / OOM issues ---
docker inspect container_id | grep -i oom
docker stats --no-stream container_id
dmesg | grep -i "out of memory"

# ============================================
# DEBUGGING TOOLS
# ============================================
# Lightweight debug container
docker run -it --rm \
  --network container:myapp \         # Same network as myapp
  --pid container:myapp \             # Same PID namespace
  nicolaka/netshoot                   # Network debugging tools

# nsenter (direct namespace access)
docker inspect --format='{{.State.Pid}}' container_id
sudo nsenter -t <PID> -n ip addr       # Container network

# ============================================
# USEFUL DEBUG COMMANDS
# ============================================
docker inspect container_id
docker system info
docker system events --since="10m"
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemPerc}}"
journalctl -u docker.service -f        # Docker daemon logs
sudo journalctl -u docker.service --since="1 hour ago"
```

---

## Chapter 24: Docker Best Practices

> 🔗 Related: [Dockerfile](#chapter-7-dockerfile--all-instructions) | [Security](#chapter-20-docker-security--content-trust)

```dockerfile
# ============================================
# DOCKERFILE BEST PRACTICES
# ============================================

# ✅ 1. Specific base image tags use karo (never latest)
FROM node:18.19.0-alpine3.19   # ✅ Pinned version
# FROM node:latest             # ❌ Unpredictable

# ✅ 2. Minimal base images
FROM node:18-alpine            # ✅ ~50MB
# FROM node:18                 # ❌ ~900MB
# FROM ubuntu + node install   # ❌ Even bigger

# ✅ 3. Non-root user (Security!)
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser
# FROM node:18-alpine  # 'node' user already exists
# USER node            # ✅ Ready-made

# ✅ 4. Layer optimization
# ❌ Bad (too many layers)
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y vim
RUN rm -rf /var/lib/apt/lists/*

# ✅ Good (one layer, cleanup included)
RUN apt-get update \
    && apt-get install -y --no-install-recommends curl vim \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

# ✅ 5. Build cache optimize karo
COPY package*.json ./          # ✅ Dependencies pehle
RUN npm ci                     # Ye cache hoga
COPY . .                       # Source baad mein
RUN npm run build

# ✅ 6. .dockerignore use karo
# (node_modules, .git, .env, *.log, etc.)

# ✅ 7. Multi-stage builds (production mein)

# ✅ 8. HEALTHCHECK add karo
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
  CMD wget -qO- http://localhost:3000/health || exit 1

# ✅ 9. Secrets kab
