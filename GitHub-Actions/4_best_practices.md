# GitHub Actions Best Practices

## Security

### Use Secrets
```yaml
steps:
- name: Deploy
  env:
    API_KEY: ${{ secrets.API_KEY }}
  run: ./deploy.sh
```

### Pin Action Versions
```yaml
# Good - Pin to SHA
- uses: actions/checkout@8e5e7e5ab8b370d6c329ec480221332ada57f0ab

# Acceptable - Pin to version
- uses: actions/checkout@v3

# Bad - Don't use branches
- uses: actions/checkout@main
```

### Limit Permissions
```yaml
permissions:
  contents: read
  pull-requests: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v3
```

## Performance

### Cache Dependencies
```yaml
steps:
- uses: actions/checkout@v3

- name: Cache node modules
  uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

### Cancel Redundant Runs
```yaml
concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

### Optimize Checkout
```yaml
- uses: actions/checkout@v3
  with:
    fetch-depth: 1
```

## Maintainability

### Use Descriptive Names
```yaml
name: CI/CD Pipeline

jobs:
  test-and-build:
    name: Test and Build Application
```

### DRY with Composite Actions
```yaml
# .github/actions/setup/action.yml
name: Setup
runs:
  using: composite
  steps:
  - uses: actions/checkout@v3
  - uses: actions/setup-node@v3
```

### Environment Variables
```yaml
env:
  NODE_VERSION: '18'

jobs:
  build:
    steps:
    - uses: actions/setup-node@v3
      with:
        node-version: ${{ env.NODE_VERSION }}
```

## Reliability

### Set Timeouts
```yaml
jobs:
  build:
    timeout-minutes: 30
    steps:
    - name: Build
      timeout-minutes: 10
      run: npm run build
```

### Handle Failures
```yaml
steps:
- name: May fail
  continue-on-error: true
  run: npm audit

- name: Always run
  if: always()
  run: ./cleanup.sh
```

## Cost Optimization

### Skip CI
```yaml
on:
  push:
    paths-ignore:
      - '**.md'
      - 'docs/**'
```

### Conditional Jobs
```yaml
jobs:
  deploy:
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
```

## Debugging

### Enable Debug Logging
```bash
# Set repository secrets
ACTIONS_RUNNER_DEBUG=true
ACTIONS_STEP_DEBUG=true
```

### Add Debug Steps
```yaml
steps:
- name: Debug
  run: |
    echo "Event: ${{ github.event_name }}"
    echo "Ref: ${{ github.ref }}"
```
