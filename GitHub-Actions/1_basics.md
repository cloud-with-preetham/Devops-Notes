# GitHub Actions Basics

## What is GitHub Actions?

CI/CD platform that automates build, test, and deployment workflows directly from GitHub repositories.

## Core Concepts

### Workflow
YAML file in `.github/workflows/` that defines automated process.

### Event
Trigger that starts a workflow (push, pull_request, schedule, etc.).

### Job
Set of steps that execute on the same runner.

### Step
Individual task that runs commands or actions.

### Action
Reusable unit of code that performs a specific task.

### Runner
Server that runs workflows (GitHub-hosted or self-hosted).

## Basic Workflow Structure

```yaml
name: CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    - name: Run tests
      run: npm test
```

## Workflow File Location

```
.github/
  workflows/
    ci.yml
    deploy.yml
```

## Common Triggers

```yaml
# Push to branch
on:
  push:
    branches: [ main ]

# Pull request
on:
  pull_request:
    branches: [ main ]

# Multiple events
on: [push, pull_request]

# Schedule (cron)
on:
  schedule:
    - cron: '0 0 * * *'

# Manual trigger
on: workflow_dispatch
```

## Runners

```yaml
# GitHub-hosted
runs-on: ubuntu-latest
runs-on: windows-latest
runs-on: macos-latest

# Self-hosted
runs-on: self-hosted
```

## Environment Variables

```yaml
env:
  NODE_VERSION: '18'

jobs:
  build:
    env:
      API_URL: https://api.example.com
    steps:
    - name: Use env
      run: echo $NODE_VERSION
```

## Secrets

```yaml
steps:
- name: Use secret
  env:
    API_KEY: ${{ secrets.API_KEY }}
  run: echo "Using API key"
```

Add secrets: Repository Settings > Secrets and variables > Actions
