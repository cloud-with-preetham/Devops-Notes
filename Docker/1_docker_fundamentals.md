# Docker Fundamentals

## What is Docker?

Docker is a platform for developing, shipping, and running applications in containers. Containers package software with all dependencies, ensuring consistency across environments.

## Key Concepts

### Container

- Lightweight, standalone executable package
- Includes code, runtime, libraries, and dependencies
- Isolated from host and other containers
- Shares OS kernel with host

### Image

- Read-only template for creating containers
- Built from Dockerfile instructions
- Stored in registries (Docker Hub, private registries)
- Layered filesystem

### Dockerfile

- Text file with instructions to build an image
- Defines base image, dependencies, and commands
- Each instruction creates a new layer

### Registry

- Storage and distribution system for Docker images
- Docker Hub is the default public registry
- Can host private registries

## Docker Architecture

![Docker Architecture](docker-architecture.png)

### Components

- **Docker Client**: CLI tool (docker command)
- **Docker Daemon**: Background service managing containers
- **Docker Registry**: Stores Docker images
- **Docker Objects**: Images, containers, networks, volumes

## Installation

### Linux

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```

### Verify Installation

```bash
docker --version
docker run hello-world
```

## Docker Images

### Pull Image

```bash
docker pull ubuntu
docker pull nginx:latest
docker pull mysql:8.0
```

### List Images

```bash
docker images
docker image ls
```

### Remove Image

```bash
docker rmi image_name
docker rmi image_id
docker image rm image_name
```

### Image Details

```bash
docker inspect image_name
docker history image_name
```

### Search Images

```bash
docker search nginx
```

## Docker Containers

### Run Container

```bash
docker run ubuntu
docker run -it ubuntu bash          # Interactive
docker run -d nginx                 # Detached
docker run --name mycontainer nginx # Named container
docker run -p 8080:80 nginx        # Port mapping
docker run -v /host:/container nginx # Volume mount
```

### List Containers

```bash
docker ps                  # Running containers
docker ps -a              # All containers
docker container ls       # Running containers
docker container ls -a    # All containers
```

### Start/Stop Containers

```bash
docker start container_name
docker stop container_name
docker restart container_name
docker pause container_name
docker unpause container_name
```

### Remove Container

```bash
docker rm container_name
docker rm -f container_name        # Force remove
docker container prune             # Remove all stopped
```

### Execute Commands

```bash
docker exec -it container_name bash
docker exec container_name ls /app
docker exec -u root container_name whoami
```

### View Logs

```bash
docker logs container_name
docker logs -f container_name      # Follow logs
docker logs --tail 100 container_name
docker logs --since 10m container_name
```

### Container Details

```bash
docker inspect container_name
docker stats container_name
docker top container_name
docker port container_name
```

## Dockerfile

### Basic Structure

```dockerfile
FROM ubuntu:20.04
LABEL maintainer="user@example.com"
RUN apt-get update && apt-get install -y nginx
COPY index.html /var/www/html/
WORKDIR /app
ENV APP_ENV=production
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Dockerfile Instructions

#### FROM

```dockerfile
FROM ubuntu:20.04
FROM node:16-alpine
```

#### RUN

```dockerfile
RUN apt-get update
RUN npm install
RUN mkdir -p /app/data
```

#### COPY

```dockerfile
COPY . /app
COPY package.json /app/
COPY --chown=user:group file.txt /app/
```

#### ADD

```dockerfile
ADD file.tar.gz /app/
ADD https://example.com/file.txt /app/
```

#### WORKDIR

```dockerfile
WORKDIR /app
```

#### ENV

```dockerfile
ENV NODE_ENV=production
ENV PORT=3000
```

#### EXPOSE

```dockerfile
EXPOSE 80
EXPOSE 3000
```

#### CMD

```dockerfile
CMD ["nginx", "-g", "daemon off;"]
CMD ["node", "app.js"]
```

#### ENTRYPOINT

```dockerfile
ENTRYPOINT ["python", "app.py"]
```

#### VOLUME

```dockerfile
VOLUME /data
VOLUME ["/var/log", "/var/db"]
```

#### USER

```dockerfile
USER appuser
```

#### ARG

```dockerfile
ARG VERSION=1.0
RUN echo $VERSION
```

### Build Image

```bash
docker build -t myapp:1.0 .
docker build -t myapp:latest -f Dockerfile.prod .
docker build --no-cache -t myapp:1.0 .
```

## Docker Volumes

### Create Volume

```bash
docker volume create myvolume
```

### List Volumes

```bash
docker volume ls
```

### Inspect Volume

```bash
docker volume inspect myvolume
```

### Remove Volume

```bash
docker volume rm myvolume
docker volume prune
```

### Use Volume

```bash
docker run -v myvolume:/data nginx
docker run -v /host/path:/container/path nginx
docker run --mount source=myvolume,target=/data nginx
```

### Bind Mounts

```bash
docker run -v $(pwd):/app nginx
docker run -v /home/user/data:/data:ro nginx  # Read-only
```

## Docker Networks

### List Networks

```bash
docker network ls
```

### Create Network

```bash
docker network create mynetwork
docker network create --driver bridge mynetwork
```

### Inspect Network

```bash
docker network inspect mynetwork
```

### Remove Network

```bash
docker network rm mynetwork
docker network prune
```

### Connect Container to Network

```bash
docker run --network mynetwork nginx
docker network connect mynetwork container_name
docker network disconnect mynetwork container_name
```

### Network Types

- **bridge**: Default, isolated network
- **host**: Use host network directly
- **none**: No networking
- **overlay**: Multi-host networking

## Docker Compose

### docker-compose.yml

```yaml
version: "3.8"

services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    networks:
      - mynetwork

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: mydb
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - mynetwork

volumes:
  db_data:

networks:
  mynetwork:
```

### Compose Commands

```bash
docker-compose up
docker-compose up -d
docker-compose down
docker-compose ps
docker-compose logs
docker-compose logs -f web
docker-compose exec web bash
docker-compose build
docker-compose restart
docker-compose stop
docker-compose start
```

## Container Lifecycle

1. **Create**: `docker create`
2. **Start**: `docker start`
3. **Run**: `docker run` (create + start)
4. **Pause**: `docker pause`
5. **Unpause**: `docker unpause`
6. **Stop**: `docker stop`
7. **Kill**: `docker kill`
8. **Remove**: `docker rm`

## Port Mapping

```bash
docker run -p 8080:80 nginx              # Host:Container
docker run -p 127.0.0.1:8080:80 nginx   # Specific IP
docker run -P nginx                      # Random ports
docker run -p 8080:80 -p 8443:443 nginx # Multiple ports
```

## Environment Variables

```bash
docker run -e VAR=value nginx
docker run -e VAR1=value1 -e VAR2=value2 nginx
docker run --env-file .env nginx
```

## Resource Limits

```bash
docker run -m 512m nginx                 # Memory limit
docker run --cpus=2 nginx                # CPU limit
docker run --memory=1g --cpus=1.5 nginx # Combined
```

## Docker Registry

### Login

```bash
docker login
docker login registry.example.com
```

### Tag Image

```bash
docker tag myapp:1.0 username/myapp:1.0
docker tag myapp:1.0 registry.example.com/myapp:1.0
```

### Push Image

```bash
docker push username/myapp:1.0
docker push registry.example.com/myapp:1.0
```

### Pull Image

```bash
docker pull username/myapp:1.0
```

## Best Practices

### Dockerfile

1. Use official base images
2. Minimize layers (combine RUN commands)
3. Use .dockerignore file
4. Don't run as root
5. Use specific image tags
6. Clean up in same layer
7. Use multi-stage builds
8. Order instructions by change frequency

### Security

1. Scan images for vulnerabilities
2. Use minimal base images (alpine)
3. Don't store secrets in images
4. Run containers as non-root user
5. Use read-only filesystems when possible
6. Limit container resources

### Performance

1. Use layer caching effectively
2. Minimize image size
3. Use .dockerignore
4. Clean package manager cache
5. Use multi-stage builds

## Common Patterns

### Multi-stage Build

```dockerfile
# Build stage
FROM node:16 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Production stage
FROM node:16-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm install --production
CMD ["node", "dist/index.js"]
```

### .dockerignore

```
node_modules
npm-debug.log
.git
.gitignore
README.md
.env
.DS_Store
```

## Troubleshooting

### View Container Logs

```bash
docker logs container_name
docker logs -f container_name
```

### Access Container Shell

```bash
docker exec -it container_name bash
docker exec -it container_name sh
```

### Inspect Container

```bash
docker inspect container_name
docker stats container_name
```

### Check Container Processes

```bash
docker top container_name
```

### Copy Files

```bash
docker cp container_name:/path/file.txt ./
docker cp ./file.txt container_name:/path/
```

## Cleanup

```bash
docker system prune              # Remove unused data
docker system prune -a           # Remove all unused
docker container prune           # Remove stopped containers
docker image prune              # Remove dangling images
docker volume prune             # Remove unused volumes
docker network prune            # Remove unused networks
```

## Useful Commands

```bash
docker version                   # Docker version
docker info                      # System information
docker system df                 # Disk usage
docker events                    # Real-time events
docker save -o image.tar image   # Save image to tar
docker load -i image.tar         # Load image from tar
docker export container > file.tar  # Export container
docker import file.tar           # Import container
```
