# Docker Containers

## What is a Container?

A container is a running instance of a Docker image. It's an isolated, lightweight, executable package that includes everything needed to run an application: code, runtime, system tools, libraries, and settings.

## Container Lifecycle

```
Created → Running → Paused → Stopped → Removed
```

## Basic Container Commands

### Running Containers

```bash
# Run a container
docker run <image>

# Run with name
docker run --name mycontainer nginx

# Run in detached mode (background)
docker run -d nginx

# Run with port mapping
docker run -p 8080:80 nginx

# Run with environment variables
docker run -e ENV_VAR=value nginx

# Run interactively
docker run -it ubuntu bash

# Run with auto-remove after exit
docker run --rm nginx
```

### Listing Containers

```bash
# List running containers
docker ps

# List all containers (including stopped)
docker ps -a

# List with specific format
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"
```

### Container Management

```bash
# Start a stopped container
docker start <container_id|name>

# Stop a running container
docker stop <container_id|name>

# Restart a container
docker restart <container_id|name>

# Pause a container
docker pause <container_id|name>

# Unpause a container
docker unpause <container_id|name>

# Kill a container (force stop)
docker kill <container_id|name>

# Remove a container
docker rm <container_id|name>

# Remove a running container (force)
docker rm -f <container_id|name>

# Remove all stopped containers
docker container prune
```

### Container Inspection

```bash
# View container details
docker inspect <container_id|name>

# View container logs
docker logs <container_id|name>

# Follow logs in real-time
docker logs -f <container_id|name>

# View last N lines of logs
docker logs --tail 50 <container_id|name>

# View container resource usage
docker stats <container_id|name>

# View running processes in container
docker top <container_id|name>
```

### Executing Commands in Containers

```bash
# Execute command in running container
docker exec <container_id|name> <command>

# Execute interactively
docker exec -it <container_id|name> bash

# Execute as specific user
docker exec -u root <container_id|name> whoami
```

### Copying Files

```bash
# Copy from container to host
docker cp <container_id>:/path/in/container /path/on/host

# Copy from host to container
docker cp /path/on/host <container_id>:/path/in/container
```

## Container Run Options

### Resource Limits

```bash
# Limit memory
docker run -m 512m nginx

# Limit CPU
docker run --cpus="1.5" nginx

# Set CPU shares
docker run --cpu-shares=512 nginx
```

### Networking

```bash
# Use host network
docker run --network host nginx

# Use custom network
docker run --network mynetwork nginx

# Expose ports
docker run -p 8080:80 -p 443:443 nginx
```

### Volumes

```bash
# Mount volume
docker run -v myvolume:/data nginx

# Bind mount
docker run -v /host/path:/container/path nginx

# Read-only mount
docker run -v /host/path:/container/path:ro nginx
```

### Environment & Working Directory

```bash
# Set environment variables
docker run -e DB_HOST=localhost -e DB_PORT=5432 myapp

# Set working directory
docker run -w /app myapp

# Use env file
docker run --env-file .env myapp
```

## Container States

- **Created**: Container created but not started
- **Running**: Container is actively running
- **Paused**: Container processes are paused
- **Restarting**: Container is restarting
- **Exited**: Container has stopped
- **Dead**: Container is in a non-functional state

## Best Practices

1. **Use specific tags**: Avoid `latest` tag in production
2. **One process per container**: Follow single responsibility principle
3. **Use .dockerignore**: Exclude unnecessary files
4. **Don't run as root**: Use non-root users for security
5. **Clean up**: Remove unused containers regularly
6. **Use health checks**: Monitor container health
7. **Limit resources**: Set memory and CPU limits
8. **Use volumes**: For persistent data
9. **Log to stdout/stderr**: For proper log management
10. **Keep containers ephemeral**: Design for easy recreation

## Common Patterns

### Interactive Shell

```bash
docker run -it --rm ubuntu bash
```

### Background Service

```bash
docker run -d --name webserver -p 80:80 nginx
```

### Temporary Container

```bash
docker run --rm alpine echo "Hello World"
```

### Development Environment

```bash
docker run -it -v $(pwd):/app -w /app node:18 bash
```

## Troubleshooting

### Container Won't Start

```bash
# Check logs
docker logs <container_id>

# Inspect container
docker inspect <container_id>

# Check events
docker events
```

### Container Exits Immediately

- Check if the main process exits
- Review logs for errors
- Ensure proper CMD/ENTRYPOINT in image

### Performance Issues

```bash
# Monitor resources
docker stats

# Check running processes
docker top <container_id>
```

## Quick Reference

| Command          | Description                |
| ---------------- | -------------------------- |
| `docker run`     | Create and start container |
| `docker start`   | Start stopped container    |
| `docker stop`    | Stop running container     |
| `docker restart` | Restart container          |
| `docker rm`      | Remove container           |
| `docker ps`      | List containers            |
| `docker logs`    | View logs                  |
| `docker exec`    | Execute command            |
| `docker inspect` | View details               |
| `docker stats`   | Resource usage             |

---

**Related Topics**: Docker Images, Docker Volumes, Docker Networking, Dockerfile
