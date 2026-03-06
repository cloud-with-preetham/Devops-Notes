# Git Branching

## Branch Basics

```bash
# List branches
git branch
git branch -a                # All branches including remote

# Create branch
git branch <branch-name>

# Switch branch
git checkout <branch-name>
git switch <branch-name>     # Modern syntax

# Create and switch
git checkout -b <branch-name>
git switch -c <branch-name>

# Delete branch
git branch -d <branch-name>  # Safe delete
git branch -D <branch-name>  # Force delete
```

## Merging

```bash
# Merge branch into current
git merge <branch-name>

# Abort merge
git merge --abort

# Merge strategies
git merge --no-ff <branch>   # Create merge commit
git merge --squash <branch>  # Squash commits
```

## Rebasing

```bash
# Rebase current branch
git rebase <branch>

# Interactive rebase
git rebase -i HEAD~3         # Last 3 commits

# Abort rebase
git rebase --abort

# Continue after resolving conflicts
git rebase --continue
```

## Remote Branches

```bash
# List remote branches
git branch -r

# Fetch remote branches
git fetch origin

# Track remote branch
git checkout -b <branch> origin/<branch>
git checkout --track origin/<branch>

# Push branch to remote
git push origin <branch-name>
git push -u origin <branch-name>  # Set upstream

# Delete remote branch
git push origin --delete <branch-name>
```

## Branch Strategies

### Git Flow
- `main` - Production code
- `develop` - Development branch
- `feature/*` - New features
- `release/*` - Release preparation
- `hotfix/*` - Production fixes

### GitHub Flow
- `main` - Always deployable
- `feature-branches` - Short-lived feature branches
- Pull requests for review
- Deploy after merge

### Trunk-Based Development
- Single `main` branch
- Short-lived feature branches
- Frequent integration
