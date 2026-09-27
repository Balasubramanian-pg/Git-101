# Merging Branches

## The Integration Point

Merging combines divergent histories into a single lineage. It reconciles work done in parallel so that both sets of changes coexist in the target branch. Git performs this operation by finding the common ancestor commit, calculating the differences from that ancestor to each branch tip, and applying both sets of changes together.

The merge result depends on whether the branches have diverged. If the target branch has not advanced since the feature branch was created, Git performs a fast-forward merge. No new commit is created. The branch pointer simply moves forward. If both branches have new commits, Git creates a merge commit with two parents. This preserves the historical fact that parallel development occurred.

## Preparing for Merge

Ensure your working directory is clean. Uncommitted changes may interfere with the merge process or be overwritten. Stash or commit any pending work before starting.

Update the target branch to its latest state. Fetch from the remote and pull any new commits. Merging an outdated branch increases conflict probability and may require rework after resolution.

```bash
git checkout main
git fetch origin
git pull origin main
```

## Executing the Merge

Switch to the branch receiving the changes. Specify the source branch to merge from.

```bash
git merge feature/payment-integration
```

Git attempts automatic resolution. If successful, it creates a merge commit (unless fast-forward is possible) and opens an editor for the commit message. The default message describes which branches were merged. Edit this to provide context about the integration if needed.

If conflicts arise, Git pauses the merge. Resolve them using standard conflict resolution techniques. Stage the resolved files and complete the merge with a commit.

## Merge Strategies

**Recursive strategy**: The default for most merges. Handles complex histories with multiple merge bases. Works well for typical feature branch integrations.

**Octopus merge**: Merges more than two branches simultaneously. Useful when integrating multiple feature branches at once. Avoids creating a chain of sequential merge commits. Only works when no conflicts exist across the branches.

**Squash merge**: Combines all commits from the source branch into a single commit on the target. The individual development history is lost. Use this when the intermediate commits are noisy or incomplete. Many platforms implement squash merging through pull request interfaces rather than command line.

**Rebase instead of merge**: Rebasing replays commits from the feature branch onto the target branch. This produces linear history without merge commits. Prefer rebasing for personal branches before opening pull requests. Never rebase shared branches that others depend on.

## Handling Merge Commits

Merge commits serve as integration markers. They document when and how branches converged. In complex projects with many contributors, merge commits provide valuable navigation points through history. You can revert an entire feature by reverting its merge commit.

However, excessive merge commits clutter the log. Teams practicing frequent rebasing see fewer merge commits. Teams preferring explicit integration points use `--no-ff` to force merge commits even when fast-forward is possible. Choose based on your team's history visualization preferences.

## Post-Merge Verification

Run tests after every merge. Automated CI should catch issues but local verification provides immediate feedback. Check that the merged code behaves correctly in combination with recent changes on the target branch.

For data engineering pipelines, validate schema compatibility. Ensure that transformations added in the feature branch do not break existing downstream dependencies. Verify row counts and data quality metrics match expectations.

Push the merged branch to the remote repository if it is a shared branch.

```bash
git push origin main
```

Delete the source branch if it is no longer needed. This keeps the repository clean and signals completion.

```bash
git branch -d feature/payment-integration
git push origin --delete feature/payment-integration
```

## Common Scenarios

**Feature to main**: The standard workflow. Merge completed features into the primary integration branch. Use pull requests to facilitate review before merging.

**Hotfix to multiple branches**: A critical bug fix on main needs to reach older release branches. Cherry-pick the fix commit to each target branch rather than merging entire histories. This isolates the fix without introducing unrelated changes.

**Release branch to main**: After testing on a release branch, merge it back to main to ensure main contains all production changes. This synchronization prevents drift between what is deployed and what is in development.

## Troubleshooting

If a merge produces unexpected results, examine the diff between the merge commit and its parents. Verify that all intended changes are present. Check for accidentally deleted code or incorrect conflict resolutions.

Use `git log --merge` to see commits that contributed to conflicts during the merge. This helps identify which changes caused problems and whether similar issues might recur in future merges.

If the merge breaks functionality and cannot be easily fixed, revert the merge commit. This restores the pre-merge state cleanly.

```bash
git revert -m 1 <merge-commit-hash>
```

Investigate the root cause before attempting the merge again. Adjust the source branch or coordinate with other developers to prevent recurrence.
