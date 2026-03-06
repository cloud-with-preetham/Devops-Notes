# YAML Notes for DevOps

## What is YAML?

YAML (YAML Ain't Markup Language) is a human-readable data serialization language commonly used for configuration files in DevOps tools like Docker Compose, Kubernetes, Ansible, CI/CD pipelines, and more.

## Key Characteristics

- **Human-readable**: Easy to read and write
- **Data-oriented**: Focuses on data structure
- **Indentation-based**: Uses spaces (not tabs) for structure
- **Case-sensitive**: Keys and values are case-sensitive
- **File extension**: `.yml` or `.yaml`

## Basic Syntax Rules

### Indentation

```yaml
# Use spaces, NOT tabs
# 2 spaces is standard
parent:
  child: value
  another_child: value
```

### Comments

```yaml
# This is a single-line comment

key: value # Inline comment
```

### Key-Value Pairs

```yaml
name: John Doe
age: 30
city: New York
```

## Data Types

### Strings

```yaml
# Unquoted
name: John Doe

# Single quotes (literal)
message: "Hello World"

# Double quotes (escape sequences allowed)
path: "C:\\Users\\Admin"

# Multi-line (preserves newlines)
description: |
  This is a multi-line string.
  Each line is preserved.
  Including newlines.

# Multi-line (folds into single line)
summary: >
  This is a long text
  that will be folded
  into a single line.
```

### Numbers

```yaml
integer: 42
float: 3.14
exponential: 1.2e+3
octal: 0o14
hexadecimal: 0x0C
```

### Booleans

```yaml
enabled: true
disabled: false
active: yes
inactive: no
on: on
off: off
```

### Null Values

```yaml
value: null
empty: ~
nothing:
```

### Dates and Timestamps

```yaml
date: 2024-01-15
datetime: 2024-01-15T10:30:00Z
timestamp: 2024-01-15 10:30:00
```

## Data Structures

### Lists (Arrays)

```yaml
# Block style
fruits:
  - apple
  - banana
  - orange

# Inline style
colors: [red, green, blue]

# Nested lists
matrix:
  - [1, 2, 3]
  - [4, 5, 6]
  - [7, 8, 9]
```

### Dictionaries (Maps/Objects)

```yaml
# Block style
person:
  name: John
  age: 30
  city: New York

# Inline style
person: {name: John, age: 30, city: New York}

# Nested dictionaries
company:
  name: TechCorp
  address:
    street: 123 Main St
    city: Boston
    country: USA
```

### Complex Structures

```yaml
employees:
  - name: Alice
    role: Developer
    skills:
      - Python
      - Docker
      - Kubernetes
  - name: Bob
    role: DevOps
    skills:
      - AWS
      - Terraform
      - Ansible
```

## Advanced Features

### Anchors and Aliases

```yaml
# Define anchor with &
defaults: &defaults
  timeout: 30
  retries: 3

# Reference with *
service1:
  <<: *defaults
  name: api

service2:
  <<: *defaults
  name: web
  timeout: 60 # Override
```

### Merge Keys

```yaml
base: &base
  name: base
  version: 1.0

development:
  <<: *base
  environment: dev

production:
  <<: *base
  environment: prod
```

### Multiple Documents

```yaml
---
# Document 1
name: config1
value: 100
---
# Document 2
name: config2
value: 200
---
# Document 3
name: config3
value: 300
```

## YAML in DevOps Tools

### Docker Compose

```yaml
version: "3.8"

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    environment:
      - NGINX_HOST=localhost
    depends_on:
      - api
    networks:
      - frontend

  api:
    build: ./api
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DB_HOST=db
    depends_on:
      - db
    networks:
      - frontend
      - backend

  db:
    image: postgres:15
    environment:
      - POSTGRES_PASSWORD=secret
      - POSTGRES_DB=myapp
    volumes:
      - db_data:/var/lib/postgresql/data
    networks:
      - backend

volumes:
  db_data:

networks:
  frontend:
  backend:
```

### Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
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
          resources:
            limits:
              memory: "128Mi"
              cpu: "500m"
            requests:
              memory: "64Mi"
              cpu: "250m"
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: LoadBalancer
```

### Ansible

```yaml
---
- name: Configure web servers
  hosts: webservers
  become: yes
  vars:
    http_port: 80
    max_clients: 200

  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Start nginx service
      service:
        name: nginx
        state: started
        enabled: yes

    - name: Copy configuration
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      service:
        name: nginx
        state: restarted
```

### GitHub Actions

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: "18"

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Run linter
        run: npm run lint

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Build Docker image
        run: docker build -t myapp:${{ github.sha }} .

      - name: Push to registry
        run: |
          echo ${{ secrets.DOCKER_PASSWORD }} | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin
          docker push myapp:${{ github.sha }}

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to production
        run: echo "Deploying to production..."
```

### GitLab CI

```yaml
stages:
  - build
  - test
  - deploy

variables:
  DOCKER_IMAGE: myapp
  DOCKER_TAG: $CI_COMMIT_SHORT_SHA

before_script:
  - echo "Starting pipeline..."

build:
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

test:
  stage: test
  image: node:18
  script:
    - npm install
    - npm test
    - npm run lint
  coverage: '/Coverage: \d+\.\d+%/'

deploy_staging:
  stage: deploy
  script:
    - echo "Deploying to staging..."
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - develop

deploy_production:
  stage: deploy
  script:
    - echo "Deploying to production..."
  environment:
    name: production
    url: https://example.com
  only:
    - main
  when: manual
```

### Terraform (HCL but similar)

```yaml
# terraform.tfvars.yml style
region: us-east-1
instance_type: t2.micro
environment: production

vpc_config:
  cidr_block: 10.0.0.0/16
  enable_dns: true

subnets:
  - name: public-1
    cidr: 10.0.1.0/24
    az: us-east-1a
  - name: public-2
    cidr: 10.0.2.0/24
    az: us-east-1b

tags:
  Environment: production
  Project: myapp
  ManagedBy: terraform
```

## Best Practices

### 1. Indentation

```yaml
# Good - consistent 2 spaces
services:
  web:
    image: nginx
    ports:
      - "80:80"

# Bad - inconsistent indentation
services:
  web:
      image: nginx
    ports:
    - "80:80"
```

### 2. Quotes

```yaml
# Use quotes when needed
version: "3.8" # Version numbers
path: "C:\\Users" # Special characters
message: "Value with: colon" # Contains special chars

# No quotes for simple values
name: myapp
port: 8080
enabled: true
```

### 3. Comments

```yaml
# Service configuration
services:
  web:
    image: nginx:alpine # Use Alpine for smaller size
    ports:
      - "80:80" # HTTP port
```

### 4. Anchors for Reusability

```yaml
# Define common configuration
x-common: &common
  restart: always
  logging:
    driver: json-file
    options:
      max-size: "10m"

services:
  web:
    <<: *common
    image: nginx

  api:
    <<: *common
    image: node:18
```

### 5. Environment Variables

```yaml
# Use environment variables
services:
  app:
    image: myapp:${VERSION:-latest}
    environment:
      - DB_HOST=${DB_HOST}
      - DB_PORT=${DB_PORT:-5432}
```

### 6. Multi-line Strings

```yaml
# Use | for preserving newlines
script: |
  #!/bin/bash
  echo "Starting deployment"
  ./deploy.sh
  echo "Deployment complete"

# Use > for folding lines
description: >
  This is a long description
  that will be folded into
  a single line.
```

## Common Mistakes

### 1. Using Tabs

```yaml
# Wrong - uses tabs
services:
	web:
		image: nginx

# Correct - uses spaces
services:
  web:
    image: nginx
```

### 2. Inconsistent Indentation

```yaml
# Wrong
services:
  web:
      image: nginx
    ports:
  - "80:80"

# Correct
services:
  web:
    image: nginx
    ports:
      - "80:80"
```

### 3. Missing Quotes

```yaml
# Wrong - special characters without quotes
path: C:\Users\Admin

# Correct
path: "C:\\Users\\Admin"
```

### 4. Boolean Values

```yaml
# Be explicit with booleans
enabled: true # Not "true" or yes
disabled: false # Not "false" or no
```

## Validation and Linting

### Using yamllint

```bash
# Install
pip install yamllint

# Validate file
yamllint config.yml

# With custom config
yamllint -c .yamllint config.yml
```

### .yamllint Configuration

```yaml
extends: default

rules:
  line-length:
    max: 120
  indentation:
    spaces: 2
  comments:
    min-spaces-from-content: 1
```

### Online Validators

- YAML Lint: http://www.yamllint.com/
- Code Beautify: https://codebeautify.org/yaml-validator

## Conversion Tools

### JSON to YAML

```bash
# Using yq
yq eval -P config.json > config.yml

# Using Python
python -c "import yaml, json; print(yaml.dump(json.load(open('config.json'))))"
```

### YAML to JSON

```bash
# Using yq
yq eval -o=json config.yml > config.json

# Using Python
python -c "import yaml, json; print(json.dumps(yaml.safe_load(open('config.yml'))))"
```

## Security Considerations

### 1. Don't Store Secrets

```yaml
# Bad - hardcoded secrets
database:
  password: mysecretpassword123

# Good - use environment variables
database:
  password: ${DB_PASSWORD}
```

### 2. Use Secret Management

```yaml
# Docker Compose with secrets
services:
  app:
    image: myapp
    secrets:
      - db_password

secrets:
  db_password:
    external: true
```

### 3. Validate Input

```yaml
# Use schema validation
# Define constraints and types
version: "3.8"
services:
  web:
    image: nginx:alpine # Specific version, not latest
```

## Troubleshooting

### Common Errors

```yaml
# Error: mapping values are not allowed here
# Cause: Missing quotes around value with colon
url: http://example.com  # Wrong
url: "http://example.com"  # Correct

# Error: could not find expected ':'
# Cause: Incorrect indentation
services:
web:  # Wrong - needs indentation
  image: nginx

# Error: found character '\t' that cannot start any token
# Cause: Using tabs instead of spaces
# Solution: Replace all tabs with spaces
```

---

**Related Topics**: Docker Compose, Kubernetes, Ansible, CI/CD, Configuration Management
