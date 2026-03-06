# Git Troubleshooting

## Undo Changes

### Discard Working Directory Changes

```bash
# Discard changes in specific file
git restore <file>
git checkout -- <file>

# Discard all changes
git restore .
git checkout -- .
```

### Unstage Files

```bash
# Unstage specific file
git restore --staged <file>
git reset HEAD <file>

# Unstage all files
git restore --staged .
git reset HEAD .
```

### Undo Last Commit

```bash
# Keep changes in working directory
git reset --soft HEAD~1

# Keep changes unstaged
git reset HEAD~1

# Discard changes completely
git reset --hard HEAD~1
```

### Undo Multiple Commits

```bash
# Undo last 3 commits
git reset --soft HEAD~3
git reset HEAD~3
git reset --hard HEAD~3
```

### Revert Commit

```bash
# Create new commit that undoes changes
git revert <commit-hash>

# Revert without committing
git revert -n <commit-hash>
```

## Merge Conflicts

### Resolve Conflicts

```bash
# 1. See conflicted files
git status

# 2. Open and edit conflicted files
# Look for conflict markers:
<<<<<<< HEAD
Your changes
=======
Their changes
>>>>>>> branch-name

# 3. After resolving
git add <resolved-file>
git commit

# Abort merge
git merge --abort
```

### Resolve Rebase Conflicts

```bash
# 1. Resolve conflicts in files
# 2. Stage resolved files
git add <file>

# 3. Continue rebase
git rebase --continue

# Skip commit
git rebase --skip

# Abort rebase
git rebase --abort
```

## Branch Issues

### Recover Deleted Branch

```bash
# Find commit hash
git reflog

# Recreate branch
git branch <branch-name> <commit-hash>
```

### Switch Branch with Uncommitted Changes

```bash
# Stash changes
git stash
git checkout <branch>
git stash pop

# Or commit to temporary branch
git checkout -b temp-branch
git add .
git commit -m "WIP"
git checkout <target-branch>
```

### Delete Branch That Won't Delete

```bash
# Force delete local branch
git branch -D <branch-name>

# Delete remote branch
git push origin --delete <branch-name>
```

## Remote Issues

### Wrong Remote URL

```bash
# View remotes
git remote -v

# Change URL
git remote set-url origin <new-url>

# Remove and re-add
git remote remove origin
git remote add origin <url>
```

### Push Rejected

```bash
# Pull first
git pull --rebase origin main
git push origin main

# Force push (dangerous!)
git push --force origin main

# Force push with lease (safer)
git push --force-with-lease origin main
```

### Diverged Branches

```bash
# Option 1: Rebase
git pull --rebase origin main

# Option 2: Merge
git pull origin main

# Option 3: Reset to remote
git fetch origin
git reset --hard origin/main
```

## Commit Issues

### Amend Last Commit

```bash
# Change commit message
git commit --amend -m "New message"

# Add forgotten files
git add <forgotten-file>
git commit --amend --no-edit

# Amend author
git commit --amend --author="Name <email>"
```

### Change Old Commit Message

```bash
# Interactive rebase
git rebase -i HEAD~3

# Change 'pick' to 'reword' for commits to edit
# Save and edit messages
```

### Split Commit

```bash
# Interactive rebase
git rebase -i HEAD~3

# Change 'pick' to 'edit'
# Reset commit
git reset HEAD~1

# Stage and commit separately
git add <file1>
git commit -m "First part"
git add <file2>
git commit -m "Second part"

# Continue rebase
git rebase --continue
```

## Stash Issues

### List Stashes

```bash
git stash list
```

### Apply Specific Stash

```bash
git stash apply stash@{2}
git stash pop stash@{2}
```

### Recover Dropped Stash

```bash
# Find stash commit
git fsck --unreachable | grep commit

# Apply stash
git stash apply <commit-hash>
```

## Large File Issues

### Remove Large File from History

```bash
# Using BFG Repo-Cleaner
bfg --delete-files large-file.zip

# Using git filter-branch
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch path/to/large-file" \
  --prune-empty --tag-name-filter cat -- --all
```

### Repository Too Large

```bash
# Garbage collection
git gc --aggressive --prune=now

# Remove reflog
git reflog expire --expire=now --all
git gc --prune=now
```

## Authentication Issues

### HTTPS Authentication

```bash
# Use credential helper
git config --global credential.helper cache
git config --global credential.helper 'cache --timeout=3600'

# Store credentials (less secure)
git config --global credential.helper store
```

### SSH Issues

```bash
# Test SSH connection
ssh -T git@github.com

# Use verbose mode
ssh -vT git@github.com

# Check SSH agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

## Performance Issues

### Slow Operations

```bash
# Optimize repository
git gc --aggressive

# Repack objects
git repack -a -d --depth=250 --window=250

# Clean up
git prune
git fsck
```

### Large Repository

```bash
# Shallow clone
git clone --depth 1 <url>

# Fetch only specific branch
git clone --single-branch --branch <branch> <url>
```

## Recovery

### Recover Lost Commits

```bash
# View reflog
git reflog

# Checkout lost commit
git checkout <commit-hash>

# Create branch from lost commit
git branch recovery-branch <commit-hash>
```

### Corrupted Repository

```bash
# Verify repository
git fsck --full

# Clone fresh copy
git clone <url> new-directory
cd new-directory
```

## Common Error Messages

### "fatal: not a git repository"

```bash
# Initialize repository
git init

# Or check if you're in correct directory
pwd
```

### "error: failed to push some refs"

```bash
# Pull first
git pull --rebase origin main
git push origin main
```

### "fatal: refusing to merge unrelated histories"

```bash
git pull origin main --allow-unrelated-histories
```

### "Permission denied (publickey)"

```bash
# Check SSH key
ssh-add -l

# Add SSH key
ssh-add ~/.ssh/id_ed25519

# Generate new key if needed
ssh-keygen -t ed25519 -C "email@example.com"
```
