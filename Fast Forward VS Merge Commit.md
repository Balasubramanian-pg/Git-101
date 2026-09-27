# Fast-Forward vs Merge Commit

## The Mechanical Difference

Git integrates branches in two distinct ways. Understanding which occurs and why matters for history clarity.

**Fast-forward** happens when the target branch has no new commits since the feature branch diverged. Git simply moves the target branch pointer forward to match the feature branch. No new commit is created. The history remains linear because no actual merging occurred. The branches were never truly separate in terms of commit content.

**Merge commit** occurs when both branches have advanced independently. Git creates a new commit with two parents: one from each branch. This preserves the fact that parallel development happened. The history shows a fork and rejoin pattern.

## When Each Occurs

Fast-forward is automatic when you branch from main, make commits, and no one else pushes to main during your work. The moment another developer merges their work to main while you are developing, fast-forward becomes impossible. Your branches have diverged. Git must create a merge commit to reconcile the two lines of history.

You can force a merge commit even when fast-forward is possible using the `--no-ff` flag. This creates an explicit integration point in history regardless of whether it is technically necessary.

```bash
git merge --no-ff feature/user-auth
```

Conversely, you can prevent merge commits and force fast-forward only by using `--ff-only`. If fast-forward is not possible, the merge fails entirely.

```bash
git merge --ff-only feature/user-auth
```

## Tradeoffs

**Fast-forward advantages**: Clean linear history. No extra merge commits cluttering the log. Easy to follow with simple tools like `git log --oneline`. Works well for small teams or solo developers where branch divergence is rare.

**Fast-forward disadvantages**: Loses information about when and how features were integrated. You cannot distinguish which commits belonged to which feature after the fact. The branch structure disappears from history.

**Merge commit advantages**: Preserves topical grouping. All commits from a feature remain visually connected through the merge commit. Makes it easy to revert an entire feature by reverting the merge commit. Provides audit trail showing when integration decisions were made.

**Merge commit disadvantages**: Clutters history with merge noise. Complex projects accumulate hundreds of merge commits that add little value. Makes `git log` output harder to read without filtering.

## Team Conventions

Most professional teams standardize on one approach. Consistency matters more than which method you choose.

Teams using pull request workflows often prefer `--no-ff` merges because the PR represents a logical unit of work worth preserving in history. The merge commit acts as a boundary marker between features.

Teams practicing frequent rebasing before merge often end up with mostly fast-forward merges. By rebasing the feature branch onto the latest main before merging, they ensure the target has not moved. This produces linear history but requires discipline about keeping branches short.

## Visual Comparison

Fast-forward history looks like a straight line:
```
A - B - C - D - E (main)
            \
             F - G (feature, then main points here too)
```

Merge commit history shows the fork:
```
A - B - C - D - H (main, H is merge commit)
            \   /
             E - F (feature)
```

## Recommendation

For data engineering teams working on shared repositories, use `--no-ff` for feature branches merged to main. The merge commit provides clear boundaries between pipeline changes. Use fast-forward for trivial fixes or documentation updates where the overhead of a merge commit adds no value.

Document your team's preference in the contributing guide. Enforce it through branch protection rules if your platform supports requiring merge commits or preventing them.
