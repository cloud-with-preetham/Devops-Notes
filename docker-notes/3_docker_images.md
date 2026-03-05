# Docker Images

## What is a Docker Image?

A Docker image is a read-only template containing instructions for creating a container. It includes the application code, runtime, libraries, dependencies, and configuration files needed to run an application.

## Image Layers

Images are built in layers:

- Each instruction in a Dockerfile creates a new layer
- Layers are cached and reused for efficiency
- Only changed layers are rebuilt
- Layers are shared between images

```
┌─────────────────┐
│   Application   │  ← Top Layer
├─────────────────┤
│   Dependencies  │
├─────────────────┤
│     Runtime     │
├─────────────────┤
│   Base Image    │  ← Bottom Layer
└─────────────────┘
```

## Basic Image Commands

### Pulling Images

```bash
# Pull image from Docker Hub
docker pull <image>

# Pull specific version
docker pull nginx:1.21

# Pull from specific registry
docker pull gcr.io/project/image:tag
```

### Listing Images

```bash
# List all images
docker images

# List with specific format
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"

# List image IDs only
docker images -q

# Show dangling images
docker images -f "dangling=true"
```

### Removing Images

```bash
# Remove image
docker rmi <image_id|name>

# Remove multiple images
docker rmi image1 image2 image3

# Force remove
docker rmi -f <image_id>

# Remove all unused images
docker image prune

# Remove all images
docker rmi $(docker images -q)
```

### Image Inspection

```bash
# View image details
docker inspect <image>

# View image history
docker history <image>

# View image layers
docker history --no-trunc <image>
```

## Building Images

### From Dockerfile

```bash
# Build image
docker build -t myapp:1.0 .

# Build with custom Dockerfile
docker build -f Dockerfile.dev -t myapp:dev .

# Build without cache
docker build --no-cache -t myapp:1.0 .

# Build with build arguments
docker build --build-arg VERSION=1.0 -t myapp .
```

### From Container

```bash
# Create image from container
docker commit <container_id> myimage:tag

# With commit message
docker commit -m "Added feature" <container_id> myimage:tag
```

## Tagging Images

```bash
# Tag an image
docker tag <source_image> <target_image>:tag

# Example
docker tag myapp:latest myapp:1.0
docker tag myapp:latest username/myapp:latest
```

## Pushing Images

```bash
# Login to registry
docker login

# Push to Docker Hub
docker push username/myapp:tag

# Push to private registry
docker push registry.example.com/myapp:tag
```

## Saving and Loading Images

```bash
# Save image to tar file
docker save -o myapp.tar myapp:latest

# Save multiple images
docker save -o images.tar image1 image2

# Load image from tar file
docker load -i myapp.tar

# Export container as tar
docker export <container_id> > container.tar

# Import from tar
docker import container.tar myimage:tag
```

## Image Registries

### Docker Hub

```bash
# Search images
docker search nginx

# Pull official image
docker pull nginx

# Pull user image
docker pull username/myapp
```

### Private Registry

```bash
# Run local registry
docker run -d -p 5000:5000 --name registry registry:2

# Tag for private registry
docker tag myapp localhost:5000/myapp

# Push to private registry
docker push localhost:5000/myapp
```

## Image Naming Convention

```
[registry/][username/]repository[:tag]
```

Examples:

- `nginx` → Official image, latest tag
- `nginx:1.21` → Official image, specific version
- `username/myapp` → User image, latest tag
- `username/myapp:v1.0` → User image, specific tag
- `gcr.io/project/app:latest` → Private registry

## Multi-Architecture Images

```bash
# Build for multiple platforms
docker buildx build --platform linux/amd64,linux/arm64 -t myapp:latest .

# Inspect image architecture
docker inspect <image> | grep Architecture
```

## Image Optimization

### Reduce Image Size

1. **Use smaller base images**

   ```dockerfile
   FROM alpine:3.18
   ```

2. **Multi-stage builds**

   ```dockerfile
   FROM node:18 AS builder
   WORKDIR /app
   COPY . .
   RUN npm install && npm run build

   FROM nginx:alpine
   COPY --from=builder /app/dist /usr/share/nginx/html
   ```

3. **Combine RUN commands**

   ```dockerfile
   RUN apt-get update && \
       apt-get install -y package1 package2 && \
       apt-get clean && \
       rm -rf /var/lib/apt/lists/*
   ```

4. **Use .dockerignore**
   ```
   node_modules
   .git
   *.log
   ```

### Layer Caching

- Order Dockerfile instructions from least to most frequently changing
- Copy dependency files before source code
- Leverage build cache

```dockerfile
# Good - dependencies cached separately
COPY package.json package-lock.json ./
RUN npm install
COPY . .
```

## Image Security

### Best Practices

1. **Use official images**
2. **Scan for vulnerabilities**
   ```bash
   docker scan myapp:latest
   ```
3. **Don't store secrets in images**
4. **Run as non-root user**
   ```dockerfile
   USER nonroot
   ```
5. **Keep images updated**
6. **Use specific tags, not `latest`**

### Scanning Images

```bash
# Scan with Docker Scout
docker scout cves myapp:latest

# Scan with Trivy
trivy image myapp:latest
```

## Common Image Operations

### Cleanup

```bash
# Remove unused images
docker image prune

# Remove all unused data
docker system prune -a

# Remove images older than 24h
docker image prune -a --filter "until=24h"
```

### Filtering

```bash
# Filter by label
docker images --filter "label=version=1.0"

# Filter by name
docker images --filter "reference=nginx:*"

# Filter dangling images
docker images --filter "dangling=true"
```

## Image Formats

### OCI (Open Container Initiative)

- Standard format for container images
- Compatible across different container runtimes
- Docker images follow OCI specification

## Troubleshooting

### Image Pull Fails

```bash
# Check network connectivity
ping registry-1.docker.io

# Check authentication
docker login

# Use different registry mirror
docker pull mirror.gcr.io/library/nginx
```

### Image Too Large

- Use multi-stage builds
- Choose smaller base images (alpine)
- Remove unnecessary files
- Combine RUN commands

### Build Cache Issues

```bash
# Clear build cache
docker builder prune

# Build without cache
docker build --no-cache -t myapp .
```

## Quick Reference

| Command          | Description    |
| ---------------- | -------------- |
| `docker pull`    | Download image |
| `docker images`  | List images    |
| `docker rmi`     | Remove image   |
| `docker build`   | Build image    |
| `docker tag`     | Tag image      |
| `docker push`    | Upload image   |
| `docker save`    | Export image   |
| `docker load`    | Import image   |
| `docker inspect` | View details   |
| `docker history` | View layers    |

## Image vs Container

| Image                          | Container                |
| ------------------------------ | ------------------------ |
| Read-only template             | Running instance         |
| Blueprint                      | Actual application       |
| Stored on disk                 | Running in memory        |
| Can create multiple containers | Created from one image   |
| Immutable                      | Mutable (writable layer) |

---

**Related Topics**: Dockerfile, Docker Containers, Docker Registry, Multi-stage Builds
