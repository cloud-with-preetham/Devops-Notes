# Git Basics

## What is Git?

Git is a distributed version control system for tracking changes in source code during software development.

## Configuration

```bash
# Set user name and email
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# View configuration
git config --list

# Set default editor
git config --global core.editor "vim"
```

## Initialize Repository

```bash
# Create new repository
git init

# Clone existing repository
git clone <repository-url>
git clone <repository-url> <directory-name>
```

## Basic Workflow

```bash
# Check status
git status

# Add files to staging
git add <file>
git add .                    # Add all files
git add *.js                 # Add specific pattern

# Commit changes
git commit -m "commit message"
git commit -am "message"     # Add and commit tracked files

# View history
git log
git log --oneline
git log --graph --oneline --all
```

## Working with Changes

```bash
# View differences
git diff                     # Unstaged changes
git diff --staged            # Staged changes
git diff <commit1> <commit2>

# Discard changes
git restore <file>           # Discard working directory changes
git restore --staged <file>  # Unstage file

# Remove files
git rm <file>
git rm --cached <file>       # Remove from Git, keep locally
```

## Git States

1. **Working Directory** - Modified files
2. **Staging Area** - Files ready to commit
3. **Repository** - Committed files

## .gitignore

```bash
# Ignore patterns
*.log
node_modules/
.env
build/
```
