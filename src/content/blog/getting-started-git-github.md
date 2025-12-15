---
title: 'Getting Started with Git and GitHub'
description: 'Master version control with Git and collaborate effectively using GitHub'
pubDate: 'Dec 10 2025'
heroImage: '../../assets/git-github-hero.jpg'
---

Git and GitHub are essential tools for modern software development. Whether you're working solo or on a team, understanding version control will make you a more effective developer. Let's get started.

## What is Git?

Git is a distributed version control system that tracks changes in your code. Think of it as a time machine for your project - you can:

- See what changed and when
- Revert to previous versions
- Work on features without breaking the main code
- Collaborate with others without conflicts

## What is GitHub?

GitHub is a web platform that hosts Git repositories. It adds collaboration features on top of Git:

- Remote backup of your code
- Pull requests for code review
- Issue tracking
- Project management tools
- CI/CD integration

## Installing Git

### macOS
```bash
brew install git
```

### Windows
Download from [git-scm.com](https://git-scm.com)

### Linux
```bash
sudo apt-get install git  # Ubuntu/Debian
sudo yum install git      # CentOS/Fedora
```

Verify installation:
```bash
git --version
```

## Initial Configuration

Set up your identity:

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

These details will be attached to your commits.

## Creating Your First Repository

### Starting Locally

```bash
mkdir my-project
cd my-project
git init
```

This creates a hidden `.git` folder that tracks all changes.

### Cloning from GitHub

```bash
git clone https://github.com/username/repository.git
```

This downloads an existing repository to your computer.

## The Basic Workflow

### 1. Make Changes

Create or edit files in your project.

### 2. Check Status

```bash
git status
```

See which files have been modified.

### 3. Stage Changes

```bash
git add filename.js        # Stage specific file
git add .                  # Stage all changes
git add *.js              # Stage all .js files
```

Staging prepares files for commit.

### 4. Commit Changes

```bash
git commit -m "Add user authentication feature"
```

A commit is a snapshot of your staged changes.

### 5. Push to GitHub

```bash
git push origin main
```

Upload your commits to GitHub.

## Essential Git Commands

### Checking History

```bash
git log                    # View commit history
git log --oneline         # Compact view
git log --graph           # Visual branch structure
```

### Undoing Changes

```bash
git restore filename.js    # Discard changes in working directory
git restore --staged file  # Unstage a file
git reset HEAD~1           # Undo last commit (keep changes)
git reset --hard HEAD~1    # Undo last commit (discard changes)
```

### Viewing Differences

```bash
git diff                   # Changes not yet staged
git diff --staged          # Changes staged for commit
git diff main feature      # Compare branches
```

## Working with Branches

Branches let you work on features without affecting the main code.

### Creating Branches

```bash
git branch feature-login   # Create branch
git checkout feature-login # Switch to branch
# Or combine both:
git checkout -b feature-login
```

### Merging Branches

```bash
git checkout main
git merge feature-login
```

### Deleting Branches

```bash
git branch -d feature-login   # Delete merged branch
git branch -D feature-login   # Force delete
```

## GitHub Workflow

### 1. Create a Repository on GitHub

Click "New repository" on GitHub.com

### 2. Connect Local Repository

```bash
git remote add origin https://github.com/username/repo.git
git branch -M main
git push -u origin main
```

### 3. Making Changes

```bash
git checkout -b new-feature
# Make your changes
git add .
git commit -m "Implement new feature"
git push origin new-feature
```

### 4. Create Pull Request

On GitHub, click "Compare & pull request" and submit for review.

### 5. Merge and Delete Branch

After approval, merge the PR and delete the branch.

## Collaboration Best Practices

### 1. Write Good Commit Messages

✅ Good:
```
Add user authentication with JWT

- Implement login endpoint
- Add middleware for token validation
- Create user session management
```

❌ Bad:
```
fixed stuff
```

### 2. Commit Often

Make small, focused commits rather than large, multi-purpose ones.

### 3. Pull Before Push

```bash
git pull origin main
```

Get the latest changes before pushing to avoid conflicts.

### 4. Use Branches

Never commit directly to `main`. Always use feature branches.

### 5. Review Before Committing

```bash
git status
git diff
```

Double-check what you're committing.

## Handling Merge Conflicts

Conflicts occur when Git can't automatically merge changes.

### Resolving Conflicts

1. Git marks conflicts in your files:

```javascript
<<<<<<< HEAD
const greeting = "Hello";
=======
const greeting = "Hi there";
>>>>>>> feature-branch
```

2. Edit the file to resolve:

```javascript
const greeting = "Hello there";
```

3. Stage and commit:

```bash
git add filename.js
git commit -m "Resolve merge conflict"
```

## Useful Git Aliases

Add these to your `~/.gitconfig`:

```ini
[alias]
  st = status
  co = checkout
  br = branch
  ci = commit
  unstage = restore --staged
  last = log -1 HEAD
  visual = log --graph --oneline --all
```

Now use `git st` instead of `git status`.

## .gitignore File

Tell Git which files to ignore:

```
# .gitignore
node_modules/
.env
*.log
.DS_Store
dist/
```

## Common Scenarios

### Oops, Wrong Commit Message

```bash
git commit --amend -m "Corrected message"
```

### Forgot to Add a File

```bash
git add forgotten-file.js
git commit --amend --no-edit
```

### Want to Save Work Without Committing

```bash
git stash              # Save changes
git stash pop          # Restore changes
git stash list         # View stashed changes
```

### Need to Update from Main

```bash
git checkout feature-branch
git rebase main
```

## GitHub Features

### Issues

Track bugs and feature requests with GitHub Issues.

### Actions

Automate testing and deployment with GitHub Actions.

### Releases

Create versioned releases of your software.

### GitHub Pages

Host static websites directly from your repository.

## Learning More

- [Pro Git Book](https://git-scm.com/book/en/v2) - Free, comprehensive guide
- [GitHub Learning Lab](https://lab.github.com/) - Interactive tutorials
- [Oh Shit, Git!?!](https://ohshitgit.com/) - Common problems and solutions
- Practice with real projects!

## Quick Reference

```bash
# Setup
git init
git clone <url>

# Daily workflow
git status
git add <files>
git commit -m "message"
git push

# Branching
git branch <name>
git checkout <name>
git merge <name>

# Syncing
git pull
git fetch
git push

# Undo
git restore <file>
git reset <commit>
git revert <commit>
```

## Conclusion

Git seems complex at first, but you'll use the same few commands daily:
- `git status`
- `git add`
- `git commit`
- `git push`
- `git pull`

Start with these basics, and gradually explore more advanced features as you need them. The best way to learn Git is to use it - so create a repository and start committing!
