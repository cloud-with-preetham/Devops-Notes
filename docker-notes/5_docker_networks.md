# Docker Networks

## What is Docker Networking?

Docker networking enables containers to communicate with each other, the host machine, and external networks. It provides isolation, security, and flexibility for containerized applications.

## Network Drivers

### 1. Bridge (Default)

Default network driver for standalone containers. Creates a private internal network on the host.

```bash
docker run -d --network bridge nginx
```

### 2. Host

Removes network isolation, container uses host's network directly.

```bash
docker run -d --network host nginx
```

### 3. None

Disables all networking for the container.

```bash
docker run -d --network none nginx
```

### 4. Overlay

Enables communication between containers across multiple Docker hosts (Swarm mode).

```bash
docker network create -d overlay myoverlay
```

### 5. Macvlan

Assigns a MAC address to container, making it appear as a physical device on the network.

```bash
docker network create -d macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 mymacvlan
```

## Network Commands

### Creating Networks

```bash
# Create default bridge network
docker network create mynetwork

# Create with specific driver
docker network create -d bridge mybridge

# Create with subnet
docker network create --subnet=172.18.0.0/16 mynetwork

# Create with gateway
docker network create --subnet=172.18.0.0/16 --gateway=172.18.0.1 mynetwork

# Create with IP range
docker network create --subnet=172.18.0.0/16 --ip-range=172.18.5.0/24 mynetwork
```

### Listing Networks

```bash
# List all networks
docker network ls

# List with filter
docker network ls --filter driver=bridge

# Custom format
docker network ls --format "table {{.ID}}\t{{.Name}}\t{{.Driver}}"
```

### Inspecting Networks

```bash
# View network details
docker network inspect mynetwork

# View multiple networks
docker network inspect bridge host

# Format output
docker network inspect --format='{{range .Containers}}{{.Name}}{{end}}' mynetwork
```

### Removing Networks

```bash
# Remove a network
docker network rm mynetwork

# Remove multiple networks
docker network rm net1 net2 net3

# Remove all unused networks
docker network prune

# Force remove
docker network rm -f mynetwork
```

## Connecting Containers to Networks

### At Container Creation

```bash
# Connect to specific network
docker run -d --network mynetwork nginx

# Connect to multiple networks
docker run -d --network net1 nginx
docker network connect net2 <container_id>

# Assign static IP
docker run -d --network mynetwork --ip 172.18.0.10 nginx

# Set network alias
docker run -d --network mynetwork --network-alias web nginx
```

### For Running Containers

```bash
# Connect container to network
docker network connect mynetwork <container_id>

# Connect with IP address
docker network connect --ip 172.18.0.10 mynetwork <container_id>

# Connect with alias
docker network connect --alias db mynetwork <container_id>

# Disconnect from network
docker network disconnect mynetwork <container_id>
```

## Port Mapping

### Publishing Ports

```bash
# Map single port
docker run -d -p 8080:80 nginx

# Map to specific host IP
docker run -d -p 127.0.0.1:8080:80 nginx

# Map multiple ports
docker run -d -p 8080:80 -p 8443:443 nginx

# Map all exposed ports randomly
docker run -d -P nginx

# Map UDP port
docker run -d -p 53:53/udp dns-server

# Map port range
docker run -d -p 8000-8010:8000-8010 myapp
```

### Viewing Port Mappings

```bash
# Show port mappings
docker port <container_id>

# Show specific port
docker port <container_id> 80
```

## Container Communication

### By Container Name (User-defined Networks)

```bash
# Create network
docker network create myapp

# Run containers
docker run -d --name web --network myapp nginx
docker run -d --name api --network myapp node:18

# Containers can communicate using names
# web can reach api at: http://api:3000
```

### By IP Address

```bash
# Get container IP
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container_id>

# Connect using IP
curl http://172.18.0.2:80
```

### Using Links (Legacy)

```bash
# Create linked containers
docker run -d --name db postgres
docker run -d --name app --link db:database myapp

# app can access db using hostname "database"
```

## DNS Resolution

Docker provides automatic DNS resolution for containers on user-defined networks.

```bash
# Create network
docker network create mynet

# Run containers
docker run -d --name service1 --network mynet alpine sleep 3600
docker run -d --name service2 --network mynet alpine sleep 3600

# service1 can ping service2 by name
docker exec service1 ping service2
```

## Network Aliases

```bash
# Create container with alias
docker run -d --network mynet --network-alias api --network-alias backend myapp

# Other containers can reach it using either alias
curl http://api:3000
curl http://backend:3000
```

## Exposing Ports in Dockerfile

```dockerfile
# Expose port (documentation only)
EXPOSE 80

# Expose multiple ports
EXPOSE 80 443

# Expose UDP port
EXPOSE 53/udp
```

Note: EXPOSE doesn't publish ports, use `-p` flag when running container.

## Network Security

### Isolating Containers

```bash
# Create isolated networks
docker network create frontend
docker network create backend

# Web server on frontend only
docker run -d --name web --network frontend nginx

# Database on backend only
docker run -d --name db --network backend postgres

# App server on both networks
docker run -d --name app --network frontend myapp
docker network connect backend app
```

### Internal Networks

```bash
# Create internal network (no external access)
docker network create --internal private

# Containers can communicate internally only
docker run -d --network private redis
```

## Docker Compose Networking

```yaml
version: "3.8"

services:
  web:
    image: nginx
    networks:
      - frontend
    ports:
      - "80:80"

  app:
    image: myapp
    networks:
      - frontend
      - backend

  db:
    image: postgres
    networks:
      - backend
    environment:
      POSTGRES_PASSWORD: secret

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true
```

## Advanced Network Configuration

### Custom Bridge Network

```bash
docker network create \
  --driver bridge \
  --subnet 172.20.0.0/16 \
  --ip-range 172.20.240.0/20 \
  --gateway 172.20.0.1 \
  --opt "com.docker.network.bridge.name"="docker1" \
  custom-bridge
```

### Network with IPv6

```bash
docker network create \
  --ipv6 \
  --subnet 2001:db8:1::/64 \
  myipv6net
```

### MTU Configuration

```bash
docker network create \
  --opt "com.docker.network.driver.mtu"="1450" \
  mymtu
```

## Inspecting Network Traffic

### Using tcpdump

```bash
# Install tcpdump in container
docker exec -it <container> apt-get update && apt-get install -y tcpdump

# Capture traffic
docker exec <container> tcpdump -i eth0 -w /tmp/capture.pcap
```

### Network Statistics

```bash
# View network stats
docker stats --format "table {{.Container}}\t{{.NetIO}}"
```

## Common Network Patterns

### Multi-tier Application

```bash
# Create networks
docker network create frontend
docker network create backend

# Frontend (nginx)
docker run -d --name web --network frontend -p 80:80 nginx

# Application (node)
docker run -d --name app --network frontend myapp
docker network connect backend app

# Database (postgres)
docker run -d --name db --network backend postgres
```

### Load Balancing

```bash
# Create network
docker network create loadbalanced

# Run multiple app instances
docker run -d --name app1 --network loadbalanced --network-alias app myapp
docker run -d --name app2 --network loadbalanced --network-alias app myapp
docker run -d --name app3 --network loadbalanced --network-alias app myapp

# Load balancer
docker run -d --name lb --network loadbalanced -p 80:80 nginx
```

### Service Discovery

```bash
# Containers automatically discover each other by name
docker network create services

docker run -d --name api --network services myapi
docker run -d --name cache --network services redis
docker run -d --name db --network services postgres

# api can connect to: redis://cache:6379 and postgres://db:5432
```

## Troubleshooting

### Check Container Network

```bash
# View container networks
docker inspect <container> | grep -A 20 Networks

# Get container IP
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container>

# Test connectivity
docker exec <container> ping <target>
docker exec <container> curl http://target:port
```

### Network Issues

```bash
# Check network exists
docker network ls

# Inspect network
docker network inspect mynetwork

# Check DNS resolution
docker exec <container> nslookup service-name

# Check port listening
docker exec <container> netstat -tulpn
```

### Port Conflicts

```bash
# Check what's using a port on host
sudo netstat -tulpn | grep :8080
sudo lsof -i :8080

# Use different host port
docker run -d -p 8081:80 nginx
```

## Network Best Practices

1. **Use user-defined networks** instead of default bridge
2. **Create separate networks** for different tiers
3. **Use internal networks** for databases
4. **Avoid host network** in production
5. **Use network aliases** for service discovery
6. **Document exposed ports** in Dockerfile
7. **Limit port exposure** to necessary services
8. **Use overlay networks** for multi-host setups
9. **Clean up unused networks** regularly
10. **Monitor network performance**

## Network Drivers Comparison

| Driver  | Use Case          | Isolation | Performance |
| ------- | ----------------- | --------- | ----------- |
| Bridge  | Single host       | High      | Good        |
| Host    | High performance  | None      | Excellent   |
| Overlay | Multi-host        | High      | Good        |
| Macvlan | Legacy apps       | Medium    | Excellent   |
| None    | Maximum isolation | Complete  | N/A         |

## Quick Reference

| Command                     | Description            |
| --------------------------- | ---------------------- |
| `docker network create`     | Create network         |
| `docker network ls`         | List networks          |
| `docker network inspect`    | View details           |
| `docker network rm`         | Remove network         |
| `docker network connect`    | Connect container      |
| `docker network disconnect` | Disconnect container   |
| `docker network prune`      | Remove unused networks |
| `-p host:container`         | Publish port           |
| `--network`                 | Specify network        |
| `--network-alias`           | Set network alias      |

## Default Networks

- **bridge**: Default network for containers
- **host**: Host network mode
- **none**: No networking

```bash
# View default networks
docker network ls
```

---

**Related Topics**: Docker Containers, Docker Compose, Service Discovery, Load Balancing
