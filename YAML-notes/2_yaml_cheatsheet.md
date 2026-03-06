# YAML Cheatsheet for DevOps

## Basic Syntax

```yaml
# Key-value pairs
key: value
name: John Doe
age: 30

# Nested objects
person:
  name: John
  age: 30
  address:
    city: New York
    country: USA

# Lists
fruits:
  - apple
  - banana
  - orange

# Inline list
colors: [red, green, blue]

# Inline object
person: {name: John, age: 30}
```

## Data Types

```yaml
# Strings
string1: Hello World
string2: "Single quotes"
string3: "Double quotes"

# Numbers
integer: 42
float: 3.14
exponential: 1.2e+3

# Booleans
enabled: true
disabled: false
active: yes
inactive: no

# Null
value: null
empty: ~
nothing:

# Dates
date: 2024-01-15
datetime: 2024-01-15T10:30:00Z
```

## Multi-line Strings

```yaml
# Preserve newlines (|)
script: |
  #!/bin/bash
  echo "Line 1"
  echo "Line 2"

# Fold into single line (>)
description: >
  This is a long text
  that will be folded
  into a single line.

# Strip final newlines (|-)
content: |-
  No trailing newline

# Keep final newlines (|+)
content: |+
  Keep trailing newlines
```

## Lists

```yaml
# Block style
items:
  - item1
  - item2
  - item3

# Inline style
items: [item1, item2, item3]

# List of objects
users:
  - name: Alice
    role: admin
  - name: Bob
    role: user

# Nested lists
matrix:
  - [1, 2, 3]
  - [4, 5, 6]
```

## Dictionaries

```yaml
# Block style
config:
  host: localhost
  port: 8080
  debug: true

# Inline style
config: {host: localhost, port: 8080, debug: true}

# Nested
server:
  web:
    host: 0.0.0.0
    port: 80
  database:
    host: db
    port: 5432
```

## Anchors & Aliases

```yaml
# Define anchor (&)
defaults: &defaults
  timeout: 30
  retries: 3

# Reference alias (*)
service1:
  <<: *defaults
  name: api

service2:
  <<: *defaults
  name: web
  timeout: 60 # Override

# Multiple anchors
base: &base
  version: 1.0

dev: &dev
  <<: *base
  env: development

# Merge multiple
production:
  <<: [*base, *dev]
  env: production
```

## Comments

```yaml
# Single line comment

key: value # Inline comment


# Multi-line comment
# Line 1
# Line 2
# Line 3
```

## Docker Compose

```yaml
version: "3.8"

services:
  web:
    image: nginx:alpine
    container_name: web
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./html:/usr/share/nginx/html
      - ./config:/etc/nginx/conf.d
    environment:
      - NGINX_HOST=localhost
      - NGINX_PORT=80
    env_file:
      - .env
    depends_on:
      - api
    networks:
      - frontend
    restart: always
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3

  api:
    build:
      context: ./api
      dockerfile: Dockerfile
      args:
        - VERSION=1.0
    ports:
      - "3000:3000"
    volumes:
      - ./api:/app
      - /app/node_modules
    environment:
      NODE_ENV: production
      DB_HOST: db
    depends_on:
      db:
        condition: service_healthy
    networks:
      - frontend
      - backend

  db:
    image: postgres:15
    volumes:
      - db_data:/var/lib/postgresql/data
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: user
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    networks:
      - backend

volumes:
  db_data:
    driver: local

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
    internal: true
```

## Kubernetes

```yaml
# Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
  annotations:
    description: "Nginx deployment"
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:1.21
          ports:
            - containerPort: 80
          env:
            - name: ENV
              value: production
          resources:
            limits:
              memory: "128Mi"
              cpu: "500m"
            requests:
              memory: "64Mi"
              cpu: "250m"
          livenessProbe:
            httpGet:
              path: /health
              port: 80
            initialDelaySeconds: 30
            periodSeconds: 10
          volumeMounts:
            - name: config
              mountPath: /etc/nginx
      volumes:
        - name: config
          configMap:
            name: nginx-config

---
# Service
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

---
# ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: nginx-config
data:
  nginx.conf: |
    server {
      listen 80;
      server_name localhost;
    }

---
# Secret
apiVersion: v1
kind: Secret
metadata:
  name: db-secret
type: Opaque
data:
  password: cGFzc3dvcmQxMjM= # base64 encoded
```

## Ansible

```yaml
---
- name: Configure servers
  hosts: webservers
  become: yes
  vars:
    http_port: 80
    packages:
      - nginx
      - git
      - curl

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
        cache_valid_time: 3600

    - name: Install packages
      apt:
        name: "{{ packages }}"
        state: present

    - name: Start service
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Copy file
      copy:
        src: files/index.html
        dest: /var/www/html/
        owner: www-data
        group: www-data
        mode: "0644"

    - name: Template config
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      service:
        name: nginx
        state: restarted
```

## GitHub Actions

```yaml
name: CI/CD

on:
  push:
    branches: [main, develop]
    tags:
      - "v*"
  pull_request:
    branches: [main]
  schedule:
    - cron: "0 0 * * 0"
  workflow_dispatch:

env:
  NODE_VERSION: "18"
  DOCKER_IMAGE: myapp

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node: [16, 18, 20]
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}
          cache: "npm"

      - run: npm ci
      - run: npm test
      - run: npm run lint

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Docker build
        run: |
          docker build -t ${{ env.DOCKER_IMAGE }}:${{ github.sha }} .
          docker tag ${{ env.DOCKER_IMAGE }}:${{ github.sha }} ${{ env.DOCKER_IMAGE }}:latest

      - name: Login to registry
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Push image
        run: docker push ${{ env.DOCKER_IMAGE }} --all-tags

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment:
      name: production
      url: https://example.com
    steps:
      - name: Deploy
        run: echo "Deploying..."
```

## GitLab CI

```yaml
stages:
  - build
  - test
  - deploy

variables:
  DOCKER_IMAGE: $CI_REGISTRY_IMAGE
  DOCKER_TAG: $CI_COMMIT_SHORT_SHA

.common: &common
  before_script:
    - echo "Starting job..."
  after_script:
    - echo "Job complete"

build:
  <<: *common
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $DOCKER_IMAGE:$DOCKER_TAG .
    - docker push $DOCKER_IMAGE:$DOCKER_TAG
  only:
    - main
    - develop
  tags:
    - docker

test:unit:
  stage: test
  image: node:18
  script:
    - npm install
    - npm test
  coverage: '/Coverage: \d+\.\d+%/'
  artifacts:
    reports:
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

test:integration:
  stage: test
  script:
    - npm run test:integration
  only:
    - merge_requests

deploy:staging:
  stage: deploy
  script:
    - echo "Deploy to staging"
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - develop

deploy:production:
  stage: deploy
  script:
    - echo "Deploy to production"
  environment:
    name: production
    url: https://example.com
  only:
    - main
  when: manual
```

## Common Patterns

### Environment-specific Config

```yaml
# base.yml
app:
  name: myapp
  version: 1.0.0

# dev.yml
app:
  environment: development
  debug: true
  database:
    host: localhost

# prod.yml
app:
  environment: production
  debug: false
  database:
    host: db.example.com
```

### Reusable Templates

```yaml
# Common configuration
x-logging: &logging
  driver: json-file
  options:
    max-size: "10m"
    max-file: "3"

x-healthcheck: &healthcheck
  interval: 30s
  timeout: 10s
  retries: 3
  start_period: 40s

services:
  web:
    image: nginx
    logging: *logging
    healthcheck:
      <<: *healthcheck
      test: ["CMD", "curl", "-f", "http://localhost"]

  api:
    image: node:18
    logging: *logging
    healthcheck:
      <<: *healthcheck
      test: ["CMD", "curl", "-f", "http://localhost:3000/health"]
```

## Validation

```bash
# yamllint
yamllint config.yml

# Docker Compose
docker compose config

# Kubernetes
kubectl apply --dry-run=client -f deployment.yml

# Ansible
ansible-playbook --syntax-check playbook.yml
```

## Best Practices

```yaml
# ✅ Good
version: "3.8"  # Quote version numbers
image: nginx:1.21  # Specific tags
timeout: 30  # Numbers without quotes
enabled: true  # Booleans without quotes
path: "C:\\Users"  # Quote special characters

# ❌ Bad
version: 3.8  # Unquoted version
image: nginx:latest  # Latest tag
timeout: "30"  # Quoted number
enabled: "true"  # Quoted boolean
path: C:\Users  # Unquoted special chars
```

## Common Mistakes

```yaml
# ❌ Using tabs (use spaces)
services:
	web:  # Wrong

# ✅ Use spaces
services:
  web:  # Correct

# ❌ Inconsistent indentation
services:
  web:
      image: nginx  # Wrong
    ports:  # Wrong

# ✅ Consistent indentation
services:
  web:
    image: nginx
    ports:

# ❌ Missing quotes
url: http://example.com:8080  # May fail

# ✅ Use quotes for URLs
url: "http://example.com:8080"
```

## Quick Reference

| Syntax       | Description        |
| ------------ | ------------------ |
| `key: value` | Key-value pair     |
| `- item`     | List item          |
| `[a, b, c]`  | Inline list        |
| `{k: v}`     | Inline object      |
| `&anchor`    | Define anchor      |
| `*alias`     | Reference alias    |
| `<<: *merge` | Merge anchor       |
| `\|`         | Preserve newlines  |
| `>`          | Fold lines         |
| `#`          | Comment            |
| `---`        | Document separator |
| `...`        | Document end       |

## Environment Variables

```yaml
# Docker Compose
services:
  app:
    image: myapp:${VERSION:-latest}
    environment:
      - DB_HOST=${DB_HOST}
      - DB_PORT=${DB_PORT:-5432}

# Kubernetes
env:
  - name: DB_HOST
    value: "localhost"
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: password

# GitHub Actions
env:
  NODE_VERSION: ${{ vars.NODE_VERSION }}
  SECRET: ${{ secrets.MY_SECRET }}
```

## Troubleshooting

```yaml
# Error: mapping values are not allowed here
# Fix: Add quotes
url: "http://example.com"

# Error: could not find expected ':'
# Fix: Check indentation
services:
  web: # Needs proper indent
    image: nginx

# Error: found character '\t'
# Fix: Replace tabs with spaces

# Error: duplicate key
# Fix: Ensure unique keys at same level
```

---

**Quick Tip**: Always use 2 spaces for indentation, never tabs!
