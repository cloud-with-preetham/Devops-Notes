# Docker Volumes

## What are Docker Volumes?

Volumes are the preferred mechanism for persisting data generated and used by Docker containers. Unlike bind mounts, volumes are completely managed by Docker and provide better portability and performance.

## Why Use Volumes?

- **Data Persistence**: Data survives container deletion
- **Data Sharing**: Share data between containers
- **Performance**: Better I/O performance than bind mounts
- **Backup**: Easier to backup and migrate
- **Decoupling**: Separate data from container lifecycle

## Types of Mounts

### 1. Volumes (Recommended)

Managed by Docker, stored in `/var/lib/docker/volumes/`

```bash
docker run -v myvolume:/data nginx
```

### 2. Bind Mounts

Direct mapping to host filesystem

```bash
docker run -v /host/path:/container/path nginx
```

### 3. tmpfs Mounts

Stored in host memory only (Linux)

```bash
docker run --tmpfs /app/temp nginx
```

## Volume Commands

### Creating Volumes

```bash
# Create a volume
docker volume create myvolume

# Create with driver options
docker volume create --driver local myvolume

# Create with labels
docker volume create --label env=prod myvolume
```

### Listing Volumes

```bash
# List all volumes
docker volume ls

# List with filter
docker volume ls --filter "dangling=true"

# List with custom format
docker volume ls --format "table {{.Name}}\t{{.Driver}}"
```

### Inspecting Volumes

```bash
# View volume details
docker volume inspect myvolume

# View multiple volumes
docker volume inspect vol1 vol2
```

### Removing Volumes

```bash
# Remove a volume
docker volume rm myvolume

# Remove multiple volumes
docker volume rm vol1 vol2 vol3

# Remove all unused volumes
docker volume prune

# Force remove
docker volume rm -f myvolume
```

## Using Volumes with Containers

### Named Volumes

```bash
# Create and mount volume
docker run -d -v mydata:/data nginx

# Mount multiple volumes
docker run -d -v vol1:/data1 -v vol2:/data2 nginx

# Read-only volume
docker run -d -v mydata:/data:ro nginx
```

### Anonymous Volumes

```bash
# Docker creates volume with random name
docker run -d -v /data nginx
```

### Bind Mounts

```bash
# Mount host directory
docker run -d -v /host/path:/container/path nginx

# Current directory
docker run -d -v $(pwd):/app nginx

# Read-only bind mount
docker run -d -v /host/path:/container/path:ro nginx
```

### Mount Flag (Newer Syntax)

```bash
# Named volume
docker run -d --mount source=myvolume,target=/data nginx

# Bind mount
docker run -d --mount type=bind,source=/host/path,target=/data nginx

# Read-only
docker run -d --mount source=myvolume,target=/data,readonly nginx
```

## Volume Drivers

### Local Driver (Default)

```bash
docker volume create --driver local myvolume
```

### Third-Party Drivers

- **NFS**: Network File System
- **AWS EBS**: Amazon Elastic Block Store
- **Azure File Storage**
- **GlusterFS**: Distributed filesystem

```bash
# NFS example
docker volume create --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.1.1,rw \
  --opt device=:/path/to/dir \
  nfs-volume
```

## Volume in Dockerfile

```dockerfile
# Declare volume
VOLUME /data

# Multiple volumes
VOLUME ["/data", "/logs"]
```

Note: Dockerfile VOLUME creates anonymous volumes at runtime

## Sharing Data Between Containers

### Using Named Volumes

```bash
# Container 1 writes data
docker run -d --name writer -v shared:/data alpine sh -c "echo 'Hello' > /data/file.txt"

# Container 2 reads data
docker run --rm -v shared:/data alpine cat /data/file.txt
```

### Using Volumes-from

```bash
# Create data container
docker run -d --name datastore -v /data alpine

# Use volumes from datastore
docker run -d --volumes-from datastore nginx
```

## Volume Backup and Restore

### Backup Volume

```bash
# Backup to tar file
docker run --rm -v myvolume:/data -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz -C /data .

# Alternative method
docker run --rm -v myvolume:/source -v $(pwd):/backup alpine cp -r /source /backup/
```

### Restore Volume

```bash
# Restore from tar file
docker run --rm -v myvolume:/data -v $(pwd):/backup alpine tar xzf /backup/backup.tar.gz -C /data

# Create new volume and restore
docker volume create newvolume
docker run --rm -v newvolume:/data -v $(pwd):/backup alpine tar xzf /backup/backup.tar.gz -C /data
```

## Volume Migration

### Copy Between Volumes

```bash
# Copy data from vol1 to vol2
docker run --rm -v vol1:/source -v vol2:/dest alpine cp -r /source/. /dest/
```

### Move to Different Host

```bash
# On source host
docker run --rm -v myvolume:/data -v $(pwd):/backup alpine tar czf /backup/data.tar.gz -C /data .

# Transfer data.tar.gz to destination host

# On destination host
docker volume create myvolume
docker run --rm -v myvolume:/data -v $(pwd):/backup alpine tar xzf /backup/data.tar.gz -C /data
```

## Volume Permissions

### Setting Permissions

```bash
# Run as specific user
docker run -d --user 1000:1000 -v myvolume:/data nginx

# Initialize volume with permissions
docker run --rm -v myvolume:/data alpine chown -R 1000:1000 /data
```

### Common Permission Issues

```bash
# Fix permission denied errors
docker run --rm -v myvolume:/data alpine chmod -R 755 /data
```

## Docker Compose with Volumes

```yaml
version: "3.8"

services:
  app:
    image: nginx
    volumes:
      - mydata:/data
      - ./config:/etc/nginx:ro
      - logs:/var/log/nginx

volumes:
  mydata:
    driver: local
  logs:
    driver: local
    driver_opts:
      type: none
      device: /host/logs
      o: bind
```

## Volume Best Practices

1. **Use named volumes** for important data
2. **Avoid anonymous volumes** in production
3. **Use bind mounts** for development only
4. **Regular backups** of critical volumes
5. **Clean up unused volumes** regularly
6. **Use volume drivers** for distributed storage
7. **Set proper permissions** to avoid access issues
8. **Document volume usage** in docker-compose.yml
9. **Use read-only mounts** when possible
10. **Monitor volume disk usage**

## Common Use Cases

### Database Persistence

```bash
docker run -d \
  --name postgres \
  -v pgdata:/var/lib/postgresql/data \
  -e POSTGRES_PASSWORD=secret \
  postgres:15
```

### Application Logs

```bash
docker run -d \
  --name app \
  -v logs:/var/log/app \
  myapp:latest
```

### Configuration Files

```bash
docker run -d \
  --name nginx \
  -v ./nginx.conf:/etc/nginx/nginx.conf:ro \
  nginx
```

### Development Environment

```bash
docker run -d \
  --name dev \
  -v $(pwd):/app \
  -v node_modules:/app/node_modules \
  node:18
```

## Troubleshooting

### Volume Not Mounting

```bash
# Check volume exists
docker volume ls

# Inspect volume
docker volume inspect myvolume

# Check container mounts
docker inspect <container> | grep -A 10 Mounts
```

### Permission Denied

```bash
# Check volume permissions
docker run --rm -v myvolume:/data alpine ls -la /data

# Fix permissions
docker run --rm -v myvolume:/data alpine chown -R 1000:1000 /data
```

### Volume Full

```bash
# Check volume size
docker system df -v

# Clean up unused volumes
docker volume prune
```

### Data Not Persisting

- Ensure volume is mounted correctly
- Check if using anonymous volume
- Verify volume path in container
- Check if container is writing to correct location

## Volume vs Bind Mount

| Feature     | Volume                     | Bind Mount    |
| ----------- | -------------------------- | ------------- |
| Management  | Docker managed             | User managed  |
| Location    | `/var/lib/docker/volumes/` | Any host path |
| Performance | Better                     | Good          |
| Portability | High                       | Low           |
| Backup      | Easier                     | Manual        |
| Use Case    | Production                 | Development   |

## Quick Reference

| Command                 | Description           |
| ----------------------- | --------------------- |
| `docker volume create`  | Create volume         |
| `docker volume ls`      | List volumes          |
| `docker volume inspect` | View details          |
| `docker volume rm`      | Remove volume         |
| `docker volume prune`   | Remove unused volumes |
| `-v name:/path`         | Mount named volume    |
| `-v /host:/container`   | Bind mount            |
| `--mount`               | Advanced mount syntax |
| `--volumes-from`        | Share volumes         |

## Storage Locations

- **Linux**: `/var/lib/docker/volumes/`
- **macOS**: `~/Library/Containers/com.docker.docker/Data/vms/0/`
- **Windows**: `C:\ProgramData\Docker\volumes\`

---

**Related Topics**: Docker Containers, Docker Compose, Data Persistence, Backup Strategies
