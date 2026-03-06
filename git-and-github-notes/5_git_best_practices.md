# Git Best Practices

## Commit Guidelines

### Write Good Commit Messages

```bash
# Good
git commit -m "Fix login validation bug"
git commit -m "Add user authentication feature"

# Bad
git commit -m "fix"
git commit -m "changes"
git commit -m "update"
```

### Commit Message Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

Example:
```
feat(auth): add JWT token validation

Implement JWT token validation middleware
to secure API endpoints

Closes #123
```

### Commit Often

- Make small, logical commits
- Each commit should be a single logical change
- Easier to review and revert

## Branching Strategy

### Branch Naming

```bash
# Feature branches
feature/user-authentication
feature/payment-integration

# Bug fixes
fix/login-error
bugfix/memory-leak

# Hotfixes
hotfix/security-patch

# Release branches
release/v1.2.0
```

### Keep Branches Short-Lived

- Merge frequently
- Delete after merge
- Avoid long-running branches

## Code Review

### Before Creating PR

- Test your changes
- Update documentation
- Follow coding standards
- Rebase on latest main

### PR Description

- Clear title
- Describe changes
- Link related issues
- Add screenshots if UI changes

## Merge Strategies

### Fast-Forward Merge
```bash
git merge --ff-only feature-branch
```
- Linear history
- Clean timeline

### Merge Commit
```bash
git merge --no-ff feature-branch
```
- Preserves branch history
- Shows feature context

### Squash and Merge
```bash
git merge --squash feature-branch
```
- Single commit per feature
- Clean history

### Rebase
```bash
git rebase main
```
- Linear history
- No merge commits

## .gitignore Best Practices

```bash
# OS files
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
*.swp

# Dependencies
node_modules/
vendor/

# Build outputs
dist/
build/
*.o
*.exe

# Environment
.env
.env.local

# Logs
*.log
logs/

# Temporary files
tmp/
temp/
```

## Security

### Never Commit Secrets

```bash
# Bad - Never do this
API_KEY=abc123xyz
DATABASE_PASSWORD=secret123

# Good - Use environment variables
API_KEY=${API_KEY}
DATABASE_PASSWORD=${DB_PASSWORD}
```

### Remove Committed Secrets

```bash
# Remove file from history
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch path/to/file" \
  --prune-empty --tag-name-filter cat -- --all

# Or use BFG Repo-Cleaner
bfg --delete-files secrets.txt
```

### Sign Commits

```bash
# Generate GPG key
gpg --gen-key

# Configure Git
git config --global user.signingkey <key-id>
git config --global commit.gpgsign true

# Sign commit
git commit -S -m "Signed commit"
```

## Collaboration

### Pull Before Push

```bash
git pull --rebase origin main
git push origin feature-branch
```

### Communicate

- Comment on PRs
- Use issue tracking
- Document decisions

### Code Ownership

- Use CODEOWNERS file
- Assign reviewers
- Distribute knowledge

## Performance

### Large Files

```bash
# Use Git LFS for large files
git lfs install
git lfs track "*.psd"
git add .gitattributes
```

### Shallow Clone

```bash
# Clone with limited history
git clone --depth 1 <url>
```

### Sparse Checkout

```bash
# Clone only specific directories
git clone --filter=blob:none --sparse <url>
git sparse-checkout add <directory>
```

## Maintenance

### Clean Up

```bash
# Remove merged branches
git branch --merged | grep -v "\*" | xargs -n 1 git branch -d

# Prune remote branches
git remote prune origin

# Garbage collection
git gc --aggressive
```

### Regular Backups

```bash
# Backup to multiple remotes
git remote add backup <backup-url>
git push backup --all
git push backup --tags
```

## Common Mistakes to Avoid

- Don't commit directly to main
- Don't force push to shared branches
- Don't commit large binary files
- Don't rewrite public history
- Don't ignore merge conflicts
- Don't commit commented code
- Don't use generic commit messages
