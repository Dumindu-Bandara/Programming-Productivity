# 🐳 Docker Layers and How Docker Works

## 1. Overview
Docker packages applications and their dependencies into **containers**.  
Containers run from **images**, which are built from **layers** — stacked filesystem diffs representing each step in a `Dockerfile`.

---

## 2. What Are Docker Layers?

Each line in a Dockerfile creates a new **layer** — a read-only snapshot of filesystem changes.

Example:
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
CMD ["python", "app.py"]
```

Resulting layers:
1. Base OS → python:3.12-slim
2. Working directory → /app
3. Copy requirements.txt
4. Install dependencies
5. Copy source code
6. Default command
7. Each layer is immutable and cached for future builds.

## 3. Why layers matters? 

| Benefit | Description |
|----------|-------------|
| 🧠 Caching | Unchanged layers are reused between builds |
| ⚡ Speed | Faster rebuilds (Docker skips identical steps) |
| 📦 Sharing | Common base layers (e.g., Python, Alpine) reused across projects |
| 💾 Efficiency | Smaller image downloads and less disk space |

## 4. How Docker Builds an Image

Docker reads the Dockerfile top-to-bottom.
Executes each instruction in a temporary container.
Captures the filesystem diff (changes made).
Saves each diff as a read-only layer.
Combines all layers into a final image.
When you run a container, Docker adds a writable layer on top.


```pgsql
Container
 ├── Read/Write layer (temporary)
 ├── App code layer
 ├── Pip dependencies
 ├── Requirements.txt
 ├── Workdir
 └── Base image (Python 3.12-slim)
```

## 5. Key Components

| Component | Purpose |
|------------|----------|
| **Dockerfile** | Recipe for building an image |
| **Image** | Read-only stack of layers |
| **Container** | Running instance of an image (adds a writable layer) |
| **Docker Daemon (`dockerd`)** | Service managing images, containers, and networks |
| **Docker CLI** | User command-line interface |
| **Registry (e.g., Docker Hub)** | Cloud repository for images |

## 6. How Docker Works (Under the Hood)

```bash
docker run hello-world
```

1. Checks if the image exists locally.
2. Pulls it from Docker Hub if missing.
3. Creates a container (adds writable layer).
4. Runs the command defined in CMD or ENTRYPOINT.
5. Streams output to the terminal.
6. Removes the container if --rm flag is used.

## 7. Inspect Layers

```bash
docker history myimage:latest
```
