# Git Commands Cheatsheet

## Setup & Config

```bash
git config --global user.name "Name"
git config --global user.email "email@example.com"
git config --list
git init
git clone <url>
```

## Basic Commands

```bash
git status
git add <file>
git add .
git commit -m "message"
git commit -am "message"
git log
git log --oneline
git diff
```

## Branching

```bash
git branch
git branch <name>
git checkout <branch>
git switch <branch>
git checkout -b <branch>
git merge <branch>
git branch -d <branch>
```

## Remote

```bash
git remote add origin <url>
git remote -v
git push origin <branch>
git push -u origin <branch>
git pull
git fetch
```

## Undo Changes

```bash
git restore <file>
git restore --staged <file>
git reset HEAD~1
git reset --soft HEAD~1
git reset --hard HEAD~1
git revert <commit>
```

## Stash

```bash
git stash
git stash save "message"
git stash list
git stash apply
git stash pop
git stash drop
git stash clear
```

## Tags

```bash
git tag
git tag <name>
git tag -a <name> -m "message"
git push origin <tag>
git push origin --tags
git tag -d <name>
```

## History

```bash
git log
git log --oneline
git log --graph
git log --author="name"
git log --since="2 weeks ago"
git show <commit>
git blame <file>
```

## Advanced

```bash
git rebase <branch>
git rebase -i HEAD~3
git cherry-pick <commit>
git reflog
git clean -fd
git bisect start
```

## Aliases

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --global alias.unstage 'reset HEAD --'
git config --global alias.last 'log -1 HEAD'
```
