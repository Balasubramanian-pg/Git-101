# Git Rebase

<img width="1408" height="768" alt="image" src="https://github.com/user-attachments/assets/f00af458-9d08-4c5f-84f6-713cfb5169a6" />

## The Concept

Rebase rewrites history. It takes a series of commits from one branch and replays them on top of another branch. Unlike merge, which creates a new commit linking two histories, rebase moves the entire feature branch to begin at the tip of the target branch. The result is a linear history where it appears the work was done sequentially rather than in parallel.

This operation changes commit hashes. Since each commit’s hash depends on its parent’s hash, replaying commits generates new identifiers. Never rebase branches that others are using. Rewriting shared history forces teammates to reconcile their local copies with your new history, causing confusion and potential data loss.

## Basic Usage

Switch to the feature branch you want to rebase. Specify the target branch, typically main or develop.

```bash
git checkout feature/user-auth
git rebase main
```

Git identifies the common ancestor between the two branches. It saves the commits unique to your feature branch. It resets your branch to the tip of main. Then it applies each saved commit one by one. If a commit applies cleanly, Git moves to the next. If a conflict arises, Git pauses for resolution.

## Handling Conflicts During Rebase

<img width="1408" height="768" alt="image" src="https://github.com/user-attachments/assets/21166102-679a-407b-b148-6aa5b6b4c4c5" />

Conflicts occur when the target branch modified the same lines as your feature branch. Git stops at the conflicting commit. Resolve the conflict in your editor. Stage the resolved files. Continue the rebase.

```bash
# After resolving conflicts
git add .
git rebase --continue
```

If the conflict is too complex or you decide to abandon the rebase, abort the operation. This returns your branch to its pre-rebase state.

```bash
git rebase --abort
```

Skipping a commit is also possible if it is no longer relevant or causes unresolvable issues.

```bash
git rebase --skip
```

## Interactive Rebase

<img width="1408" height="768" alt="image" src="https://github.com/user-attachments/assets/9628f35e-647e-4ed9-8461-98a534d97be4" />

Interactive rebase allows you to modify commits during the replay process. Use it to clean up history before sharing work. Squash multiple small commits into one. Edit commit messages. Rearrange commit order. Delete unnecessary commits.

```bash
git rebase -i HEAD~3
```

This opens an editor listing the last three commits. Change the command prefix for each commit. `pick` keeps the commit as is. `squash` merges it with the previous commit. `edit` pauses for manual changes. `drop` removes the commit entirely. Save and close the editor to execute the plan.

## When to Use Rebase

**Before opening a pull request**: Rebase your feature branch onto the latest main. This ensures your PR starts from a current baseline. It reduces merge conflicts during review and makes the diff easier to understand since it does not include unrelated changes from other developers.

**Cleaning up local history**: Squash fixup commits like "typo" or "wip" into meaningful logical units. A clean history helps reviewers understand the evolution of the code without noise.

**Maintaining linear history**: Teams preferring straight-line logs use rebase exclusively for integration. This avoids merge commits cluttering the history. Tools like `git bisect` work more effectively on linear histories.

## When to Avoid Rebase

**Shared branches**: Never rebase main, develop, or any branch multiple people commit to. The rewritten history breaks synchronization for everyone else. Use merge for integrating shared branches.

**After pushing**: If you have already pushed commits to a remote repository, rebasing requires force pushing to update the remote. This disrupts teammates who have based work on your original commits. Coordinate carefully if rebasing pushed branches is absolutely necessary.

**Complex dependency chains**: If multiple feature branches depend on each other, rebasing one requires rebasing all downstream branches. This cascading effect increases complexity and risk of errors.

## Best Practices

Rebase frequently. Small, incremental rebases are easier to resolve than large ones accumulated over weeks. Fetch the target branch often to stay aware of changes.

Test after rebasing. Although rebase preserves code content, the context changes. Run tests to ensure the rebased commits work correctly with the new base.

Use `git log` to verify the result. Check that commits appear in the expected order and that no unintended changes were introduced.

## Recovery

If a rebase goes wrong, use the reflog to find the pre-rebase state. The reflog records every change to HEAD, including the position before the rebase started.

```bash
git reflog
git reset --hard HEAD@{n}
```

This restores your branch to exactly where it was before the rebase began. No work is lost unless you explicitly deleted commits during an interactive rebase.
