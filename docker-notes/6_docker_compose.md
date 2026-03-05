# Docker Compose

## What is Docker Compose?

Docker Compose is a tool for defining and running multi-container Docker applications. Using a YAML file, you can configure all your application's services, networks, and volumes, then create and start everything with a single command.

## Why Use Docker Compose?

- **Simplified multi-container management**
- **Declarative configuration**
- **Easy environment setup**
- **Reproducible deployments**
- **Version control friendly**
- **Development and testing efficiency**

## Installation

```bash
# Check if installed
docker compose version

# Install (if needed)
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## Basic Compose File Structure

```yaml
version: "3.8"

services:
  service_name:
    image: image_name
    # or
    build: ./path

volumes:
  volume_name:

networks:
  network_name:
```

## Compose Commands

### Basic Commands

```bash
# Start services
docker compose up

# Start in detached mode
docker compose up -d

# Stop services
docker compose down

# Stop and remove volumes
docker compose down -v

# View running services
docker compose ps

# View logs
docker compose logs

# Follow logs
docker compose logs -f

# Restart services
docker compose restart

# Pause services
docker compose pause

# Unpause services
docker compose unpause
```

### Build Commands

```bash
# Build images
docker compose build

# Build without cache
docker compose build --no-cache

# Build specific service
docker compose build service_name

# Build and start
docker compose up --build
```

### Service Management

```bash
# Start specific service
docker compose up service_name

# Stop specific service
docker compose stop service_name

# Remove specific service
docker compose rm service_name

# Scale services
docker compose up -d --scale web=3

# Execute command in service
docker compose exec service_name command

# Run one-off command
docker compose run service_name command
```

### Viewing Information

```bash
# List services
docker compose ps

# View service logs
docker compose logs service_name

# View configuration
docker compose config

# Validate compose file
docker compose config --quiet

# View images
docker compose images

# View top processes
docker compose top
```

## Service Configuration

### Basic Service

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
```

### Build from Dockerfile

```yaml
services:
  app:
    build: .
    # or
    build:
      context: ./app
      dockerfile: Dockerfile.dev
      args:
        - VERSION=1.0
```

### Environment Variables

```yaml
services:
  app:
    image: myapp
    environment:
      - NODE_ENV=production
      - DB_HOST=db
      - DB_PORT=5432
    # or
    env_file:
      - .env
      - .env.prod
```

### Ports

```yaml
services:
  web:
    image: nginx
    ports:
      - "8080:80" # host:container
      - "443:443"
      - "127.0.0.1:8000:8000" # specific IP
```

### Volumes

```yaml
services:
  app:
    image: myapp
    volumes:
      - ./app:/app # bind mount
      - data:/var/lib/data # named volume
      - /var/run/docker.sock:/var/run/docker.sock # host path
      - logs:/var/log:ro # read-only

volumes:
  data:
  logs:
```

### Networks

```yaml
services:
  web:
    image: nginx
    networks:
      - frontend

  app:
    image: myapp
    networks:
      - frontend
      - backend

  db:
    image: postgres
    networks:
      - backend

networks:
  frontend:
  backend:
```

### Dependencies

```yaml
services:
  web:
    image: nginx
    depends_on:
      - app

  app:
    image: myapp
    depends_on:
      db:
        condition: service_healthy

  db:
    image: postgres
    healthcheck:
      test: ["CMD", "pg_isready"]
      interval: 10s
      timeout: 5s
      retries: 5
```

### Restart Policies

```yaml
services:
  app:
    image: myapp
    restart: always
    # Options: no, always, on-failure, unless-stopped
```

### Resource Limits

```yaml
services:
  app:
    image: myapp
    deploy:
      resources:
        limits:
          cpus: "0.5"
          memory: 512M
        reservations:
          cpus: "0.25"
          memory: 256M
```

### Health Checks

```yaml
services:
  web:
    image: nginx
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

### Command and Entrypoint

```yaml
services:
  app:
    image: myapp
    command: npm start
    # or
    command: ["npm", "start"]

    entrypoint: /app/entrypoint.sh
    # or
    entrypoint: ["/app/entrypoint.sh"]
```

## Complete Example: LAMP Stack

```yaml
version: "3.8"

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./public:/var/www/html
    depends_on:
      - php
    networks:
      - frontend

  php:
    build:
      context: ./php
      dockerfile: Dockerfile
    volumes:
      - ./public:/var/www/html
    networks:
      - frontend
      - backend
    depends_on:
      - db

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: myapp
      MYSQL_USER: user
      MYSQL_PASSWORD: password
    volumes:
      - db_data:/var/lib/mysql
    networks:
      - backend
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  db_data:

networks:
  frontend:
  backend:
```

## Complete Example: Node.js + MongoDB

```yaml
version: "3.8"

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - MONGO_URL=mongodb://db:27017/myapp
    volumes:
      - ./src:/app/src
      - /app/node_modules
    depends_on:
      - db
    restart: unless-stopped
    networks:
      - app-network

  db:
    image: mongo:6
    volumes:
      - mongo_data:/data/db
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=secret
    networks:
      - app-network
    restart: unless-stopped

  redis:
    image: redis:alpine
    networks:
      - app-network
    restart: unless-stopped

volumes:
  mongo_data:

networks:
  app-network:
    driver: bridge
```

## Environment Variables

### Using .env File

```bash
# .env file
COMPOSE_PROJECT_NAME=myproject
DB_PASSWORD=secret
APP_PORT=3000
```

```yaml
# docker-compose.yml
services:
  app:
    image: myapp
    ports:
      - "${APP_PORT}:3000"
    environment:
      - DB_PASSWORD=${DB_PASSWORD}
```

### Variable Substitution

```yaml
services:
  web:
    image: nginx:${NGINX_VERSION:-latest}
    ports:
      - "${WEB_PORT:-80}:80"
```

## Extending Compose Files

### Override File

```yaml
# docker-compose.yml (base)
services:
  app:
    image: myapp
    ports:
      - "3000:3000"

# docker-compose.override.yml (auto-loaded)
services:
  app:
    volumes:
      - ./src:/app/src
    environment:
      - DEBUG=true
```

### Multiple Files

```bash
# Use specific files
docker compose -f docker-compose.yml -f docker-compose.prod.yml up

# Production override
docker compose -f docker-compose.yml -f docker-compose.prod.yml config
```

### Extends (Legacy)

```yaml
# common.yml
services:
  base:
    image: myapp
    environment:
      - NODE_ENV=production

# docker-compose.yml
services:
  app:
    extends:
      file: common.yml
      service: base
    ports:
      - "3000:3000"
```

## Profiles

```yaml
services:
  app:
    image: myapp
    profiles: ["prod"]

  debug:
    image: myapp-debug
    profiles: ["dev"]

# Start with profile
# docker compose --profile dev up
```

## Secrets (Swarm Mode)

```yaml
services:
  app:
    image: myapp
    secrets:
      - db_password

secrets:
  db_password:
    file: ./secrets/db_password.txt
```

## Best Practices

1. **Use version control** for compose files
2. **Use .env files** for environment-specific values
3. **Don't store secrets** in compose files
4. **Use named volumes** for data persistence
5. **Define health checks** for critical services
6. **Use depends_on** with conditions
7. **Limit resource usage** with deploy section
8. **Use specific image tags** not `latest`
9. **Organize with networks** for security
10. **Document your setup** with comments

## Common Patterns

### Development Setup

```yaml
version: "3.8"

services:
  app:
    build: .
    volumes:
      - .:/app
      - /app/node_modules
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
    command: npm run dev
```

### Production Setup

```yaml
version: "3.8"

services:
  app:
    image: myapp:1.0.0
    restart: always
    environment:
      - NODE_ENV=production
    deploy:
      resources:
        limits:
          memory: 512M
    logging:
      driver: "json-file"
      options:
        max-size: "10m"
        max-file: "3"
```

## Troubleshooting

### View Logs

```bash
# All services
docker compose logs

# Specific service
docker compose logs app

# Follow logs
docker compose logs -f

# Last N lines
docker compose logs --tail=100
```

### Debug Configuration

```bash
# Validate and view config
docker compose config

# Check for errors
docker compose config --quiet
```

### Restart Services

```bash
# Restart all
docker compose restart

# Restart specific service
docker compose restart app

# Force recreate
docker compose up -d --force-recreate
```

### Clean Up

```bash
# Stop and remove containers
docker compose down

# Remove volumes too
docker compose down -v

# Remove images too
docker compose down --rmi all
```

## Quick Reference

| Command                  | Description      |
| ------------------------ | ---------------- |
| `docker compose up`      | Start services   |
| `docker compose down`    | Stop and remove  |
| `docker compose ps`      | List services    |
| `docker compose logs`    | View logs        |
| `docker compose build`   | Build images     |
| `docker compose exec`    | Execute command  |
| `docker compose restart` | Restart services |
| `docker compose config`  | Validate config  |
| `docker compose pull`    | Pull images      |
| `docker compose push`    | Push images      |

## Compose File Versions

- **v3.8**: Latest v3, recommended for most use cases
- **v3.x**: Docker Engine 17.06.0+
- **v2.x**: Legacy, Docker Engine 1.10.0+

## Migration from v2 to v3

```yaml
# v2
version: '2'
services:
  app:
    mem_limit: 512m

# v3
version: '3.8'
services:
  app:
    deploy:
      resources:
        limits:
          memory: 512M
```

---

**Related Topics**: Docker Containers, Docker Networks, Docker Volumes, Multi-container Applications
