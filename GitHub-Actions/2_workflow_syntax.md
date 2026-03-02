# Workflow Syntax

## Complete Workflow Example

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]
  workflow_dispatch:

env:
  NODE_VERSION: '18'

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node
      uses: actions/setup-node@v3
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: Install dependencies
      run: npm ci
    
    - name: Run tests
      run: npm test

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
    - uses: actions/checkout@v3
    - name: Deploy
      run: ./deploy.sh
```

## Triggers (on)

```yaml
# Push
on:
  push:
    branches:
      - main
      - 'releases/**'
    tags:
      - v1.*
    paths:
      - '**.js'

# Pull Request
on:
  pull_request:
    types: [opened, synchronize, reopened]

# Schedule
on:
  schedule:
    - cron: '30 5 * * 1-5'

# Manual
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment'
        required: true
        default: 'staging'
```

## Jobs

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Job 1"
  
  job2:
    needs: job1
    runs-on: ubuntu-latest
    steps:
      - run: echo "Job 2"
  
  job3:
    needs: [job1, job2]
    runs-on: ubuntu-latest
    steps:
      - run: echo "Job 3"
```

## Steps

```yaml
steps:
# Checkout code
- uses: actions/checkout@v3

# Run command
- name: Run script
  run: ./script.sh

# Multi-line command
- name: Multi-line
  run: |
    echo "Line 1"
    echo "Line 2"

# Conditional step
- name: Conditional
  if: github.ref == 'refs/heads/main'
  run: echo "Main branch"

# Continue on error
- name: May fail
  continue-on-error: true
  run: exit 1
```

## Matrix Strategy

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node: [14, 16, 18]
    
    steps:
    - uses: actions/checkout@v3
    - uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node }}
    - run: npm test
```

## Outputs

```yaml
jobs:
  job1:
    runs-on: ubuntu-latest
    outputs:
      output1: ${{ steps.step1.outputs.test }}
    steps:
    - id: step1
      run: echo "test=hello" >> $GITHUB_OUTPUT
  
  job2:
    needs: job1
    runs-on: ubuntu-latest
    steps:
    - run: echo ${{ needs.job1.outputs.output1 }}
```

## Artifacts

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - run: npm run build
    
    - name: Upload artifact
      uses: actions/upload-artifact@v3
      with:
        name: build-files
        path: dist/
  
  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
    - name: Download artifact
      uses: actions/download-artifact@v3
      with:
        name: build-files
```

## Contexts

```yaml
steps:
- name: Context examples
  run: |
    echo "Event: ${{ github.event_name }}"
    echo "Ref: ${{ github.ref }}"
    echo "SHA: ${{ github.sha }}"
    echo "Actor: ${{ github.actor }}"
    echo "Runner OS: ${{ runner.os }}"
    echo "Job status: ${{ job.status }}"
```

## Expressions

```yaml
steps:
- name: Conditional
  if: ${{ github.ref == 'refs/heads/main' }}
  run: echo "Main branch"

- name: Contains
  if: contains(github.event.head_commit.message, '[skip ci]')
  run: echo "Skip CI"

- name: StartsWith
  if: startsWith(github.ref, 'refs/tags/')
  run: echo "Tag push"
```

## Timeouts

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    timeout-minutes: 30
    
    steps:
    - name: Long task
      timeout-minutes: 10
      run: ./long-script.sh
```

## Services

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    
    services:
      postgres:
        image: postgres:14
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432
    
    steps:
    - uses: actions/checkout@v3
    - run: npm test
```

## Concurrency

```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - run: echo "Build"
```
