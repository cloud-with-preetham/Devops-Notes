# CI/CD Notes for DevOps

## What is CI/CD?

**CI/CD** stands for Continuous Integration and Continuous Delivery/Deployment - a set of practices that automate the software development lifecycle from code commit to production deployment.

### Continuous Integration (CI)

Automatically building and testing code changes as developers commit to version control.

### Continuous Delivery (CD)

Automatically preparing code changes for release to production (manual approval required).

### Continuous Deployment (CD)

Automatically deploying code changes to production without manual intervention.

## CI/CD Pipeline Stages

```
Code → Build → Test → Deploy → Monitor
  ↓       ↓       ↓       ↓        ↓
Commit  Compile  Unit   Staging  Metrics
Push    Package  Integ  Prod     Logs
        Docker   E2E    Release  Alerts
```

## Key Concepts

### 1. Version Control

- **Git**: Source code management
- **Branching strategies**: GitFlow, trunk-based
- **Pull/Merge requests**: Code review process
- **Webhooks**: Trigger pipelines on events

### 2. Build Automation

- **Compile code**: Transform source to executable
- **Dependency management**: Install packages
- **Artifact creation**: Build Docker images, JARs, binaries
- **Version tagging**: Semantic versioning

### 3. Testing Automation

- **Unit tests**: Test individual components
- **Integration tests**: Test component interactions
- **E2E tests**: Test complete workflows
- **Security scans**: Vulnerability detection
- **Code quality**: Linting, coverage

### 4. Deployment Automation

- **Staging deployment**: Pre-production testing
- **Production deployment**: Live release
- **Rollback mechanisms**: Revert on failure
- **Blue-green deployment**: Zero-downtime
- **Canary releases**: Gradual rollout

### 5. Monitoring & Feedback

- **Application monitoring**: Performance metrics
- **Log aggregation**: Centralized logging
- **Alerting**: Notify on issues
- **Feedback loops**: Continuous improvement

## Popular CI/CD Tools

### Cloud-based

- **GitHub Actions**: Integrated with GitHub
- **GitLab CI/CD**: Built into GitLab
- **CircleCI**: Cloud-native CI/CD
- **Travis CI**: GitHub integration
- **Azure DevOps**: Microsoft ecosystem
- **AWS CodePipeline**: AWS native

### Self-hosted

- **Jenkins**: Most popular, highly extensible
- **GitLab Runner**: Self-hosted GitLab CI
- **TeamCity**: JetBrains product
- **Bamboo**: Atlassian product
- **Drone**: Container-native
- **Tekton**: Kubernetes-native

## GitHub Actions

### Basic Workflow

```yaml
name: CI Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: "18"
          cache: "npm"

      - name: Install dependencies
        run: npm ci

      - name: Run tests
        run: npm test

      - name: Build application
        run: npm run build

      - name: Upload artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: dist/
```

### Complete CI/CD Pipeline

```yaml
name: Complete CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

env:
  DOCKER_IMAGE: myapp
  REGISTRY: ghcr.io

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [16, 18, 20]

    steps:
      - uses: actions/checkout@v3

      - name: Setup Node ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}

      - name: Cache dependencies
        uses: actions/cache@v3
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run test:coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Run security scan
        uses: aquasecurity/trivy-action@master
        with:
          scan-type: "fs"
          scan-ref: "."

      - name: Dependency check
        run: npm audit

  build:
    needs: [test, security]
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v3

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Login to registry
        uses: docker/login-action@v2
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: |
            ${{ env.REGISTRY }}/${{ env.DOCKER_IMAGE }}:${{ github.sha }}
            ${{ env.REGISTRY }}/${{ env.DOCKER_IMAGE }}:latest
          cache-from: type=gha
          cache-to: type=gha,mode=max

  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    if: github.event_name == 'push'
    environment:
      name: staging
      url: https://staging.example.com

    steps:
      - name: Deploy to staging
        run: |
          echo "Deploying to staging..."
          # kubectl set image deployment/myapp myapp=${{ env.REGISTRY }}/${{ env.DOCKER_IMAGE }}:${{ github.sha }}

  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    environment:
      name: production
      url: https://example.com

    steps:
      - name: Deploy to production
        run: |
          echo "Deploying to production..."
          # kubectl set image deployment/myapp myapp=${{ env.REGISTRY }}/${{ env.DOCKER_IMAGE }}:${{ github.sha }}
```

## GitLab CI/CD

### Basic Pipeline

```yaml
stages:
  - build
  - test
  - deploy

variables:
  DOCKER_IMAGE: $CI_REGISTRY_IMAGE
  DOCKER_TAG: $CI_COMMIT_SHORT_SHA

build:
  stage: build
  image: node:18
  script:
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 hour

test:
  stage: test
  image: node:18
  script:
    - npm install
    - npm test
  coverage: '/Coverage: \d+\.\d+%/'

deploy:
  stage: deploy
  script:
    - echo "Deploying application..."
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
  DOCKER_IMAGE: $CI_REGISTRY_IMAGE
  DOCKER_TAG: $CI_COMMIT_SHORT_SHA
  DOCKER_DRIVER: overlay2

.docker_template: &docker_template
  image: docker:latest
  services:
    - docker:dind
  before_script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY

build:
  <<: *docker_template
  stage: build
  script:
    - docker build -t $DOCKER_IMAGE:$DOCKER_TAG .
    - docker tag $DOCKER_IMAGE:$DOCKER_TAG $DOCKER_IMAGE:latest
    - docker push $DOCKER_IMAGE:$DOCKER_TAG
    - docker push $DOCKER_IMAGE:latest
  only:
    - main
    - develop

test:unit:
  stage: test
  image: node:18
  script:
    - npm ci
    - npm run lint
    - npm test
  coverage: '/Coverage: \d+\.\d+%/'
  artifacts:
    reports:
      junit: junit.xml
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml

test:integration:
  stage: test
  image: node:18
  services:
    - postgres:15
  variables:
    POSTGRES_DB: testdb
    POSTGRES_USER: user
    POSTGRES_PASSWORD: password
  script:
    - npm ci
    - npm run test:integration

security:sast:
  stage: security
  image: returntocorp/semgrep
  script:
    - semgrep --config=auto .
  allow_failure: true

security:dependency:
  stage: security
  image: node:18
  script:
    - npm audit --audit-level=moderate
  allow_failure: true

security:container:
  stage: security
  image: aquasec/trivy
  script:
    - trivy image $DOCKER_IMAGE:$DOCKER_TAG
  allow_failure: true

deploy:staging:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl config use-context staging
    - kubectl set image deployment/myapp myapp=$DOCKER_IMAGE:$DOCKER_TAG
    - kubectl rollout status deployment/myapp
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - develop

deploy:production:
  stage: deploy
  image: bitnami/kubectl:latest
  script:
    - kubectl config use-context production
    - kubectl set image deployment/myapp myapp=$DOCKER_IMAGE:$DOCKER_TAG
    - kubectl rollout status deployment/myapp
  environment:
    name: production
    url: https://example.com
  only:
    - main
  when: manual
```

## Jenkins

### Declarative Pipeline

```groovy
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'myapp'
        DOCKER_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Test') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        sh 'npm test'
                    }
                }
                stage('Lint') {
                    steps {
                        sh 'npm run lint'
                    }
                }
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                sh 'kubectl set image deployment/myapp myapp=${DOCKER_IMAGE}:${DOCKER_TAG}'
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
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
    def app

    stage('Clone') {
        checkout scm
    }

    stage('Build') {
        sh 'npm install'
        sh 'npm run build'
    }

    stage('Test') {
        sh 'npm test'
    }

    stage('Docker Build') {
        app = docker.build("myapp:${env.BUILD_NUMBER}")
    }

    stage('Push') {
        docker.withRegistry('https://registry.example.com', 'docker-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }

    stage('Deploy') {
        if (env.BRANCH_NAME == 'main') {
            sh "kubectl set image deployment/myapp myapp=myapp:${env.BUILD_NUMBER}"
        }
    }
}
```

## CircleCI

```yaml
version: 2.1

orbs:
  node: circleci/node@5.0
  docker: circleci/docker@2.0

workflows:
  build-test-deploy:
    jobs:
      - build-and-test
      - build-docker:
          requires:
            - build-and-test
      - deploy-staging:
          requires:
            - build-docker
          filters:
            branches:
              only: develop
      - deploy-production:
          requires:
            - build-docker
          filters:
            branches:
              only: main

jobs:
  build-and-test:
    docker:
      - image: cimg/node:18.0
    steps:
      - checkout
      - node/install-packages:
          pkg-manager: npm
      - run:
          name: Run tests
          command: npm test
      - run:
          name: Run linter
          command: npm run lint
      - store_test_results:
          path: test-results
      - store_artifacts:
          path: coverage

  build-docker:
    docker:
      - image: cimg/base:stable
    steps:
      - checkout
      - setup_remote_docker
      - docker/check
      - docker/build:
          image: myapp
          tag: ${CIRCLE_SHA1}
      - docker/push:
          image: myapp
          tag: ${CIRCLE_SHA1}

  deploy-staging:
    docker:
      - image: cimg/base:stable
    steps:
      - run:
          name: Deploy to staging
          command: |
            echo "Deploying to staging..."

  deploy-production:
    docker:
      - image: cimg/base:stable
    steps:
      - run:
          name: Deploy to production
          command: |
            echo "Deploying to production..."
```

## Best Practices

### 1. Pipeline Design

- **Keep pipelines fast**: Optimize build times
- **Fail fast**: Run quick tests first
- **Parallel execution**: Run independent jobs concurrently
- **Caching**: Cache dependencies and build artifacts
- **Modular stages**: Break into logical stages

### 2. Testing Strategy

- **Test pyramid**: More unit tests, fewer E2E tests
- **Test in isolation**: Use containers for consistency
- **Code coverage**: Maintain minimum coverage threshold
- **Security scanning**: Integrate SAST/DAST tools
- **Performance testing**: Load and stress tests

### 3. Security

- **Secret management**: Use vault or secret managers
- **Least privilege**: Minimal permissions for pipelines
- **Dependency scanning**: Check for vulnerabilities
- **Image scanning**: Scan Docker images
- **Signed commits**: Verify code authenticity

### 4. Deployment Strategies

- **Blue-Green**: Two identical environments
- **Canary**: Gradual rollout to subset of users
- **Rolling**: Update instances incrementally
- **Feature flags**: Toggle features without deployment
- **Rollback plan**: Quick revert mechanism

### 5. Monitoring

- **Pipeline metrics**: Track success rate, duration
- **Application metrics**: Monitor performance
- **Log aggregation**: Centralized logging
- **Alerting**: Notify on failures
- **Dashboards**: Visualize pipeline health

## Common Patterns

### Multi-environment Deployment

```yaml
# GitHub Actions
jobs:
  deploy:
    strategy:
      matrix:
        environment: [dev, staging, production]
    environment:
      name: ${{ matrix.environment }}
    steps:
      - name: Deploy to ${{ matrix.environment }}
        run: ./deploy.sh ${{ matrix.environment }}
```

### Conditional Execution

```yaml
# GitLab CI
deploy:production:
  script:
    - ./deploy.sh
  only:
    - main
  when: manual

deploy:staging:
  script:
    - ./deploy.sh
  only:
    - develop
  when: on_success
```

### Artifact Management

```yaml
# GitHub Actions
- name: Build artifacts
  run: npm run build

- name: Upload artifacts
  uses: actions/upload-artifact@v3
  with:
    name: dist-files
    path: dist/
    retention-days: 7

- name: Download artifacts
  uses: actions/download-artifact@v3
  with:
    name: dist-files
```

## Troubleshooting

### Common Issues

**Pipeline fails intermittently**

- Check for race conditions
- Verify external dependencies
- Review resource constraints

**Slow pipeline execution**

- Enable caching
- Parallelize jobs
- Optimize Docker layers

**Deployment failures**

- Check rollback mechanisms
- Verify environment configuration
- Review deployment logs

**Secret management issues**

- Rotate secrets regularly
- Use secret scanning tools
- Implement least privilege

## Tools Comparison

| Feature       | GitHub Actions | GitLab CI | Jenkins     | CircleCI  |
| ------------- | -------------- | --------- | ----------- | --------- |
| Hosting       | Cloud          | Both      | Self-hosted | Cloud     |
| Configuration | YAML           | YAML      | Groovy      | YAML      |
| Integration   | GitHub         | GitLab    | Any         | Any       |
| Pricing       | Free tier      | Free tier | Free        | Free tier |
| Ease of use   | Easy           | Easy      | Complex     | Easy      |
| Flexibility   | Medium         | High      | Very High   | Medium    |

---

**Related Topics**: Docker, Kubernetes, Git, DevOps, Automation, Testing
