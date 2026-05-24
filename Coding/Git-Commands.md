# Git Commands Reference

---

## Setup

```bash
git config --global user.name "Marnus van den Heever"
git config --global user.email "marnus@vdhfamily.com"
git config --global core.editor "code --wait"  # VS Code as editor
```

---

## Daily Workflow

```bash
# Start your day
git pull                          # get latest changes

# Check status
git status                        # what's changed
git diff                          # see unstaged changes
git diff --staged                 # see staged changes

# Stage & commit
git add file.txt                  # stage specific file
git add .                         # stage all changes
git commit -m "feat: add login"   # commit with message

# Push
git push                          # push to tracking branch
git push origin main              # push to specific branch
```

---

## Branching

```bash
# Create & switch
git checkout -b feature/new-thing  # create and switch
git switch -c feature/new-thing    # modern syntax

# Switch between branches
git checkout main
git switch main

# List branches
git branch                         # local
git branch -a                      # all (including remote)

# Merge
git checkout main
git merge feature/new-thing

# Rebase (cleaner history)
git checkout feature/new-thing
git rebase main

# Delete branch
git branch -d feature/new-thing    # safe delete
git branch -D feature/new-thing    # force delete
git push origin --delete feature/new-thing  # delete remote
```

---

## Undoing Things

```bash
# Unstage a file
git restore --staged file.txt

# Discard working directory changes
git restore file.txt

# Undo last commit (keep changes staged)
git reset --soft HEAD~1

# Undo last commit (keep changes unstaged)
git reset HEAD~1

# Undo last commit (discard changes) ⚠️ destructive
git reset --hard HEAD~1

# Revert a commit (safe - creates new commit)
git revert abc1234

# Fix last commit message
git commit --amend -m "corrected message"

# Stash changes
git stash                          # save for later
git stash pop                      # restore
git stash list                     # see all stashes
```

---

## Viewing History

```bash
# Log
git log                            # full log
git log --oneline                  # compact
git log --oneline --graph --all    # visual branch tree
git log -p                         # with diffs
git log --author="Marnus"          # by author
git log --since="2 weeks ago"

# Show specific commit
git show abc1234

# Who changed what line
git blame file.txt

# Find when a bug was introduced
git bisect start
git bisect bad                     # current commit is bad
git bisect good abc1234            # last known good
# git will checkout commits for you to test
git bisect reset                   # when done
```

---

## Remote Repositories

```bash
# Add remote
git remote add origin https://github.com/user/repo.git

# View remotes
git remote -v

# Fetch (download without merging)
git fetch origin

# Pull (fetch + merge)
git pull origin main

# Push and set upstream
git push -u origin main

# Clone
git clone https://github.com/user/repo.git
git clone https://github.com/user/repo.git my-folder
```

---

## Tags (Releases)

```bash
git tag v1.0.0                     # lightweight tag
git tag -a v1.0.0 -m "Release 1.0" # annotated tag
git push origin v1.0.0             # push tag
git push origin --tags             # push all tags
git tag                            # list tags
```

---

## Useful Tricks

```bash
# Cherry-pick a commit from another branch
git cherry-pick abc1234

# See what's different between branches
git diff main..feature/new-thing

# Find deleted file
git log --diff-filter=D -- path/to/file

# Search commit messages
git log --grep="fix bug"

# Search code changes
git log -S "functionName"

# Clean untracked files
git clean -fd                      # remove untracked files/dirs

# Squash last 3 commits into one
git rebase -i HEAD~3
```

---

## .gitignore Patterns

```gitignore
# Dependencies
node_modules/
vendor/

# Build output
dist/
build/
*.pyc
__pycache__/

# Environment
.env
.env.local
*.env

# IDE
.vscode/
.idea/
*.swp

# OS
.DS_Store
Thumbs.db

# Logs
*.log
logs/
```

---

## Commit Message Convention (Conventional Commits)

```
feat:     new feature
fix:      bug fix
docs:     documentation
style:    formatting (no logic change)
refactor: code restructure
test:     adding tests
chore:    maintenance tasks
perf:     performance improvement

Examples:
feat: add user authentication
fix: resolve login redirect loop
docs: update API documentation
refactor: extract payment service
```
