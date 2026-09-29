# Undoing Changes Safely

## The Hierarchy of Regret

Undoing work in Git depends entirely on where the change resides. The further a change has progressed through the workflow, the more complex and risky the reversal becomes. Understand the state of your changes before selecting a recovery method.

**Uncommitted working directory changes**: The safest to undo. Files are modified on disk but not staged. Git has not recorded them. You can discard these changes freely without affecting history.

**Staged but uncommitted changes**: Files are added to the index but not committed. They exist in the object database as blobs but have no commit pointer. Reversing this requires removing files from the staging area while optionally preserving or discarding the working directory modifications.

**Committed locally**: Changes are part of the repository history but not yet pushed to a remote. You can rewrite this history safely because no one else depends on it. Amending, resetting, or rebasing works without coordination.

**Pushed to remote**: Changes are shared. Other developers may have based work on them. Rewriting history here is dangerous. It forces teammates to reconcile their local copies with your rewritten timeline. Use revert operations that create new corrective commits rather than erasing existing ones.

## Discarding Uncommitted Changes

Restore a specific file to its last committed state. This overwrites your local modifications permanently. There is no confirmation prompt. Ensure you do not need the changes before executing.

```bash
git checkout -- path/to/file.py
```

In newer Git versions, `restore` replaces `checkout` for this purpose to reduce ambiguity.

```bash
git restore path/to/file.py
```

Discard all uncommitted changes in the working directory. This resets every modified file to match the HEAD commit. Use with extreme caution.

```bash
git restore .
```

## Unstaging Changes

Remove files from the staging area while keeping modifications in the working directory. This reverses `git add`. Your edits remain intact for further refinement or selective staging later.

```bash
git reset HEAD path/to/file.py
```

Unstage all files at once. This clears the entire index but leaves working directory files untouched.

```bash
git reset HEAD
```

## Amending the Last Commit

Fix the most recent commit by adding forgotten files or correcting the message. This replaces the previous commit entirely with a new one containing updated content. The old commit hash becomes invalid.

```bash
git add forgotten_file.py
git commit --amend -m "Corrected message"
```

Do not amend commits already pushed to shared branches. The rewritten hash breaks synchronization for teammates who have pulled the original version.

## Resetting Local History

Move the branch pointer backward to discard recent commits. The mode determines what happens to the index and working directory.

**Soft reset**: Moves HEAD only. Changes remain staged. Useful for squashing commits while preserving the staging area.

```bash
git reset --soft HEAD~1
```

**Mixed reset**: Moves HEAD and resets the index. Changes remain in the working directory but are unstaged. Common for reworking recent commits.

```bash
git reset HEAD~1
```

**Hard reset**: Moves HEAD, resets the index, and overwrites the working directory. All changes after the target commit vanish permanently. Dangerous but effective for starting fresh from a known point.

```bash
git reset --hard HEAD~1
```

## Reverting Shared Commits

Create a new commit that undoes the changes introduced by a previous commit. This preserves history while correcting mistakes. Safe for shared branches because it does not rewrite existing commits.

```bash
git revert <commit-hash>
```

Revert handles conflicts if the original changes overlap with subsequent modifications. Resolve conflicts normally and complete the revert commit. This approach provides an audit trail showing both the error and its correction.

## The Reflog Safety Net

Git records every movement of HEAD in the reflog. This includes commits, resets, rebases, and checkouts. The reflog exists locally and is not shared via push or pull. It enables recovery from almost any mistake involving lost commits.

View the reflog to find where HEAD used to be.

```bash
git reflog
```

Restore a lost commit by resetting to its position in the reflog.

```bash
git reset --hard HEAD@{n}
```

The reflog expires entries after ninety days by default. Act quickly if you realize a mistake. Do not rely on reflog for long-term backup. Use it as an immediate recovery tool.

## Best Practices

Test after undoing changes. Reverting or resetting may leave the codebase in an unexpected state. Run tests to verify correctness. For data pipelines, validate output schemas and row counts to ensure no silent corruption occurred during the reversal.

Communicate when rewriting shared history. If you must force push after a local reset, notify teammates immediately. Provide instructions for recovering their local branches. Coordination minimizes disruption.

Prefer revert over reset for public branches. Revert is transparent. Reset is destructive. Transparency builds trust. Destruction creates confusion. Choose the method that preserves team velocity and historical clarity.
