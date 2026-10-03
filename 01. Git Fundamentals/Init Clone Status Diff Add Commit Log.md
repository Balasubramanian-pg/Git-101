# Init, Clone, Status, Diff, Add, Commit, Log

## Initializing a Repository

`git init` creates a new repository in the current directory. 
>[!Note]
>It generates the `.git` subdirectory containing all metadata, object storage, and configuration files.

- The working directory remains unchanged. 
- No files are tracked yet.
- This command is **idempotent**.
- Running it again in an existing repository **reinitializes** configuration but does not destroy data.

>[!Tip]
>Use this when starting a new project from scratch or converting an unversioned directory into a Git repository.

```bash
mkdir my-project
cd my-project
git init
```

## Cloning an Existing Repository

`git clone` copies a remote repository to your local machine. 
- It downloads all history, branches, and tags.
- It creates a working directory with the latest version of the default branch checked out.
- It configures a remote named `origin` pointing to the source URL.

```bash
git clone https://github.com/user/repo.git
```

Cloning is distinct from downloading a zip archive. 
- A clone includes the **full object database** allowing offline history inspection and **branch creation**.
- Specify a **directory name** as the **second argument** to clone into a different folder.

```bash
git clone https://github.com/user/repo.git custom-name
```

## Checking Status

`git status` displays the state of the working directory and staging area. 
- It categorizes files into three groups:
  - untracked,
  - modified but unstaged,
  - and staged for commit.
- It shows which branch you are on and whether your local branch is ahead or behind the remote.

Run this frequently. 
- It provides **immediate feedback on what Git sees versus what you expect**. 
- If a file you modified does not appear in status output, check if it is ignored via `.gitignore`.

```bash
git status
```

## Viewing Differences

`git diff` compares changes between different states. Without arguments, it shows modifications in the working directory that are not yet staged. This helps verify exactly what changed before adding files.

```bash
git diff
```

To see changes already staged for the next commit, use the `--cached` or `--staged` flag. This compares the index against the last commit.

```bash
git diff --cached
```

Compare specific commits by providing their hashes. Compare branches by naming them. Limit output to specific files by appending paths.

```bash
git diff HEAD~1 HEAD
git diff main feature-branch
git diff -- path/to/file.py
```

## Staging Changes

`git add` moves changes from the working directory to the staging area. It snapshots the current content of specified files. Subsequent modifications to those files do not affect the staged version until you run add again.

Add specific files for precision. Use `.` to stage all changes in the current directory. Use `-A` or `--all` to stage all changes in the entire repository including deletions.

```bash
git add file.py
git add .
git add -A
```

Interactive staging via `git add -p` allows selecting individual hunks within a file. This enables committing related changes separately even if they exist in the same file. Useful for separating bug fixes from refactoring done in the same session.

## Creating Commits

`git commit` records the staged snapshot permanently in the repository history. It creates a commit object with author information, timestamp, and a message. The message should explain why the change was made, not just what changed. The code diff shows what. The message explains context.

Provide the message inline with `-m`. Omitting this flag opens the default editor for multi-line messages.

```bash
git commit -m "Fix null handling in user transformation"
```

Amend the most recent commit with `--amend`. This replaces the previous commit entirely. Use it to fix typos in messages or include forgotten files. Never amend commits already pushed to shared branches.

```bash
git commit --amend -m "Updated message"
```

## Reviewing History

`git log` displays commit history. By default, it shows commits reachable from the current HEAD in reverse chronological order. Each entry includes hash, author, date, and message.

Limit output with `-n` to show only the most recent commits. Use `--oneline` for compact display showing one commit per line. Use `--graph` to visualize branch topology.

```bash
git log -n 5
git log --oneline
git log --graph --oneline --all
```

Filter by author, date range, or file path. Search commit messages with `--grep`. These filters help locate specific changes in large histories.

```bash
git log --author="Balu"
git log --since="2 weeks ago"
git log -- path/to/file.py
```
