# Docker Cheatsheet

## Container Commands

### Running Containers

```bash
docker run <image>                          # Run container
docker run -d <image>                       # Run in background (detached)
docker run -it <image> bash                 # Run interactively
docker run --name <name> <image>            # Run with custom name
docker run --rm <image>                     # Auto-remove after exit
docker run -p 8080:80 <image>               # Port mapping
docker run -v /host:/container <image>      # Volume mount
docker run -e VAR=value <image>             # Environment variable
docker run --network <network> <image>      # Specify network
docker run -m 512m --cpus="1.5" <image>     # Resource limits
```

### Container Management

```bash
docker ps                                   # List running containers
docker ps -a                                # List all containers
docker start <container>                    # Start stopped container
docker stop <container>                     # Stop running container
docker restart <container>                  # Restart container
docker pause <container>                    # Pause container
docker unpause <container>                  # Unpause container
docker kill <container>                     # Force stop container
docker rm <container>                       # Remove container
docker rm -f <container>                    # Force remove running container
docker container prune                      # Remove all stopped containers
```

### Container Inspection

```bash
docker logs <container>                     # View logs
docker logs -f <container>                  # Follow logs
docker logs --tail 100 <container>          # Last N lines
docker inspect <container>                  # View details
docker stats <container>                    # Resource usage
docker top <container>                      # Running processes
docker port <container>                     # Port mappings
docker exec -it <container> bash            # Execute command
docker cp <container>:/path /host/path      # Copy files
```

## Image Commands

### Image Management

```bash
docker images                               # List images
docker pull <image>                         # Download image
docker pull <image>:<tag>                   # Download specific version
docker push <image>                         # Upload image
docker rmi <image>                          # Remove image
docker rmi -f <image>                       # Force remove image
docker image prune                          # Remove unused images
docker image prune -a                       # Remove all unused images
```

### Building Images

```bash
docker build -t <name>:<tag> .              # Build image
docker build -f Dockerfile.dev .            # Use specific Dockerfile
docker build --no-cache .                   # Build without cache
docker build --build-arg VAR=value .        # Build with arguments
docker tag <source> <target>                # Tag image
docker history <image>                      # View image layers
docker inspect <image>                      # View image details
```

### Image Operations

```bash
docker save -o file.tar <image>             # Export image
docker load -i file.tar                     # Import image
docker export <container> > file.tar        # Export container
docker import file.tar <image>              # Import as image
docker commit <container> <image>           # Create image from container
```

## Volume Commands

```bash
docker volume create <volume>               # Create volume
docker volume ls                            # List volumes
docker volume inspect <volume>              # View volume details
docker volume rm <volume>                   # Remove volume
docker volume prune                         # Remove unused volumes
```

### Volume Usage

```bash
docker run -v <volume>:/path <image>        # Named volume
docker run -v /host:/container <image>      # Bind mount
docker run -v /host:/container:ro <image>   # Read-only mount
docker run --mount source=vol,target=/data  # Mount syntax
```

## Network Commands

```bash
docker network create <network>             # Create network
docker network ls                           # List networks
docker network inspect <network>            # View network details
docker network rm <network>                 # Remove network
docker network prune                        # Remove unused networks
docker network connect <net> <container>    # Connect container
docker network disconnect <net> <container> # Disconnect container
```

### Network Usage

```bash
docker run --network <network> <image>      # Use specific network
docker run --network host <image>           # Use host network
docker run --network none <image>           # Disable networking
docker run --network-alias <alias> <image>  # Set network alias
docker run --ip 172.18.0.10 <image>         # Assign static IP
```

## Docker Compose Commands

```bash
docker compose up                           # Start services
docker compose up -d                        # Start in background
docker compose down                         # Stop and remove
docker compose down -v                      # Stop and remove volumes
docker compose ps                           # List services
docker compose logs                         # View logs
docker compose logs -f                      # Follow logs
docker compose build                        # Build images
docker compose pull                         # Pull images
docker compose restart                      # Restart services
docker compose stop                         # Stop services
docker compose start                        # Start services
docker compose exec <service> <cmd>         # Execute command
docker compose run <service> <cmd>          # Run one-off command
docker compose config                       # Validate and view config
docker compose up --build                   # Build and start
docker compose up --scale web=3             # Scale service
```

## System Commands

```bash
docker version                              # Show version
docker info                                 # System information
docker system df                            # Disk usage
docker system prune                         # Remove unused data
docker system prune -a                      # Remove all unused data
docker system prune -a --volumes            # Remove everything unused
docker events                               # Real-time events
```

## Dockerfile Instructions

```dockerfile
FROM <image>                                # Base image
FROM <image>:<tag>                          # Specific version
WORKDIR /app                                # Set working directory
COPY <src> <dest>                           # Copy files
ADD <src> <dest>                            # Copy (with extraction)
RUN <command>                               # Execute command
CMD ["executable", "param"]                 # Default command
ENTRYPOINT ["executable"]                   # Container executable
EXPOSE <port>                               # Document port
ENV KEY=value                               # Environment variable
ARG KEY=value                               # Build argument
VOLUME /data                                # Create volume
USER <user>                                 # Set user
LABEL key="value"                           # Add metadata
HEALTHCHECK CMD <command>                   # Health check
```

## Docker Compose File (YAML)

```yaml
version: "3.8"

services:
  web:
    image: nginx:alpine
    build: ./app
    ports:
      - "80:80"
    volumes:
      - ./data:/data
      - logs:/var/log
    environment:
      - NODE_ENV=production
    env_file:
      - .env
    depends_on:
      - db
    networks:
      - frontend
    restart: always
    deploy:
      resources:
        limits:
          cpus: "0.5"
          memory: 512M
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3

volumes:
  logs:

networks:
  frontend:
```

## Common Patterns

### Development Environment

```bash
docker run -it --rm \
  -v $(pwd):/app \
  -w /app \
  -p 3000:3000 \
  node:18 bash
```

### Database with Persistence

```bash
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=secret \
  -v pgdata:/var/lib/postgresql/data \
  -p 5432:5432 \
  postgres:15
```

### Multi-container App

```bash
# Create network
docker network create myapp

# Database
docker run -d --name db --network myapp postgres

# Backend
docker run -d --name api --network myapp -p 3000:3000 myapi

# Frontend
docker run -d --name web --network myapp -p 80:80 nginx
```

## Cleanup Commands

```bash
# Remove all stopped containers
docker container prune

# Remove all unused images
docker image prune -a

# Remove all unused volumes
docker volume prune

# Remove all unused networks
docker network prune

# Remove everything
docker system prune -a --volumes

# Remove specific pattern
docker ps -a | grep "pattern" | awk '{print $1}' | xargs docker rm
docker images | grep "pattern" | awk '{print $3}' | xargs docker rmi
```

## Debugging Commands

```bash
# View logs
docker logs <container>
docker logs -f --tail 100 <container>

# Execute shell
docker exec -it <container> bash
docker exec -it <container> sh

# Inspect container
docker inspect <container>
docker inspect --format='{{.State.Status}}' <container>

# Check processes
docker top <container>

# Monitor resources
docker stats
docker stats --no-stream

# View events
docker events
docker events --filter 'container=myapp'

# Check network
docker network inspect <network>
docker inspect <container> | grep IPAddress
```

## Port Mapping Examples

```bash
-p 8080:80                                  # Host 8080 → Container 80
-p 127.0.0.1:8080:80                        # Specific IP
-p 8080:80 -p 8443:443                      # Multiple ports
-P                                          # Random ports
-p 53:53/udp                                # UDP port
-p 8000-8010:8000-8010                      # Port range
```

## Volume Mount Examples

```bash
-v myvolume:/data                           # Named volume
-v $(pwd):/app                              # Current directory
-v /host/path:/container/path               # Absolute path
-v /host:/container:ro                      # Read-only
-v /var/run/docker.sock:/var/run/docker.sock # Docker socket
```

## Environment Variables

```bash
-e NODE_ENV=production                      # Single variable
-e VAR1=value1 -e VAR2=value2               # Multiple variables
--env-file .env                             # From file
--env-file .env --env-file .env.prod        # Multiple files
```

## Resource Limits

```bash
-m 512m                                     # Memory limit
-m 512m --memory-swap 1g                    # Memory + swap
--cpus="1.5"                                # CPU limit
--cpu-shares=512                            # CPU shares
--pids-limit=100                            # Process limit
```

## Restart Policies

```bash
--restart no                                # Never restart
--restart always                            # Always restart
--restart unless-stopped                    # Restart unless manually stopped
--restart on-failure                        # Restart on error
--restart on-failure:5                      # Max 5 restart attempts
```

## Useful Aliases

```bash
# Add to ~/.bashrc or ~/.zshrc
alias dps='docker ps'
alias dpsa='docker ps -a'
alias di='docker images'
alias dex='docker exec -it'
alias dlog='docker logs -f'
alias dstop='docker stop $(docker ps -q)'
alias drm='docker rm $(docker ps -aq)'
alias drmi='docker rmi $(docker images -q)'
alias dprune='docker system prune -a --volumes'
alias dcu='docker compose up -d'
alias dcd='docker compose down'
alias dcl='docker compose logs -f'
```

## Quick Tips

```bash
# Get container IP
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container>

# Get container size
docker ps -s

# Format output
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"

# Filter containers
docker ps --filter "status=exited"
docker ps --filter "name=web"

# Copy files
docker cp <container>:/app/file.txt ./
docker cp ./file.txt <container>:/app/

# Export/Import
docker export <container> | gzip > backup.tar.gz
gunzip -c backup.tar.gz | docker import - myimage:latest

# Backup volume
docker run --rm -v myvolume:/data -v $(pwd):/backup alpine tar czf /backup/backup.tar.gz -C /data .

# Restore volume
docker run --rm -v myvolume:/data -v $(pwd):/backup alpine tar xzf /backup/backup.tar.gz -C /data
```

## Common Issues & Solutions

```bash
# Permission denied
docker exec -u root <container> chown -R user:user /path

# Port already in use
docker ps | grep <port>
sudo lsof -i :<port>

# Container exits immediately
docker logs <container>
docker inspect <container>

# Out of disk space
docker system df
docker system prune -a --volumes

# Network issues
docker network inspect bridge
docker exec <container> ping <target>

# DNS issues
docker exec <container> cat /etc/resolv.conf
docker run --dns 8.8.8.8 <image>
```

---

**Quick Reference**: Keep this cheatsheet handy for daily Docker operations!
