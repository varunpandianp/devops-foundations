# Git and GitHub Fundamentals — DevOps Learning Notes

## 1. What Is Git?

Git is a **distributed version control system (VCS)** used to track changes in files and source code over time.

It allows developers to maintain project history, compare changes, create branches, collaborate with other developers, and restore earlier versions of files.

### Why Do We Need Git?

- Track changes in source code.
- Identify who made a change and when.
- Restore previous versions of files.
- Work on multiple features using branches.
- Collaborate with other developers.
- Review code before merging it into the main branch.
- Support CI/CD pipelines and DevOps workflows.

### Example

Suppose you are developing a Python application.

- Day 1: Create `app.py`.
- Day 2: Add new functionality.
- Day 3: Fix a bug.
- Day 4: Accidentally delete some code.

Git allows you to inspect the history and recover previous committed versions.

---

## 2. What Is GitHub?

GitHub is an online platform for hosting Git repositories and collaborating on software projects.

Git and GitHub are different:

| Git | GitHub |
|---|---|
| Version control system | Online platform for Git repositories |
| Works locally on your computer | Hosts repositories remotely |
| Tracks changes and commits | Supports pull requests and code reviews |
| Supports local branches and merging | Adds collaboration and project-management features |
| Can work without internet | Requires network access for remote operations |

Other Git hosting platforms include GitLab, Bitbucket, and self-hosted Git servers.

---

## 3. Install Git

### Check Whether Git Is Installed

```bash
git --version
```

Example output:

```text
git version 2.x.x
```

The exact version depends on your operating system.

### Install Git on Ubuntu/Debian

```bash
sudo apt update
sudo apt install git -y
```

### Install Git on CentOS Stream / RHEL

```bash
sudo dnf install git -y
```

On older systems, `yum` may also be available.

### Verify Installation

```bash
git --version
```

---

## 4. Configure Git

Before creating commits, configure your identity.

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
```

Check the configuration:

```bash
git config --list
git config user.name
git config user.email
```

### What Does `--global` Mean?

- `--global`: Applies to your user account across repositories.
- `--local`: Applies only to the current repository.
- `--system`: Applies system-wide and generally requires administrative permissions.

Example of repository-specific configuration:

```bash
git config --local user.name "Project Author"
```

Git records the configured author and committer information in commits. Use an email address appropriate for your Git hosting account.

---

## 5. What Is a Git Repository?

A Git repository is a project directory managed by Git.

It contains project files and a hidden `.git` directory that stores Git's repository data, including commit history, references, and configuration.

### Initialize a Git Repository

Create a project:

```bash
mkdir devops-project
cd devops-project
```

Initialize Git:

```bash
git init
```

Check the status:

```bash
git status
```

List hidden files:

```bash
ls -la
```

You should see the `.git` directory.

### What Does `git init` Do?

- Creates the repository metadata in `.git`.
- Enables Git version tracking in the directory.
- Allows you to stage, commit, and manage files.

`git init` does not automatically upload your project to GitHub.

---

## 6. Git Working Areas

Git uses different areas to manage changes.

```text
Working Directory
       |
       | git add
       v
Staging Area
       |
       | git commit
       v
Local Repository
       |
       | git push
       v
Remote Repository
```

### A. Working Directory

The files and folders where you create or edit code.

### B. Staging Area

The area where you select changes to include in the next commit.

### C. Local Repository

Stores commits and project history on your local machine.

### D. Remote Repository

A repository hosted elsewhere, for example on GitHub.

**Remember:** `git add` stages changes, `git commit` records them locally, and `git push` sends committed changes to a remote repository.

---

## 7. Track Changes in Git

Create a file:

```bash
echo "Hello Git" > app.txt
```

Check the status:

```bash
git status
```

Git should show `app.txt` as an untracked file.

### What Is an Untracked File?

An untracked file is a file Git knows exists in the working directory but has not yet started tracking through staging and committing.

To begin tracking it:

```bash
git add app.txt
```

Check the status:

```bash
git status
```

The file is now staged.

After committing it, Git tracks its committed history. Future edits will appear as modifications.

---

## 8. Stage Changed Files

Staging means selecting changes for the next commit.

### Stage a Particular File

```bash
git add app.txt
```

### Stage Multiple Files

```bash
git add app.txt README.md
```

### Stage All Changes in the Current Directory

```bash
git add .
```

Review the staged changes:

```bash
git status
git diff --staged
```

### Why Is Staging Needed?

Staging lets you select exactly which changes belong in a commit.

For example, if you changed three files but only want to commit one of them, stage only that file.

---

## 9. Commit Changes

A commit records a snapshot of the staged changes in the local repository.

```bash
git commit -m "Add initial application file"
```

### Good Commit Messages

```bash
git commit -m "Add Docker installation notes"
git commit -m "Fix nginx service configuration"
git commit -m "Update Terraform VPC module"
```

### View Commit History

```bash
git log
git log --oneline
git log --oneline --graph --all
```

### Important Points

- A commit is stored locally.
- A commit includes the staged changes, not every unstaged edit.
- Each commit has a unique identifier called a hash.
- A commit message should explain the purpose of the change.

---

## 10. Understand Git Diff

`git diff` shows changes between versions of files.

```bash
git diff
```

Shows unstaged changes.

```bash
git diff --staged
```

Shows staged changes that will be included in the next commit.

```bash
git diff HEAD
```

Shows the difference between the working tree and the current commit, including staged and unstaged tracked-file changes.

This is useful before committing code.

---

## 11. Connect a Local Repository to GitHub

A remote repository is a copy of a Git repository hosted on another machine or platform.

### Step 1: Create a GitHub Repository

Create a new repository on GitHub, for example:

`devops-foundations`

If you already have a local project, creating the GitHub repository without an initial README, license, or `.gitignore` avoids unnecessary initial-history conflicts.

### Step 2: Add a Remote

Inside your local project:

```bash
git remote add origin https://github.com/YOUR_USERNAME/devops-foundations.git
```

Replace `YOUR_USERNAME` with your GitHub username.

### Step 3: Verify the Remote

```bash
git remote -v
```

Example output:

```text
origin  https://github.com/YOUR_USERNAME/devops-foundations.git (fetch)
origin  https://github.com/YOUR_USERNAME/devops-foundations.git (push)
```

### What Is `origin`?

`origin` is a conventional name for the default remote repository. It is a nickname, not a special Git command or a mandatory name.

### Change the Remote URL

```bash
git remote set-url origin https://github.com/YOUR_USERNAME/devops-foundations.git
```

---

## 12. Push Changes to GitHub

`git push` uploads local commits to a remote repository.

### Step 1: Check the Current Branch

```bash
git branch --show-current
```

### Step 2: Set the Main Branch Name

If you want the branch to be named `main`:

```bash
git branch -M main
```

### Step 3: Push the Changes

```bash
git push -u origin main
```

### What Does `-u` Mean?

The `-u` option sets the upstream relationship between your local branch and the remote branch.

After setting the upstream, future pushes can usually use:

```bash
git push
```

### GitHub Authentication

GitHub HTTPS operations typically require browser-based authentication, a credential manager, or a personal access token where appropriate. SSH authentication is another option.

Never commit passwords, access tokens, private keys, or other secrets to your repository.

---

## 13. What Is a Git Branch?

A branch is a movable reference to a commit. Branches let developers work on separate features or fixes without immediately changing the main development line.

### List Branches

```bash
git branch
```

List local and remote-tracking branches:

```bash
git branch -a
```

### Create a Branch

```bash
git branch feature-login
```

### Switch to a Branch

```bash
git switch feature-login
```

Create and switch in one command:

```bash
git switch -c feature-login
```

### Push a New Branch

```bash
git push -u origin feature-login
```

### Why Use Branches?

- Develop new features independently.
- Fix bugs without interrupting other work.
- Test changes before integrating them.
- Create a branch for a pull request.
- Reduce the risk of unreviewed changes reaching the main branch.

---

## 14. What Is `git pull`?

`git pull` fetches changes from a remote repository and integrates them into the current local branch.

```bash
git pull
```

You can also specify a remote and branch:

```bash
git pull origin main
```

By default, Git generally performs a fetch followed by a merge, although configuration can make pull use rebase instead.

### What Is `git fetch`?

`git fetch` downloads remote updates and updates remote-tracking references, but does not automatically integrate those changes into your current branch.

```bash
git fetch origin
```

Inspect the remote-tracking branch:

```bash
git log --oneline HEAD..origin/main
```

This shows commits reachable from `origin/main` that are not reachable from your current `HEAD`.

### `git fetch` vs `git pull`

| `git fetch` | `git pull` |
|---|---|
| Downloads remote updates | Downloads and integrates remote updates |
| Does not automatically merge into the current branch | Usually merges or rebases into the current branch |
| Useful for inspecting changes first | Useful when you are ready to integrate changes |

### Typical Workflow

```bash
git switch main
git pull origin main
```

If you have local uncommitted changes, check the status before pulling because those changes may interfere with integration.

---

## 15. What Is Git Merge?

Merging integrates changes from one branch into another.

Example:

```bash
git switch main
git merge feature-login
```

This merges the changes from `feature-login` into `main`.

### Types of Merge

**Fast-forward merge:** The target branch can move forward because it has no divergent commits.

**Three-way merge:** Git combines the histories of the target branch, the source branch, and their common ancestor. This may create a merge commit.

### Example Branch Workflow

```bash
git switch -c feature-login

# Make code changes
git add .
git commit -m "Add login feature"

git switch main
git merge feature-login
```

This example assumes the branches can be merged cleanly.

---

## 16. What Is a Merge Conflict?

A merge conflict occurs when Git cannot automatically determine how to combine changes.

For example, two developers edit the same lines in the same file differently.

Git marks the conflicting area:

```text
<<<<<<< HEAD
print("Hello from main")
=======
print("Hello from feature")
>>>>>>> feature-login
```

- `<<<<<<< HEAD`: Begins the current branch's version.
- `=======`: Separates the competing versions.
- `>>>>>>> feature-login`: Ends the incoming branch's version.

These markers are inserted into the file so you can resolve the conflict manually.

### How to Resolve a Merge Conflict

#### Step 1: Check the Status

```bash
git status
```

#### Step 2: Open the Conflicted File

```bash
vi app.py
```

Choose the correct content or combine both changes. Remove the conflict markers.

For example, the resolved content could be:

```python
print("Hello from the merged application")
```

#### Step 3: Stage the Resolved File

```bash
git add app.py
```

#### Step 4: Complete the Merge

```bash
git commit
```

Git usually prepares a merge commit message for you.

### Important

- Do not blindly choose one version without understanding the changes.
- Test the application after resolving the conflict.
- If a merge is in progress and you want to abandon it, use `git merge --abort` when supported by the current state of the merge.

---

## 17. What Is a Pull Request (PR)?

A pull request is a collaboration feature provided by Git hosting platforms such as GitHub.

It asks reviewers to inspect and discuss changes proposed from one branch into another before integration.

A pull request is not the same as `git pull`.

- **Pull request:** A review and integration request on GitHub.
- **`git pull`:** A local Git command that fetches and integrates remote changes.

### Typical Pull Request Workflow

1. Clone the repository.
2. Create a feature branch.
3. Make changes.
4. Stage and commit the changes.
5. Push the feature branch.
6. Open a pull request on GitHub.
7. Review the code and address feedback.
8. Run required CI checks.
9. Merge the pull request when approved.

### Commands

```bash
git clone https://github.com/YOUR_USERNAME/devops-foundations.git
cd devops-foundations

git switch -c feature/add-linux-notes

# Edit files

git add .
git commit -m "Add Linux learning notes"

git push -u origin feature/add-linux-notes
```

Then open GitHub in your browser and create a pull request from `feature/add-linux-notes` into `main`.

### Why Are Pull Requests Important?

- Enable code review.
- Help catch bugs and security problems.
- Provide a place for discussion and feedback.
- Integrate automated test results.
- Support protected branches and team approval rules.
- Improve traceability of changes.

---

## 18. What Is `git clone`?

`git clone` copies a remote repository to your local machine.

```bash
git clone https://github.com/YOUR_USERNAME/devops-foundations.git
```

Move into the project:

```bash
cd devops-foundations
```

Unlike `git init`, which creates a new local repository, `git clone` downloads an existing repository and configures its remote automatically.

---

## 19. What Is `.gitignore`?

`.gitignore` tells Git which untracked files and directories it should ignore.

Example `.gitignore`:

```gitignore
# Python
__pycache__/
*.pyc
.venv/

# Node.js
node_modules/

# Terraform
.terraform/
*.tfstate
*.tfstate.*
*.tfplan

# Environment files
.env

# Logs
*.log
```

### Why Use `.gitignore`?

- Avoid committing generated files.
- Keep local environment files out of version control.
- Prevent large dependency directories from being committed.
- Reduce the risk of accidentally exposing secrets.

**Important:** `.gitignore` does not automatically stop tracking files that Git already tracks. If a file is already tracked, you must remove it from the index separately, for example with `git rm --cached filename`.

---

## 20. Common Git Commands — Quick Reference

| Task | Command |
|---|---|
| Check Git version | `git --version` |
| Configure name | `git config --global user.name "Your Name"` |
| Initialize repository | `git init` |
| Check status | `git status` |
| Stage a file | `git add filename` |
| Stage all changes | `git add .` |
| Commit changes | `git commit -m "message"` |
| View history | `git log --oneline` |
| Show unstaged changes | `git diff` |
| Show staged changes | `git diff --staged` |
| Add a remote | `git remote add origin URL` |
| View remotes | `git remote -v` |
| Push a branch | `git push -u origin main` |
| Fetch updates | `git fetch origin` |
| Pull changes | `git pull origin main` |
| Create a branch | `git switch -c feature-name` |
| List branches | `git branch -a` |
| Merge a branch | `git merge feature-name` |
| Clone a repository | `git clone URL` |
| Rename current branch | `git branch -M main` |
| Abort a merge | `git merge --abort` |

---

## 21. Complete Git Workflow — From Initialization to GitHub

Use this workflow when starting a new local project.

```bash
# 1. Create a project
mkdir git-practice
cd git-practice

# 2. Initialize Git
git init

# 3. Configure identity (if not already configured)
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"

# 4. Create a file
echo "# Git Practice" > README.md

# 5. Check status
git status

# 6. Stage changes
git add README.md

# 7. Review staged changes
git diff --staged

# 8. Commit changes
git commit -m "Add initial README"

# 9. Set the branch name
git branch -M main

# 10. Connect your GitHub repository
git remote add origin https://github.com/YOUR_USERNAME/git-practice.git

# 11. Push to GitHub
git push -u origin main
```

Replace the example GitHub username and repository URL with your actual repository details.

For future changes:

```bash
# Edit files

git status
git diff
git add .
git diff --staged
git commit -m "Update project documentation"
git push
```

---

## 22. Git in a DevOps Workflow

Git is a foundational component of the software delivery process.

```text
Developer writes code
        |
        v
Git working directory
        |
        v
Stage changes (git add)
        |
        v
Commit changes (git commit)
        |
        v
Push feature branch (git push)
        |
        v
Create Pull Request
        |
        v
Code Review + CI Tests
        |
        v
Merge into main
        |
        v
CI/CD Pipeline
        |
        v
Build, Test, Package and Deploy
```

### Why Git Matters for DevOps Engineers

- Source code version control.
- Infrastructure-as-Code version control.
- Terraform configuration management.
- Dockerfile and Kubernetes manifest management.
- Collaboration through branches and pull requests.
- Triggering CI/CD workflows.
- Tracking changes and supporting rollbacks.

---

## 23. Key Takeaways

- Git is a distributed version control system.
- GitHub hosts Git repositories and supports collaboration.
- `git init` initializes a repository.
- `git add` stages changes.
- `git commit` records changes locally.
- `git push` uploads commits to a remote repository.
- `git pull` fetches and integrates remote changes.
- `git fetch` downloads updates without integrating them into the current branch.
- Branches allow independent development.
- Merge conflicts require reviewing and resolving competing changes.
- Pull requests enable code review and controlled integration.
- `.gitignore` helps exclude untracked files from version control.
- Git is essential for DevOps, Infrastructure as Code, and CI/CD workflows.