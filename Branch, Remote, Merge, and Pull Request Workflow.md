# Branch, Remote, Merge, and Pull Request Workflow

## Local Branch Creation

Start work by creating a branch from an up to date main or develop branch. Name it descriptively using team conventions such as feature/user-auth or fix/login-timeout. This isolates your changes and prevents disruption to the primary codebase.

```bash
git checkout -b feature/payment-integration
```

Keep branches short lived. Long running branches accumulate merge conflicts and drift from the main line of development. Rebase frequently if your task spans multiple days.

## Pushing to Remote

Push your local branch to the remote repository to enable collaboration and backup. Set the upstream tracking reference on the first push so subsequent commands can omit the remote and branch name.

```bash
git push -u origin feature/payment-integration
```

Force pushing should be reserved for personal branches that have not been shared. Never force push to shared branches as it rewrites history for everyone else.

## Merging Strategies

Merging integrates completed work back into the target branch. Two common approaches exist.

**Merge commit** preserves the full history of when integration occurred. It creates a dedicated commit linking two parent commits. This approach maintains chronological accuracy but can clutter the log with merge noise.

**Squash merge** condenses all branch commits into a single commit on the target branch. The result is a linear history where each commit represents a complete unit of work. Individual development steps are lost but the main branch remains clean.

Choose based on team preference and project needs. Consistency matters more than the specific strategy selected.

## Pull Requests

A pull request is a review and discussion mechanism that wraps the merge process. It provides space for code review, automated testing, and documentation before changes enter the main branch.

Open the PR while your work is still fresh. Include context about what changed and why. Link relevant tickets or design documents. Reviewers need this information to evaluate whether the implementation matches intent.

Address feedback promptly. Push additional commits to the same branch rather than opening new PRs. Most platforms update the existing PR automatically.

## Handling Conflicts During Merge

Conflicts arise when both branches modified the same lines. Resolve them locally before merging.

```bash
git fetch origin
git merge origin/main
# resolve conflicts in editor
git add .
git commit
```

Test thoroughly after resolution. Automated tests may pass while logical errors remain. Manual verification ensures the merged result behaves correctly.

## Cleanup After Merge

Delete both local and remote branches once the PR is merged. Stale branches create confusion about which work is active versus completed.

```bash
git branch -d feature/payment-integration
git push origin --delete feature/payment-integration
```

Some teams automate this through platform settings. Enable automatic deletion if available to reduce maintenance overhead.

## Common Pitfalls

Merging directly to main without a PR bypasses review and testing safeguards. Avoid this even for small changes. The overhead of a PR is minimal compared to the risk of unreviewed code.

Neglecting to sync your branch with the target before opening a PR leads to surprise conflicts during review. Fetch and merge or rebase before requesting review to surface issues early.

Treating PR approval as a formality rather than genuine review degrades code quality. Engage thoughtfully with feedback and ask clarifying questions when suggestions seem unclear.
