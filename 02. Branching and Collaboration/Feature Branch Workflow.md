# Feature Branch Workflow

## Core Principle

Work on isolated branches. Never commit directly to main or develop. Each feature, bug fix, or experiment gets its own branch created from the current state of the target integration branch. This isolates incomplete work and prevents broken code from blocking other developers.

## Branch Naming

Use consistent naming conventions that convey purpose and ownership. Common patterns include `feature/description`, `fix/issue-number`, or `experiment/concept`. Avoid generic names like `update` or `changes`. The name should allow someone scanning the remote repository to understand intent without opening the branch.

Include ticket numbers when your team uses project management tools. `feature/PAY-142-payment-validation` links code to requirements explicitly.

## Lifecycle

**Creation**: Branch from an up to date main branch. Verify you have the latest changes before starting work to minimize future merge conflicts.

```bash
git checkout main
git pull origin main
git checkout -b feature/user-profile-api
```

**Development**: Commit frequently with clear messages. Each commit should represent a logical unit of work. Do not bundle unrelated changes into single commits. Push to remote regularly for backup and visibility even if the work is incomplete.

**Synchronization**: If the feature takes multiple days, sync with main periodically. Fetch and merge or rebase to incorporate changes other developers have made. This surfaces conflicts early when they are easier to resolve rather than accumulating them until merge time.

**Completion**: Ensure tests pass. Update documentation if the feature changes interfaces or behavior. Open a pull request targeting the appropriate integration branch. Include context about what changed and why. Link related tickets.

**Review and Merge**: Address reviewer feedback. Push additional commits to the same branch. Once approved, merge using the team's preferred strategy. Delete the branch after successful merge to keep the repository clean.

## Integration Strategy

Choose between merging and rebasing based on team preference. Merging preserves the exact history of when work happened. Rebasing creates a cleaner linear history by replaying commits on top of the latest main. Both approaches work. Consistency within the team matters more than the specific technique.

If rebasing, do it locally before opening the PR or during review. Never rebase branches that others are actively working on. Rewriting shared history causes confusion and forces teammates to recover their work.

## Handling Long Running Features

Features spanning weeks risk significant drift from main. Break them into smaller sub-features that can be merged incrementally. Use feature flags to hide incomplete functionality from users while allowing the code to exist in main. This reduces merge conflict surface area and keeps the feature branch short lived.

If incremental merging is not possible, rebase onto main at least weekly. Resolve conflicts as they arise rather than letting them accumulate. Communicate with the team about your long running branch so others can coordinate around your changes.

## Remote Collaboration

Push your branch to the remote repository early. This enables backup, code review, and pair programming. Other developers can check out your branch to test locally or provide feedback before formal PR submission.

Protect important branches like main and develop through platform settings. Require pull requests for merges. Enforce status checks ensuring tests pass before integration. Prevent force pushes to shared branches.

## Cleanup

Delete local and remote branches after merge. Stale branches create noise and confusion about active work. Most Git platforms offer automatic deletion of source branches after PR merge. Enable this setting to reduce manual maintenance.

```bash
git branch -d feature/user-profile-api
git push origin --delete feature/user-profile-api
```

Periodically prune remote tracking references that no longer exist on the server.

```bash
git fetch --prune
```

## Common Mistakes

Committing directly to main bypasses review and testing safeguards. Resist the temptation even for trivial changes. The discipline of using branches for all work builds muscle memory for handling complex features.

Neglecting to sync with main leads to painful merge sessions. Regular integration keeps conflicts small and manageable.

Keeping branches alive after merge clutters the repository. Delete promptly. If you need to reference old work, use tags or rely on Git's ability to find any commit by hash.
