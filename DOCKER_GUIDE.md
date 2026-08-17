# Docker Guide

## What Docker Is
Docker is a platform for building, shipping, and running applications inside **containers** — lightweight, isolated environments that package code with all its dependencies (libraries, runtime, system tools) so it runs the same way anywhere: your laptop, a teammate's machine, or a production server.

## Containers vs Virtual Machines
- **VMs** virtualize hardware — each VM has its own full OS, which is heavy (GBs, slow to boot).
- **Containers** virtualize the OS — they share the host's kernel but isolate processes, filesystem, and network. Much lighter (MBs, start in seconds).

## Core Concepts

**Image** — A read-only template/blueprint (built from a Dockerfile) containing app code + dependencies. Images are made of layers, each corresponding to a Dockerfile instruction, and layers are cached/reused for faster builds.

**Container** — A running (or stopped) instance of an image. You can run many containers from one image.

**Dockerfile** — Text file with instructions to build an image.

**Registry** — Where images are stored/shared (Docker Hub is the default public one; you can also use private registries like GitHub Container Registry, AWS ECR, etc.).

**Volumes** — Persistent storage that lives outside the container's writable layer, so data survives container restarts/removal.

**Networks** — Let containers communicate with each other and the outside world (bridge, host, overlay networks, etc.).

## Sample Dockerfile (Node.js)

```dockerfile
# Base image
FROM node:20-alpine

# Set working directory inside container
WORKDIR /app

# Copy dependency files first (for layer caching)
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy rest of the app
COPY . .

# Expose port
EXPOSE 3000

# Command to run the app
CMD ["node", "server.js"]
```

## Sample Dockerfile (Python)

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["python", "app.py"]
```

## Key Dockerfile Instructions

| Instruction | Purpose |
|---|---|
| `FROM` | Base image to build on |
| `WORKDIR` | Sets working directory |
| `COPY` / `ADD` | Copies files into image |
| `RUN` | Executes commands during build |
| `ENV` | Sets environment variables |
| `EXPOSE` | Documents which port the app uses |
| `CMD` | Default command when container starts |
| `ENTRYPOINT` | Fixed command (harder to override than CMD) |

## .dockerignore Example

```
node_modules
.git
.env
*.log
__pycache__
```

## Common Docker Commands

| Command | Purpose |
|---|---|
| `docker build -t name .` | Build image from Dockerfile |
| `docker run -p 8080:80 name` | Run container, map ports |
| `docker ps` | List running containers |
| `docker ps -a` | List all containers (incl. stopped) |
| `docker images` | List local images |
| `docker stop <id>` | Stop a container |
| `docker rm <id>` | Remove a container |
| `docker rmi <id>` | Remove an image |
| `docker exec -it <id> bash` | Open a shell inside a running container |
| `docker logs <id>` | View container logs |
| `docker pull/push` | Download/upload images from a registry |

## Docker Compose

For multi-container apps (e.g., app + database + cache), `docker-compose.yml` lets you define and run them together:

```yaml
services:
  web:
    build: .
    ports:
      - "3000:3000"
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: example
```

Run with:
```
docker compose up
```

## Why It Matters
- **Consistency**: "works on my machine" problems mostly disappear.
- **Isolation**: apps and their dependencies don't conflict with each other.
- **Portability**: images run identically across environments.
- **Scalability**: pairs naturally with orchestration tools like Kubernetes for scaling containers across servers.
- **Efficiency**: faster startup and lower overhead than VMs, better resource utilization.

## Best Practices
- Use small base images (`alpine`, `slim` variants) to reduce size and attack surface.
- Don't run containers as root unless necessary — add a non-root `USER` in the Dockerfile.
- Use multi-stage builds to keep final images lean (build in one stage, copy only the output into a minimal final stage).
- Pin image versions instead of `latest` for reproducibility.
- Keep one process per container (single responsibility).
- Never bake secrets into images — use environment variables, secret managers, or Docker secrets.
- Regularly scan images for vulnerabilities (`docker scan`, Trivy, etc.).

## Steps to Upload to GitHub

1. Create the `Dockerfile` and `.dockerignore` in your project root.
2. Test it locally:
   ```
   docker build -t myapp .
   docker run -p 3000:3000 myapp
   ```
3. Commit and push:
   ```
   git add Dockerfile .dockerignore
   git commit -m "Add Dockerfile"
   git push origin main
   ```
