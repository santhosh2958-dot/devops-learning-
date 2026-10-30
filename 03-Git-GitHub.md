# 03 — Git and GitHub for DevOps

## 1. What Is Git?

**Git** is a version control system. It tracks changes made to files so you can see what changed, when it changed, and who made the change.

For example, if you update your Linux notes today and accidentally delete something tomorrow, Git can help you review or restore earlier versions.

### What Is GitHub?

**GitHub** is an online platform for hosting Git repositories. It lets you store code, collaborate with others, review changes, and manage projects.

| Git | GitHub |
|---|---|
| Version control software | Online platform for Git repositories |
| Works locally on your computer | Hosts repositories online |
| Tracks file changes | Enables collaboration and sharing |
| Works without an internet connection for local operations | Requires network access for remote operations |
| Commands include `git add` and `git commit` | Common actions include pull requests and code reviews |

**Remember:** Git is the tool. GitHub is a platform that hosts Git repositories.

---

## 2. Important Git Terminology

| Term | Meaning |
|---|---|
| Repository (repo) | A project tracked by Git |
| Working directory | The files you are currently editing |
| Staging area | The changes selected for the next commit |
| Commit | A saved snapshot of staged changes |
| Branch | An independent line of development |
| Main branch | The primary branch, often named `main` |
| Remote | A reference to a repository hosted elsewhere |
| `origin` | The conventional name for the default remote |
| Clone | Copy a remote repository to your computer |
| Push | Send local commits to a remote repository |
| Pull | Fetch remote changes and integrate them into your current branch |
| Fetch | Download remote Git information without integrating it into your current branch |
| Merge | Combine changes from one branch into another |
| Pull request (PR) | A request to review and merge proposed changes on platforms such as GitHub |
| `.git` | Hidden directory containing a repository's Git metadata |

---

## 3. Install Git on Ubuntu

Check whether Git is installed:

```bash
git --version
```

If Git is missing:

```bash
sudo apt update
sudo apt install git
```

Verify again:

```bash
git --version
```

Example output:

```text
git version 2.x.x
```

Your version may be different.

---

## 4. Configure Your Git Identity

Git records an author name and email address in commits.

Set your name:

```bash
git config --global user.name "Your Name"
```

Set your email:

```bash
git config --global user.email "your-email@example.com"
```

Replace these example values with the identity you want associated with your commits.

Check your configuration:

```bash
git config --global --list
```

Check individual settings:

```bash
git config user.name
git config user.email
```

### Important

- `--global` applies to your user account across repositories.
- Without `--global`, configuration can be set for the current repository.
- Use an email appropriate for your GitHub account. GitHub also provides a private email option if you do not want to expose your personal email in commits.

---

## 5. Create a Git Repository

Create a project directory:

```bash
mkdir git-practice
cd git-practice
```

Initialize Git:

```bash
git init
```

This creates a local Git repository.

Check the current status:

```bash
git status
```

List all files, including hidden files:

```bash
ls -la
```

You should see a `.git` directory.

### Important commands

```bash
pwd
```

Shows your current directory.

```bash
ls
```

Lists visible files.

```bash
ls -la
```

Lists files, including hidden files.

```bash
cd ..
```

Moves to the parent directory.

---

## 6. Understand the Git Workflow

The usual Git workflow is:

<escape>
Working Directory → Staging Area → Local Repository → Remote Repository
</escape>

1. **Working directory:** Create or edit files.
2. **Staging area:** Select changes using `git add`.
3. **Local repository:** Save a snapshot using `git commit`.
4. **Remote repository:** Share commits using `git push`.

### Example

```bash
echo "Learning Git" > notes.txt
git status
git add notes.txt
git status
git commit -m "Add Git learning notes"
git status
```

What each command does:

- `echo` creates text.
- `>` writes the text to `notes.txt`, replacing existing contents if the file already exists.
- `git status` shows the current state.
- `git add` stages the file.
- `git commit` saves the staged changes in Git history.

**Remember:** `git add` does not create a commit. `git commit` does not automatically push changes to GitHub.

---

## 7. Essential Git Commands

### Check repository status

```bash
git status
```

Shows changed, staged, untracked, and other relevant file states.

Shorter output:

```bash
git status -sb
```

### View commit history

```bash
git log
```

Compact history:

```bash
git log --oneline
```

Show branches and graph:

```bash
git log --oneline --graph --all
```

### View changes

Unstaged changes:

```bash
git diff
```

Changes staged for the next commit:

```bash
git diff --staged
```

### Stage changes

Stage one file:

```bash
git add README.md
```

Stage all changes under the current directory:

```bash
git add .
```

### Commit changes

```bash
git commit -m "Update Linux notes"
```

The message should describe what you changed.

### View tracked files

```bash
git ls-files
```

This lists files tracked by the current repository.

---

## 8. Git Shortcuts and Common Options

These are command options and shorter forms, not necessarily separate commands.

| Command | Meaning |
|---|---|
| `git status -s` | Short status output |
| `git status -sb` | Short status with branch information |
| `git log --oneline` | One-line commit history |
| `git log --oneline --graph --all` | Compact history graph across branches |
| `git diff --staged` | Show staged changes |
| `git branch` | List local branches |
| `git branch -a` | List local and remote-tracking branches |
| `git switch -c feature-name` | Create and switch to a branch |
| `git switch main` | Switch to the `main` branch |
| `git restore file.txt` | Discard unstaged changes in a tracked file |
| `git restore --staged file.txt` | Unstage a file while keeping its working-directory changes |
| `git show` | Show details of a commit or Git object |
| `git remote -v` | Show remote repository URLs |

### Useful shell shortcuts

These are terminal shortcuts rather than Git commands:

| Shortcut | Purpose |
|---|---|
| `Tab` | Complete a filename or command |
| `↑` / `↓` | Browse command history |
| `Ctrl + C` | Interrupt a running command |
| `Ctrl + L` | Clear the terminal display |
| `Ctrl + R` | Search command history in many Bash configurations |

---

## 9. Connect a Local Repository to GitHub

You can create a new GitHub repository on the GitHub website and connect it to a local repository.

Add a remote:

```bash
git remote add origin https://github.com/USERNAME/REPOSITORY.git
```

Replace `USERNAME` and `REPOSITORY` with your actual GitHub username and repository name.

Check the remote:

```bash
git remote -v
```

If the remote already exists, update it instead of adding a duplicate:

```bash
git remote set-url origin https://github.com/USERNAME/REPOSITORY.git
```

### Push your first commit

If your current branch is `main` and you have committed changes:

```bash
git push -u origin main
```

The `-u` option sets the upstream tracking branch, making future pushes and pulls simpler.

After the upstream is configured, you can often use:

```bash
git push
```

### Authentication

For HTTPS, GitHub generally requires a supported authentication method, such as Git Credential Manager or a personal access token where applicable. Your normal GitHub account password is not accepted for Git operations over HTTPS.

Never paste passwords, access tokens, or private SSH keys into a repository or a public chat.

---

## 10. Clone an Existing GitHub Repository

Cloning downloads a remote repository and creates a local working copy.

```bash
git clone https://github.com/USERNAME/REPOSITORY.git
```

Enter the repository directory:

```bash
cd REPOSITORY
```

Check the remote:

```bash
git remote -v
```

Check the current branch:

```bash
git branch --show-current
```

### Example for your DevOps notes

```bash
git clone https://github.com/santhosh2958-dot/devops-learning-.git
```

If the repository is already cloned on your computer, do not clone it again into the same directory. Move into the existing repository folder instead.

---

## 11. The Daily GitHub Update Workflow

Use this workflow when updating your DevOps notes.

### Step 1: Enter the repository

```bash
cd devops-learning-
```

Run this from the directory containing your repository, or use the full path to the repository on your computer.

### Step 2: Check the current state

```bash
git status
```

### Step 3: Get the latest changes

```bash
git pull
```

If Git reports unrelated histories, merge conflicts, or a missing upstream, stop and investigate rather than forcing the operation.

### Step 4: Edit your file

For example, edit:

```text
02-Networking.md
```

### Step 5: Review the changes

```bash
git diff
```

### Step 6: Stage the file

```bash
git add 02-Networking.md
```

### Step 7: Commit the changes

```bash
git commit -m "Update networking notes"
```

### Step 8: Push to GitHub

```bash
git push
```

### Step 9: Verify

```bash
git status
```

If your branch is synchronized with its upstream and there are no pending changes, Git should indicate that the working tree is clean and the branch is up to date.

---

## 12. Git Branches

A branch lets you work on changes separately from the main line of development.

List local branches:

```bash
git branch
```

List local and remote-tracking branches:

```bash
git branch -a
```

Create a branch:

```bash
git branch feature-notes
```

Switch to a branch:

```bash
git switch feature-notes
```

Create and switch in one command:

```bash
git switch -c feature-notes
```

Return to main:

```bash
git switch main
```

Delete a merged local branch:

```bash
git branch -d feature-notes
```

Use `-D` only when you deliberately want to force-delete a branch that Git considers unmerged:

```bash
git branch -D feature-notes
```

**Important:** Confirm the branch name before switching or deleting. A repository may use `master`, `main`, or another default branch name.

---

## 13. Merge Branches

A merge combines changes from one branch into another.

Example:

```bash
git switch main
git merge feature-notes
```

This merges `feature-notes` into the current `main` branch.

Before merging, check:

```bash
git status
```

Commit or safely handle your current changes first.

If a merge creates a conflict, Git pauses and asks you to resolve it.

---

## 14. Merge Conflicts

A merge conflict happens when Git cannot automatically combine competing changes.

A conflicting section may look like this:

```text
<<<<<<< HEAD
This is the version in the current branch.
=======
This is the incoming version.
>>>>>>> feature-notes
```

### How to resolve a conflict

1. Run `git status` to identify conflicted files.
2. Open each conflicted file.
3. Decide which content should remain.
4. Remove the conflict markers.
5. Save the corrected file.
6. Stage the resolved file.
7. Complete the merge commit if Git requires it.

Example:

```bash
git status
```

After editing the conflicted file:

```bash
git add README.md
git status
```

If you were performing a merge and Git says it still needs a commit:

```bash
git commit
```

Git may open an editor for the merge message. Follow the instructions displayed in the terminal.

### Cancel a merge

If a merge is in progress and you want to abandon it, you may use:

```bash
git merge --abort
```

This is not a universal undo command. Review the state of your working tree first, especially if you had uncommitted changes before starting the merge.

---

## 15. Undo Changes Safely

Undo commands can permanently discard work. Understand the difference before using them.

### Discard unstaged changes in one tracked file

```bash
git restore README.md
```

This restores the file to its index version, discarding its unstaged edits.

### Unstage a file

```bash
git restore --staged README.md
```

This removes the file from the staging area but keeps the working-directory changes.

### Undo the latest commit by creating a new commit

```bash
git revert HEAD
```

This creates a new commit that reverses the changes introduced by the selected commit. It is often a safer choice for commits already shared with others.

### Reset the latest commit but keep changes staged

```bash
git reset --soft HEAD~1
```

This moves the current branch pointer back one commit while keeping the changes staged.

### Important warning about reset

```bash
git reset --hard HEAD
```

This discards tracked working-directory and staged changes that differ from `HEAD`. It can destroy work.

Avoid using `git reset --hard`, `git clean -fd`, or force-pushing unless you understand exactly what will be deleted or rewritten.

---

## 16. `.gitignore` — Exclude Files from Git

A `.gitignore` file specifies untracked files and paths that Git should ignore.

Create one:

```bash
touch .gitignore
```

Example `.gitignore` content:

```gitignore
# Python cache
__pycache__/
*.pyc

# Virtual environments
.venv/
venv/

# Environment variables and secrets
.env
.env.*

# Log files
*.log

# Temporary files
*.tmp
```

### Important notes

- Patterns are matched relative to the `.gitignore` location, with rules for directory paths and wildcards.
- `.gitignore` does not automatically stop tracking files that are already tracked.
- Never commit passwords, API tokens, SSH private keys, or other secrets.

If a file is already tracked and should no longer be tracked, first ensure it is safely backed up and that you understand the consequences. To remove it from Git's index while leaving your local file in place:

```bash
git rm --cached filename
```

Then commit the change and add an appropriate ignore rule.

If a secret was committed, removing the file from the latest version is not sufficient to erase it from history. Revoke or rotate the secret immediately.

---

## 17. Git Remotes and Branch Tracking

Show remote names and URLs:

```bash
git remote -v
```

Show the current branch:

```bash
git branch --show-current
```

Show upstream tracking information:

```bash
git branch -vv
```

Download remote changes without integrating them:

```bash
git fetch
```

Fetch and integrate changes for the current branch:

```bash
git pull
```

Send local commits to the remote:

```bash
git push
```

If a branch has no upstream, you may need to specify it:

```bash
git push -u origin main
```

Use your actual branch name if it is not `main`.

### Difference between fetch and pull

- `git fetch` downloads remote information but does not merge it into your current branch.
- `git pull` fetches and then integrates changes, usually by merge or rebase depending on configuration.

---

## 18. Git Command Symbols and Syntax

| Syntax | Meaning |
|---|---|
| `-m` | Option commonly used by `git commit` to provide a message |
| `--global` | Apply Git configuration across repositories for your user |
| `--staged` | Refer to changes in the staging area for commands that support it |
| `.` | Current directory; in `git add .`, stage eligible changes under the current directory |
| `..` | Parent directory in shell paths |
| `HEAD` | Name referring to the current checked-out commit or position |
| `HEAD~1` | First parent of `HEAD` |
| `origin` | Conventional remote name |
| `main` | Common primary branch name |
| `*` | In `.gitignore`, wildcard matching zero or more characters in a pattern |
| `#` | Comment marker in `.gitignore` |

Examples:

```bash
git add .
git commit -m "Add Linux commands"
git log --oneline
git reset --soft HEAD~1
```

Do not confuse shell symbols such as `|`, `>`, and `&&` with Git-specific options. They are interpreted by the shell.

---

## 19. Troubleshooting Common Git Errors

| Error | Likely reason | What to do |
|---|---|---|
| `fatal: not a git repository` | You are outside a Git repository | Use `pwd`, `ls -la`, and move into the correct directory |
| `Author identity unknown` | Name or email is not configured | Set `git config user.name` and `git config user.email` |
| `remote origin already exists` | A remote named `origin` already exists | Check `git remote -v`; use `git remote set-url` if needed |
| `src refspec main does not match any` | The branch name is wrong or no commit exists yet | Run `git branch --show-current` and `git status`; create a commit if needed |
| `rejected (non-fast-forward)` | Remote contains commits not in your local branch | Fetch and integrate the remote changes before pushing |
| `Permission denied (publickey)` | SSH authentication failed | Check the username, key, SSH configuration, and GitHub access |
| `Authentication failed` | HTTPS credentials or authentication method is invalid | Re-authenticate using a supported method |
| `CONFLICT` | Git cannot automatically combine changes | Edit the conflicted files, stage the resolutions, and finish the operation |
| `nothing to commit` | No new staged changes exist | Run `git status` and check whether files were saved or ignored |
| `Changes not staged for commit` | Tracked files were edited but not staged | Use `git add filename` |
| `Untracked files` | New files are not yet tracked | Use `git add filename` if they belong in the repository |
| `Could not resolve host` | Network or DNS problem | Check network connectivity and DNS |
| `Repository not found` | URL is incorrect or you lack access | Check the repository URL and permissions |

### Troubleshooting sequence

When something goes wrong, start with:

```bash
pwd
git status
git branch --show-current
git remote -v
git log --oneline -5
```

Read the error carefully before trying commands that rewrite history or delete files.

---

## 20. Practical Exercises

Complete these exercises in a test repository first.

### Exercise 1: Create and commit a file

```bash
mkdir git-exercise
cd git-exercise
git init
git config user.name "Your Name"
git config user.email "your-email@example.com"
echo "My first Git practice" > practice.txt
git status
git add practice.txt
git commit -m "Add practice file"
git log --oneline
```

### Exercise 2: Modify a file

```bash
echo "Learning DevOps every day" >> practice.txt
git diff
git status
git add practice.txt
git diff --staged
git commit -m "Update practice notes"
git log --oneline
```

### Exercise 3: Practise branches

```bash
git switch -c feature-practice
echo "Practising branches" >> practice.txt
git add practice.txt
git commit -m "Practise feature branch"
git switch main
git merge feature-practice
git log --oneline --graph --all
```

If your initial branch is not named `main`, use the branch name shown by:

```bash
git branch --show-current
```

### Exercise 4: Practise `.gitignore`

```bash
echo "*.tmp" > .gitignore
touch sample.tmp
git status
```

Check whether `sample.tmp` is ignored. If it is ignored and untracked, it should not appear as an ordinary untracked file in `git status`.

### Exercise 5: Connect to GitHub

1. Create a test repository on GitHub.
2. Configure your Git identity.
3. Add a remote using the repository URL.
4. Commit a test file.
5. Push the branch.
6. Refresh GitHub and confirm the file is present.

---

## 21. Daily Git Checklist

Before you finish work, run:

```bash
git status
git diff
git diff --staged
git log --oneline -5
```

Before pushing:

```bash
git remote -v
git branch --show-current
git status
git push
```

If the push fails, read the error and investigate instead of immediately using `--force`.

---

## 22. Quick Revision Sheet

| Task | Command |
|---|---|
| Check Git version | `git --version` |
| Configure username | `git config --global user.name "Your Name"` |
| Configure email | `git config --global user.email "you@example.com"` |
| Initialize repository | `git init` |
| Check status | `git status` |
| Stage a file | `git add filename` |
| Stage eligible changes under current directory | `git add .` |
| Commit staged changes | `git commit -m "Message"` |
| View short history | `git log --oneline` |
| View unstaged changes | `git diff` |
| View staged changes | `git diff --staged` |
| Clone repository | `git clone URL` |
| Check remote | `git remote -v` |
| Fetch remote changes | `git fetch` |
| Pull changes | `git pull` |
| Push commits | `git push` |
| List branches | `git branch` |
| Create and switch branch | `git switch -c branch-name` |
| Switch branch | `git switch branch-name` |
| Merge a branch | `git merge branch-name` |
| Unstage a file | `git restore --staged filename` |
| Revert a commit | `git revert COMMIT_ID` |
| Check current branch | `git branch --show-current` |

---

## 23. Official Documentation

- Git documentation: https://git-scm.com/doc
- Git book: https://git-scm.com/book/en/v2
- GitHub documentation: https://docs.github.com/
- GitHub SSH authentication: https://docs.github.com/en/authentication/connecting-to-github-with-ssh
- GitHub repository basics: https://docs.github.com/en/repositories

**Learning order:** First practise `status`, `add`, `commit`, `log`, `diff`, `pull`, and `push`. Then learn branches, merging, conflicts, `.gitignore`, and safe ways to undo changes.
