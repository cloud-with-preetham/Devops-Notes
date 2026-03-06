# YAML Notes

Comprehensive YAML documentation covering syntax, data types, DevOps tool integration, and best practices.

## 📚 Contents

### 1. [YAML Notes](1_yaml_notes.md)

Complete YAML concepts and implementation:

- What is YAML and key characteristics
- Basic syntax rules (indentation, comments, key-value pairs)
- Data types (strings, numbers, booleans, null, dates)
- Data structures (lists, dictionaries, complex structures)
- Advanced features (anchors, aliases, merge keys, multiple documents)
- YAML in DevOps tools (Docker Compose, Kubernetes, Ansible, GitHub Actions, GitLab CI)
- Best practices and common patterns
- Validation and linting tools
- Security considerations
- Troubleshooting common errors

### 2. [YAML Cheatsheet](2_yaml_cheatsheet.md)

Quick reference guide for YAML syntax:

- Basic syntax and data types
- Multi-line strings (preserve, fold, strip)
- Lists and dictionaries
- Anchors and aliases
- Docker Compose examples
- Kubernetes manifests
- Ansible playbooks
- GitHub Actions workflows
- GitLab CI pipelines
- Common patterns and best practices
- Validation commands
- Troubleshooting tips

## 🎯 Quick Start

### Basic Syntax

```yaml
# Key-value pairs
name: John Doe
age: 30

# Nested objects
person:
  name: John
  age: 30
  city: New York

# Lists
fruits:
  - apple
  - banana
  - orange

# Inline list
colors: [red, green, blue]
```

### Data Types

```yaml
# Strings
message: "Hello World"

# Numbers
count: 42
price: 3.14

# Booleans
enabled: true
disabled: false

# Null
value: null

# Dates
date: 2024-01-15
```

## 🔑 Key Concepts

### Indentation

- Use spaces (not tabs)
- 2 spaces is standard
- Consistent indentation is critical

### Comments

```yaml
# Single-line comment
key: value # Inline comment
```

### Multi-line Strings

```yaml
# Preserve newlines (|)
script: |
  #!/bin/bash
  echo "Line 1"

# Fold into single line (>)
description: >
  This is a long text
  that will be folded.
```

### Anchors and Aliases

```yaml
# Define anchor (&)
defaults: &defaults
  timeout: 30
  retries: 3

# Reference alias (*)
service1:
  <<: *defaults
  name: api
```

## 🛠️ DevOps Tools Integration

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
```

### Kubernetes

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
```

### Ansible

```yaml
---
- name: Configure servers
  hosts: webservers
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
```

### GitHub Actions

```yaml
name: CI
on: [push, pull_request]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm install
```

### GitLab CI

```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - npm install
    - npm run build
```

## 📋 Best Practices

### Indentation

- Always use 2 spaces
- Never use tabs
- Maintain consistent indentation

### Quotes

- Quote version numbers: `version: "3.8"`
- Quote URLs and paths with special characters
- Quote values containing colons
- No quotes for simple values

### Comments

- Use comments to explain complex configurations
- Keep comments concise and helpful
- Update comments when code changes

### Reusability

- Use anchors (&) and aliases (\*) for common configurations
- Define templates for repeated patterns
- Use merge keys (<<) to combine configurations

### Security

- Never hardcode secrets
- Use environment variables for sensitive data
- Use secret management tools
- Validate input and constraints

## 🔄 Common Patterns

### Environment-specific Configuration

```yaml
# base.yml
app:
  name: myapp
  version: 1.0.0

# dev.yml
app:
  environment: development
  debug: true

# prod.yml
app:
  environment: production
  debug: false
```

### Reusable Templates

```yaml
x-common: &common
  restart: always
  logging:
    driver: json-file

services:
  web:
    <<: *common
    image: nginx

  api:
    <<: *common
    image: node:18
```

### Multi-line Scripts

```yaml
script: |
  #!/bin/bash
  echo "Starting deployment"
  ./deploy.sh
  echo "Deployment complete"
```

## 🔍 Validation and Linting

### yamllint

```bash
# Install
pip install yamllint

# Validate file
yamllint config.yml

# With custom config
yamllint -c .yamllint config.yml
```

### Docker Compose

```bash
docker compose config
```

### Kubernetes

```bash
kubectl apply --dry-run=client -f deployment.yml
```

### Ansible

```bash
ansible-playbook --syntax-check playbook.yml
```

## 🚨 Common Mistakes

### Using Tabs

```yaml
# ❌ Wrong - uses tabs
services:
	web:
		image: nginx

# ✅ Correct - uses spaces
services:
  web:
    image: nginx
```

### Inconsistent Indentation

```yaml
# ❌ Wrong
services:
  web:
      image: nginx
    ports:
  - "80:80"

# ✅ Correct
services:
  web:
    image: nginx
    ports:
      - "80:80"
```

### Missing Quotes

```yaml
# ❌ Wrong - special characters without quotes
path: C:\Users\Admin
url: http://example.com:8080

# ✅ Correct
path: "C:\\Users\\Admin"
url: "http://example.com:8080"
```

### Boolean Values

```yaml
# ❌ Inconsistent
enabled: "true"
disabled: no

# ✅ Consistent
enabled: true
disabled: false
```

## 📊 Data Types Reference

| Type       | Examples                      |
| ---------- | ----------------------------- |
| String     | `"hello"`, `hello`, `'world'` |
| Number     | `42`, `3.14`, `1.2e+3`        |
| Boolean    | `true`, `false`, `yes`, `no`  |
| Null       | `null`, `~`, (empty value)    |
| Date       | `2024-01-15`                  |
| List       | `[1, 2, 3]` or `- item`       |
| Dictionary | `{key: value}` or nested      |

## 🔗 Related Resources

- [Official YAML Specification](https://yaml.org/)
- [YAML Lint Online](http://www.yamllint.com/)
- [Docker Compose Reference](https://docs.docker.com/compose/compose-file/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [Ansible Documentation](https://docs.ansible.com/)
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [GitLab CI/CD Documentation](https://docs.gitlab.com/ee/ci/)

## 📝 Learning Path

1. Start with [YAML Fundamentals](1_yaml_notes.md#basic-syntax-rules)
2. Learn [Data Types](1_yaml_notes.md#data-types)
3. Understand [Data Structures](1_yaml_notes.md#data-structures)
4. Master [Advanced Features](1_yaml_notes.md#advanced-features)
5. Explore [DevOps Tool Integration](1_yaml_notes.md#yaml-in-devops-tools)
6. Study [Best Practices](1_yaml_notes.md#best-practices)
7. Use [Cheatsheet](2_yaml_cheatsheet.md) for quick reference

## 🎓 Practical Examples

### Docker Compose Example

```yaml
version: "3.8"
services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./html:/usr/share/nginx/html
    depends_on:
      - api

  api:
    build: ./api
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
```

### Kubernetes Example

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
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
```

### Ansible Example

```yaml
---
- name: Configure web servers
  hosts: webservers
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present

    - name: Start nginx
      service:
        name: nginx
        state: started
        enabled: yes
```

## 🚀 Next Steps

- Practice writing YAML configurations
- Validate your YAML files with yamllint
- Use YAML in Docker Compose projects
- Create Kubernetes manifests
- Write Ansible playbooks
- Build CI/CD pipelines with GitHub Actions or GitLab CI

## 💡 Pro Tips

- Always validate YAML before deploying
- Use anchors and aliases to reduce duplication
- Keep indentation consistent (2 spaces)
- Use comments to document complex configurations
- Never hardcode secrets in YAML files
- Use environment variables for sensitive data
- Test configurations in non-production environments first

---

**Happy YAML Writing! 📝**
