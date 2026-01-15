# Docker

## Installation 

```bash
docker version
docker info
docker run --rm hello-world #--rm flags removes container after running. Avoid taking too much disk space. 
```

## Core Workflow

```bash
# Pull an image
docker pull alpine:3.20

# Run a container (interactively, auto-delete on exit)
docker run --rm -it alpine:3.20

# List images/ containers
docker images
docker ps # running
docker ps -a # all (stopped too)

# Stop/remove containers
docker rm "container"
docker rmi "image"
```

## Dockerfile based Image

```Dockerfile
# Choose a base
FROM python:3.12-slim

# Set working directory
WORKDIR /app

# Copy Code
COPY app.py

# Expose port
EXPOSE 8000

# Default command
CMD ["python", "app.py"]
```

build and run 

```bash
docker build -t hello-docker:latest .
docker run --rm -p 8000:8000 hello-docker:latest
```

## Layer Caching & .dockerignore

```.dockerignore
#.dockeringore file
__pycache__/
*.log
.env
.git
```

## Add Dependencies Right Way

```dockerfile
FROM python:3.12-slim
WORKDIR /app

# Install OS packages (use --no-install-recommends on Debian/Ubuntu)
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
 && rm -rf /var/lib/apt/lists/*

# Copy and install deps first (better cache)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Then copy the rest
COPY . .
CMD ["python", "app.py"]
```

## Persist Data with Volumes

There are two ways to mount binds into docker containers. 
- Bind mount: host folder <-> container folder (great for dev)
- Named volume: managed by Docker (great for prod state)

```bash
# Bind mount current dir to /app in container
docker run --rm -it -v "$PWD":/app -w /app python:3.12-slim python app.py

# Named volume for a database
docker volume create pgdata
docker run -d --name pg -e POSTGRES_PASSWORD=pass -v pgdata:/var/lib/postgresql/data -p 5432:5432 postgres:16
```

## Networking between Containers

Docker creates and isolated bridge network per compose or else you can make one 

```bash
docker network create mynet
docker run -d --name redis --network mynet redis:7
docker run -it --rm --network mynet alpine:3.20 sh -c "apk add --no-cache redis && redis-cli -h redis PING"
```

## Docker Compose 

```yaml
services:
  web:
    build: .
    ports: ["8000:8000"]
    volumes: ["./:/app"]  # live-reload in dev if your app supports it
    depends_on: ["redis"]
  redis:
    image: redis:7
```

```bash
docker compose up --build
# stop with CTRL+C, or run detached:
docker compose up -d
docker compose logs -f web
docker compose down -v   # -v removes volumes (careful!)
```

## Environments and Secrets

```yaml
services:
  api:
    image: myorg/api:latest
    env_file: .env
    environment:
      LOG_LEVEL: info
      # Prefer secrets manager for production; for local:
      API_KEY: ${API_KEY}
```

```bash
API_KEY=supersecret
```

## Debugging Containers

```bash
# Running a shell in a container
docker exec -it <container> bash

# See logs
docker logs -f <container> 

# Inspect details (env, mounts, network)
docker inspect <container or image>

# Quick one-off debug container in same net ns:
docker run ==rm -it --network container:<container> alpine sh
```

## Best practices

- Start from minimal bases
- Use non-root
```DockerFile
RUN useradd -m appuser
USER appuser
```
- Keep layers small: Combine RUN lines, clean package caches
- Pin version: Verify checksums for downloads
- Read-only file system in prod: 
```yaml 
services:
  app:
    read_only: true
    tmpfs: ["/tmp"]
```
- Regularly rebuild for CVE(Common Vulnerability and Exposures) fixes


## Push & Pull from a Registry

```bash
docker login
docker tag hello-docker:latest yourname/hello-docker:1.0.0
docker push yourname/hello-docker:1.0.0
docker pull yourname/hello-docker:1.0.0
```

## CI/CD Patterns

- Build on a PR -> Run tests inside a container
- On main merge -> build, tag (`git sha`), push to registry
- Deploy by updating the image tag

## Common pitfalls

- App works on local and not work on container -> Missing deps, wrong working DIR and copy correctly. 
- Port not reachable -> bind `0.0.0.0` in the app, map with `-p host:container`
- File perms -> add a non-root user matching host UID/GID for bind mounts. 
- Huge Images -> Multi-stage builds, slim bases, `--no-cache-dir`, prune 

```bash
docker container prune # Remove all the stopped containers
docker system prune -f # Removes images as well, Not recommended. 
docker builder prune -f
```