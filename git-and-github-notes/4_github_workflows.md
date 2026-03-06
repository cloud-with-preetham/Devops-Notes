# GitHub Workflows

## Pull Request Workflow

```bash
# 1. Create feature branch
git checkout -b feature/new-feature

# 2. Make changes and commit
git add .
git commit -m "Add new feature"

# 3. Push to GitHub
git push -u origin feature/new-feature

# 4. Create Pull Request on GitHub
# 5. Review and merge
# 6. Delete branch after merge
git checkout main
git pull
git branch -d feature/new-feature
```

## Forking Workflow

```bash
# 1. Fork repository on GitHub

# 2. Clone your fork
git clone <your-fork-url>

# 3. Add upstream remote
git remote add upstream <original-repo-url>

# 4. Create feature branch
git checkout -b feature-branch

# 5. Make changes and commit
git add .
git commit -m "Changes"

# 6. Push to your fork
git push origin feature-branch

# 7. Create Pull Request to upstream

# 8. Sync with upstream
git fetch upstream
git checkout main
git merge upstream/main
git push origin main
```

## GitHub CLI

```bash
# Install gh CLI
# https://cli.github.com/

# Authentication
gh auth login

# Repository operations
gh repo create
gh repo clone <repo>
gh repo view

# Pull requests
gh pr create
gh pr list
gh pr view <number>
gh pr checkout <number>
gh pr merge <number>

# Issues
gh issue create
gh issue list
gh issue view <number>
gh issue close <number>
```

## GitHub Actions Basics

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Run tests
      run: |
        npm install
        npm test
```

## Collaboration Best Practices

### Branch Protection Rules
- Require pull request reviews
- Require status checks
- Require signed commits
- Restrict who can push

### Code Review Guidelines
- Review code thoroughly
- Provide constructive feedback
- Test changes locally
- Approve only when ready

### Commit Messages
```
type(scope): subject

body

footer
```

Types: feat, fix, docs, style, refactor, test, chore

## GitHub Features

### Issues
- Bug reports
- Feature requests
- Task tracking
- Labels and milestones

### Projects
- Kanban boards
- Automated workflows
- Track progress

### Wiki
- Documentation
- Guides
- Knowledge base

### Releases
```bash
# Create release
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin v1.0.0
```

## SSH Setup

```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your.email@example.com"

# Start ssh-agent
eval "$(ssh-agent -s)"

# Add key
ssh-add ~/.ssh/id_ed25519

# Copy public key
cat ~/.ssh/id_ed25519.pub

# Add to GitHub: Settings > SSH and GPG keys

# Test connection
ssh -T git@github.com
```

## Personal Access Tokens

1. GitHub Settings > Developer settings > Personal access tokens
2. Generate new token
3. Select scopes
4. Use token as password

```bash
git clone https://<token>@github.com/username/repo.git
```
