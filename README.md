# Project Name

Short description of what this project does.

## Prerequisites
- [Docker](https://www.docker.com/get-started) installed

## Build

```bash
docker build -t myapp .
```

## Run

```bash
docker run -p 3000:3000 myapp
```

App will be available at `http://localhost:3000`.

## Project Structure

```
.
├── Dockerfile
├── .dockerignore
└── README.md
```

## Dockerfile Reference

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

## .dockerignore

```
node_modules
.git
.env
*.log
__pycache__
```

## Useful Docker Commands

| Command | Purpose |
|---|---|
| `docker build -t name .` | Build image from Dockerfile |
| `docker run -p 8080:80 name` | Run container, map ports |
| `docker ps` | List running containers |
| `docker images` | List local images |
| `docker stop <id>` | Stop a container |
| `docker rm <id>` | Remove a container |
| `docker logs <id>` | View container logs |

## License
MIT


## 🐳 Docker Workflow

```text
Developer
   │
   ▼
Write Application Code
   │
   ▼
Create Dockerfile
   │
   ▼
Build Docker Image
   │
   │  docker build
   ▼
Docker Image
   │
   ▼
Run Container
   │
   │  docker run
   ▼
Docker Container
   │
   ▼
Application Runs
   │
   ▼
Test & Debug
   │
   ▼
Push Image to Docker Hub
   │
   │  docker push
   ▼
Docker Registry
   │
   ▼
Pull Image on Another Machine
   │
   │  docker pull
   ▼
Run Application Anywhere
