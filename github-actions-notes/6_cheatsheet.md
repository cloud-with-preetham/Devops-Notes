# GitHub Actions Cheatsheet

## Basic Workflow

```yaml
name: CI
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
    - run: npm test
```

## Triggers

```yaml
on: push
on: [push, pull_request]
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 0 * * *'
  workflow_dispatch:
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
```

## Steps

```yaml
steps:
- uses: actions/checkout@v3
- name: Run command
  run: npm test
- name: Multi-line
  run: |
    echo "Line 1"
    echo "Line 2"
```

## Matrix

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    node: [16, 18, 20]
runs-on: ${{ matrix.os }}
```

## Conditionals

```yaml
if: github.ref == 'refs/heads/main'
if: success()
if: failure()
if: always()
```

## Secrets

```yaml
env:
  API_KEY: ${{ secrets.API_KEY }}
```

## Artifacts

```yaml
- uses: actions/upload-artifact@v3
  with:
    name: build
    path: dist/

- uses: actions/download-artifact@v3
  with:
    name: build
```

## Cache

```yaml
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

## Contexts

```yaml
${{ github.ref }}
${{ github.sha }}
${{ github.actor }}
${{ runner.os }}
${{ secrets.TOKEN }}
```

## Common Actions

```yaml
- uses: actions/checkout@v3
- uses: actions/setup-node@v3
- uses: actions/setup-python@v4
- uses: docker/setup-buildx-action@v2
- uses: docker/login-action@v2
```
