# GitHub Actions Troubleshooting

## Workflow Not Triggering

### Check Trigger Configuration
```yaml
# Ensure correct branch names
on:
  push:
    branches: [ main ]  # Check branch name matches
```

### Check File Location
```
.github/workflows/ci.yml  # Must be in this path
```

### Check YAML Syntax
```bash
# Use yamllint or online validator
yamllint .github/workflows/ci.yml
```

## Job Failures

### View Logs
- Go to Actions tab
- Click on workflow run
- Click on failed job
- Expand failed step

### Enable Debug Logging
```bash
# Set repository secrets
ACTIONS_RUNNER_DEBUG=true
ACTIONS_STEP_DEBUG=true
```

### Common Exit Codes
- Exit 1: General error
- Exit 127: Command not found
- Exit 130: Terminated by Ctrl+C

## Permission Issues

### GITHUB_TOKEN Permissions
```yaml
permissions:
  contents: read
  pull-requests: write
  issues: write
```

### File Permissions
```yaml
steps:
- name: Make executable
  run: chmod +x ./script.sh
- run: ./script.sh
```

## Dependency Issues

### Cache Problems
```yaml
# Clear cache by changing key
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-v2-${{ hashFiles('**/package-lock.json') }}
```

### Install Failures
```yaml
# Use ci instead of install
- run: npm ci  # Not npm install

# Clean install
- run: |
    rm -rf node_modules
    npm ci
```

## Timeout Issues

### Set Appropriate Timeouts
```yaml
jobs:
  build:
    timeout-minutes: 60
    steps:
    - name: Long task
      timeout-minutes: 30
      run: ./long-script.sh
```

### Optimize Workflow
```yaml
# Use caching
- uses: actions/cache@v3

# Shallow clone
- uses: actions/checkout@v3
  with:
    fetch-depth: 1
```

## Matrix Build Failures

### Continue on Error
```yaml
strategy:
  matrix:
    node: [14, 16, 18]
  fail-fast: false
```

### Exclude Combinations
```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    node: [14, 16, 18]
    exclude:
      - os: windows-latest
        node: 14
```

## Secrets Not Working

### Check Secret Name
```yaml
# Secret names are case-sensitive
env:
  API_KEY: ${{ secrets.API_KEY }}
```

### Secret Availability
- Organization secrets
- Repository secrets
- Environment secrets

### Masked Secrets
```yaml
# Secrets are automatically masked in logs
- run: echo ${{ secrets.API_KEY }}  # Shows ***
```

## Artifact Issues

### Upload Failures
```yaml
- uses: actions/upload-artifact@v3
  with:
    name: build
    path: dist/
    if-no-files-found: error
```

### Download Issues
```yaml
- uses: actions/download-artifact@v3
  with:
    name: build
    path: ./downloaded
```

## Context Issues

### Check Available Contexts
```yaml
- name: Debug contexts
  run: |
    echo "Event: ${{ github.event_name }}"
    echo "Ref: ${{ github.ref }}"
    echo "SHA: ${{ github.sha }}"
```

### Conditional Execution
```yaml
- name: Deploy
  if: github.ref == 'refs/heads/main' && success()
  run: ./deploy.sh
```

## Self-Hosted Runner Issues

### Runner Offline
```bash
# Check runner status
./run.sh

# Restart runner
./svc.sh stop
./svc.sh start
```

### Cleanup
```bash
# Clean workspace
- run: git clean -ffdx
```

## Rate Limiting

### GitHub API Rate Limits
```yaml
# Use GITHUB_TOKEN
- uses: actions/checkout@v3
  with:
    token: ${{ secrets.GITHUB_TOKEN }}
```

### Docker Pull Rate Limits
```yaml
# Login to Docker Hub
- uses: docker/login-action@v2
  with:
    username: ${{ secrets.DOCKERHUB_USERNAME }}
    password: ${{ secrets.DOCKERHUB_TOKEN }}
```

## Debugging Tips

### Add Debug Steps
```yaml
- name: Debug
  run: |
    pwd
    ls -la
    env
    echo "Ref: ${{ github.ref }}"
```

### Use tmate for SSH Access
```yaml
- name: Setup tmate
  if: failure()
  uses: mxschmitt/action-tmate@v3
```

### Test Locally
```bash
# Use act to test locally
act -j build
```

## Common Errors

### "Resource not accessible by integration"
```yaml
# Add required permissions
permissions:
  contents: write
```

### "refusing to allow an OAuth App"
```yaml
# Use personal access token
- uses: actions/checkout@v3
  with:
    token: ${{ secrets.PAT }}
```

### "No space left on device"
```yaml
# Clean up before build
- run: docker system prune -af
```

### "fatal: could not read Username"
```yaml
# Configure git credentials
- run: |
    git config --global user.email "bot@example.com"
    git config --global user.name "Bot"
```
