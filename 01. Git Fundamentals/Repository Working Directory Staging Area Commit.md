# Repository, Working Directory, Staging Area, Commit

## The Three States

Git tracks files in three distinct states. 
- Understanding these states prevents confusion about what is saved, what is pending, and what is ignored. 
- Most Git errors stem from misunderstanding which state a file occupies at any given moment.

>[!Note]
> ### Working Directory:
>- The files you **see and edit on your disk**.<br>
>- This is your **sandbox**.<br>
>- Changes here are **local and volatile**.<br>
>- Git does not track them until you explicitly add them.<br>
>- If you delete a file in the working directory without staging or committing, it is gone unless you have backups.

>[!Note]
> ### Staging Area (Index):
>- A hidden file inside `.git/index` that acts as a preview of the next commit.
>- It holds snapshots of files you have marked for inclusion.
>- The staging area allows you to construct a commit selectively.
>- You can modify ten files but stage only three.
>- The other seven remain in the working directory, excluded from the upcoming commit.

>[!Note]
> ### Repository (HEAD):
>- The permanent history stored in `.git/objects`.
>- Commits here are immutable.
>- Once recorded, they cannot be changed without rewriting history.
>- The repository represents the last saved state of your project.

## The Workflow Cycle

Work begins in the working directory. You create or modify files. Git sees these as untracked or modified. You select which changes matter by adding them to the staging area. This action copies the current file content into Git's object database and updates the index. Finally, you commit the staged snapshot. This creates a permanent record pointing to the staged files.

```
Working Directory --[git add]--> Staging Area --[git commit]--> Repository
```

This separation provides control. You decide exactly what constitutes a logical unit of work. A single file modification might contain a bug fix and a refactor. Stage the bug fix first, commit it, then stage the refactor and commit separately. This creates a clean history where each commit has a single purpose.

## Visualizing State

`git status` shows the relationship between these three states. It lists files that are untracked, modified but unstaged, and staged. It tells you which branch you are on and whether your local branch is ahead or behind the remote.

`git diff` compares the working directory against the staging area. It shows changes you have made but not yet staged. `git diff --cached` compares the staging area against the last commit. It shows what will be included in the next commit.

## Common Confusions

**Modified but not staged**: You edited a file but did not run `git add`. Git knows the file changed but will not include it in the next commit. Running `git commit` without staging results in an empty commit or a message saying nothing to commit.

**Staged but not committed**: You ran `git add` but not `git commit`. The changes are safe in the object database but not yet part of the permanent history. If you modify the file again after staging, the new modifications are not staged. You must run `git add` again to update the snapshot.

**Tracked vs Untracked**: Git only cares about files it has been told to track. New files are untracked until added. Deleted files are tracked until the deletion is staged. Ignored files are invisible to Git entirely.

## Practical Implications

The staging area enables partial commits. You can split a large change into smaller, focused commits. This improves reviewability and makes reverting specific changes easier. It also allows you to save work-in-progress without committing incomplete logic. Stage what is ready, leave the rest in the working directory.

Amending commits replaces the most recent commit with a new one containing updated staging area content. This is useful for fixing typos in messages or including forgotten files. It rewrites history, so use it only on local commits not yet shared.

Understanding these states transforms Git from a mysterious black box into a predictable tool. You always know where your changes are and what actions are required to move them forward.
