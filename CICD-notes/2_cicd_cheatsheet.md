# CI/CD Cheatsheet for DevOps

## CI/CD Basics

```
CI = Continuous Integration
CD = Continuous Delivery/Deployment

Pipeline: Code → Build → Test → Deploy → Monitor
```

## GitHub Actions

### Basic Workflow

```yaml
name: CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm install
      - run: npm test
```

### Complete Pipeline

```yaml
name: CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: "18"

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: "npm"
      - run: npm ci
      - run: npm test
      - run: npm run lint

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: docker build -t myapp:${{ github.sha }} .
      - run: docker push myapp:${{ github.sha }}

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment: production
    steps:
      - run: kubectl set image deployment/myapp myapp=myapp:${{ github.sha }}
```

### Common Actions

```yaml
# Checkout code
- uses: actions/checkout@v3

# Setup Node.js
- uses: actions/setup-node@v3
  with:
    node-version: "18"
    cache: "npm"

# Setup Python
- uses: actions/setup-python@v4
  with:
    python-version: "3.11"

# Cache dependencies
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

# Upload artifacts
- uses: actions/upload-artifact@v3
  with:
    name: build
    path: dist/

# Download artifacts
- uses: actions/download-artifact@v3
  with:
    name: build

# Docker login
- uses: docker/login-action@v2
  with:
    username: ${{ secrets.DOCKER_USERNAME }}
    password: ${{ secrets.DOCKER_PASSWORD }}

# Docker build and push
- uses: docker/build-push-action@v4
  with:
    push: true
    tags: myapp:latest
```

### Triggers

```yaml
# Push to branches
on:
  push:
    branches: [main, develop]

# Pull requests
on:
  pull_request:
    branches: [main]

# Tags
on:
  push:
    tags:
      - 'v*'

# Schedule (cron)
on:
  schedule:
    - cron: '0 0 * * 0'  # Weekly

# Manual trigger
on:
  workflow_dispatch:

# Multiple events
on: [push, pull_request, workflow_dispatch]
```

### Matrix Strategy

```yaml
jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node: [16, 18, 20]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}
```

## GitLab CI/CD

### Basic Pipeline

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

test:
  stage: test
  script:
    - npm test

deploy:
  stage: deploy
  script:
    - ./deploy.sh
  only:
    - main
```

### Complete Pipeline

```yaml
stages:
  - build
  - test
  - security
  - deploy

variables:
  DOCKER_IMAGE: $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA

build:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  script:
    - docker build -t $DOCKER_IMAGE .
    - docker push $DOCKER_IMAGE

test:unit:
  stage: test
  image: node:18
  script:
    - npm ci
    - npm test
  coverage: '/Coverage: \d+\.\d+%/'

test:integration:
  stage: test
  script:
    - npm run test:integration
  services:
    - postgres:15

security:
  stage: security
  script:
    - npm audit
  allow_failure: true

deploy:staging:
  stage: deploy
  script:
    - kubectl apply -f k8s/
  environment:
    name: staging
  only:
    - develop

deploy:production:
  stage: deploy
  script:
    - kubectl apply -f k8s/
  environment:
    name: production
  only:
    - main
  when: manual
```

### Common Keywords

```yaml
# Job control
only:
  - main
  - develop

except:
  - tags

when: manual # manual, always, on_success, on_failure

allow_failure: true

# Dependencies
needs:
  - build
  - test

dependencies:
  - build

# Artifacts
artifacts:
  paths:
    - dist/
  expire_in: 1 week

# Cache
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/

# Services
services:
  - postgres:15
  - redis:alpine

# Variables
variables:
  VAR_NAME: value
```

### Anchors & Templates

```yaml
.common: &common
  before_script:
    - echo "Starting..."
  after_script:
    - echo "Done"

job1:
  <<: *common
  script:
    - npm test

job2:
  <<: *common
  script:
    - npm build
```

## Jenkins

### Declarative Pipeline

```groovy
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'myapp'
    }

    stages {
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh './deploy.sh'
            }
        }
    }

    post {
        success {
            echo 'Success!'
        }
        failure {
            echo 'Failed!'
        }
        always {
            cleanWs()
        }
    }
}
```

### Scripted Pipeline

```groovy
node {
    stage('Checkout') {
        checkout scm
    }

    stage('Build') {
        sh 'npm install'
        sh 'npm run build'
    }

    stage('Test') {
        sh 'npm test'
    }

    stage('Deploy') {
        if (env.BRANCH_NAME == 'main') {
            sh './deploy.sh'
        }
    }
}
```

### Common Steps

```groovy
// Checkout
checkout scm

// Shell command
sh 'npm install'

// Docker
docker.build("myapp:${env.BUILD_NUMBER}")
docker.withRegistry('https://registry.com', 'credentials') {
    app.push()
}

// Parallel execution
parallel(
    'Unit Tests': { sh 'npm test' },
    'Lint': { sh 'npm run lint' }
)

// Credentials
withCredentials([string(credentialsId: 'secret', variable: 'SECRET')]) {
    sh 'echo $SECRET'
}

// Environment
withEnv(['VAR=value']) {
    sh 'echo $VAR'
}

// Workspace cleanup
cleanWs()
```

## CircleCI

### Basic Config

```yaml
version: 2.1

jobs:
  build:
    docker:
      - image: cimg/node:18.0
    steps:
      - checkout
      - run: npm install
      - run: npm test

workflows:
  build-deploy:
    jobs:
      - build
```

### Complete Pipeline

```yaml
version: 2.1

orbs:
  node: circleci/node@5.0
  docker: circleci/docker@2.0

workflows:
  main:
    jobs:
      - test
      - build:
          requires:
            - test
      - deploy:
          requires:
            - build
          filters:
            branches:
              only: main

jobs:
  test:
    docker:
      - image: cimg/node:18.0
    steps:
      - checkout
      - node/install-packages
      - run: npm test
      - store_test_results:
          path: test-results

  build:
    docker:
      - image: cimg/base:stable
    steps:
      - checkout
      - setup_remote_docker
      - run: docker build -t myapp .
      - run: docker push myapp

  deploy:
    docker:
      - image: cimg/base:stable
    steps:
      - run: kubectl apply -f k8s/
```

## Common Pipeline Patterns

### Multi-stage Build

```yaml
stages:
  - build
  - test
  - security
  - deploy

# Each stage depends on previous
```

### Parallel Jobs

```yaml
# GitHub Actions
jobs:
  test:
    strategy:
      matrix:
        version: [16, 18, 20]

# GitLab CI
test:
  parallel:
    matrix:
      - VERSION: [16, 18, 20]
```

### Conditional Execution

```yaml
# GitHub Actions
if: github.ref == 'refs/heads/main'

# GitLab CI
only:
  - main

# Jenkins
when {
    branch 'main'
}
```

### Environment Deployment

```yaml
# GitHub Actions
environment:
  name: production
  url: https://example.com

# GitLab CI
environment:
  name: production
  url: https://example.com
```

## Docker in CI/CD

### Build Image

```bash
docker build -t myapp:$VERSION .
```

### Multi-stage Build

```dockerfile
FROM node:18 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
```

### Push to Registry

```bash
docker login -u $USER -p $PASSWORD
docker push myapp:$VERSION
```

## Kubernetes Deployment

### Apply Manifests

```bash
kubectl apply -f k8s/
```

### Update Image

```bash
kubectl set image deployment/myapp myapp=myapp:$VERSION
```

### Rollout Status

```bash
kubectl rollout status deployment/myapp
```

### Rollback

```bash
kubectl rollout undo deployment/myapp
```

## Testing Commands

```bash
# Unit tests
npm test
pytest
go test

# Integration tests
npm run test:integration

# E2E tests
npm run test:e2e
cypress run

# Linting
npm run lint
eslint .
pylint **/*.py

# Code coverage
npm run test:coverage
pytest --cov

# Security scan
npm audit
trivy image myapp:latest
```

## Secrets Management

### GitHub Actions

```yaml
steps:
  - run: echo ${{ secrets.MY_SECRET }}
```

### GitLab CI

```yaml
variables:
  SECRET: $MY_SECRET
```

### Jenkins

```groovy
withCredentials([string(credentialsId: 'secret', variable: 'SECRET')]) {
    sh 'echo $SECRET'
}
```

## Caching

### GitHub Actions

```yaml
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

### GitLab CI

```yaml
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
```

### CircleCI

```yaml
- restore_cache:
    keys:
      - v1-deps-{{ checksum "package-lock.json" }}
- save_cache:
    key: v1-deps-{{ checksum "package-lock.json" }}
    paths:
      - node_modules
```

## Artifacts

### GitHub Actions

```yaml
- uses: actions/upload-artifact@v3
  with:
    name: dist
    path: dist/
```

### GitLab CI

```yaml
artifacts:
  paths:
    - dist/
  expire_in: 1 week
```

### Jenkins

```groovy
archiveArtifacts artifacts: 'dist/**', fingerprint: true
```

## Notifications

### Slack

```yaml
# GitHub Actions
- uses: 8398a7/action-slack@v3
  with:
    status: ${{ job.status }}
    webhook_url: ${{ secrets.SLACK_WEBHOOK }}

# GitLab CI
after_script:
  - 'curl -X POST -H "Content-type: application/json" --data "{\"text\":\"Build failed\"}" $SLACK_WEBHOOK'
```

### Email

```groovy
// Jenkins
post {
    failure {
        mail to: 'team@example.com',
             subject: "Build Failed: ${env.JOB_NAME}",
             body: "Build ${env.BUILD_NUMBER} failed"
    }
}
```

## Best Practices

```yaml
✅ Use specific versions (node:18, not node:latest)
✅ Cache dependencies
✅ Run tests in parallel
✅ Fail fast (quick tests first)
✅ Use secrets for sensitive data
✅ Tag images with commit SHA
✅ Implement rollback strategy
✅ Monitor pipeline metrics
✅ Keep pipelines fast (<10 min)
✅ Use matrix for multi-version testing

❌ Don't hardcode secrets
❌ Don't use latest tags in production
❌ Don't skip tests
❌ Don't deploy without approval (production)
❌ Don't ignore security scans
```

## Quick Commands

```bash
# GitHub Actions
gh workflow list
gh workflow run <workflow>
gh run list
gh run view <run-id>

# GitLab CI
gitlab-runner verify
gitlab-runner list

# Jenkins
jenkins-cli build <job>
jenkins-cli console <job> <build-number>

# Docker
docker build -t app:$VERSION .
docker push app:$VERSION
docker run -d app:$VERSION

# Kubernetes
kubectl apply -f k8s/
kubectl set image deployment/app app=app:$VERSION
kubectl rollout status deployment/app
kubectl rollout undo deployment/app
```

## Troubleshooting

```bash
# Check pipeline logs
# GitHub: Actions tab
# GitLab: CI/CD → Pipelines
# Jenkins: Build console output

# Debug mode
# GitHub: Re-run with debug logging
# GitLab: CI_DEBUG_TRACE: "true"
# Jenkins: Add echo statements

# Common issues
- Cache not working → Check cache key
- Tests failing → Run locally first
- Deployment fails → Check credentials
- Slow pipeline → Enable caching, parallelize
```

---

**Quick Tip**: Start simple, add complexity as needed!
