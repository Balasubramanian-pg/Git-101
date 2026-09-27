# Remote Repositories

## The Distributed Model

Git is distributed. Every clone contains the full history. There is no central server required for version control to function. However, collaboration requires a shared reference point. Remote repositories serve this purpose. They are copies of your repository hosted on another machine, typically a service like GitHub or GitLab.

Remotes are not special. They are simply other Git repositories you have agreed to synchronize with. You can have multiple remotes. You can push to some and pull from others. The default remote is named `origin`. This is a convention, not a rule. You can name remotes anything that helps you track their purpose.

## Managing Remotes

List configured remotes to see their names and URLs.

```bash
git remote -v
```

Add a new remote when you need to synchronize with a different repository. This is common in fork-based workflows where you push to your fork but pull from the upstream project.

```bash
git remote add upstream https://github.com/original-owner/repo.git
```

Remove a remote when it is no longer needed. This does not affect the remote repository itself, only your local configuration.

```bash
git remote remove old-remote
```

Rename a remote if the current name is confusing or inconsistent with team standards.

```bash
git remote rename origin main-repo
```

## Fetching and Pulling

Fetching downloads changes from the remote without modifying your working directory. It updates remote-tracking branches like `origin/main`. This allows you to inspect changes before integrating them.

```bash
git fetch origin
```

Pulling combines fetching and merging. It downloads changes and immediately attempts to integrate them into your current branch. Use pull when you are ready to accept remote changes. Use fetch when you want to review first.

```bash
git pull origin main
```

## Pushing Changes

Pushing uploads your local commits to the remote. It updates the remote branch to match your local branch. Pushing fails if the remote has changes you do not have. You must pull or fetch and merge before pushing again.

```bash
git push origin feature-branch
```

Set the upstream tracking reference on the first push to simplify future commands.

```bash
git push -u origin feature-branch
```

Force pushing overwrites remote history. Use it only on personal branches. Never force push to shared branches. It destroys work and breaks synchronization for teammates.

## Upstream Tracking

Upstream tracking links a local branch to a remote branch. Git uses this link to determine default behavior for push and pull. When you run `git pull` without arguments, Git knows which remote branch to fetch from. When you run `git push` without arguments, Git knows where to send your commits.

View tracking relationships with:

```bash
git branch -vv
```

Set tracking explicitly if it was not configured during push.

```bash
git branch --set-upstream-to=origin/main main
```

## Common Workflows

**Centralized workflow**: Everyone pushes to and pulls from a single remote. Simple but requires coordination to avoid conflicts.

**Fork-based workflow**: Each developer forks the main repository. They push to their fork and submit pull requests to the main repository. This isolates changes and simplifies access control. Common in open source projects.

**Multi-remote workflow**: Developers maintain connections to multiple remotes. They push to their personal fork and pull from the main repository. This combines isolation with easy synchronization.

## Troubleshooting

If push fails due to non-fast-forward errors, fetch and integrate remote changes. If pull results in unexpected merges, check your configuration. Ensure you are pulling from the correct remote and branch.

If a remote URL changes, update it locally.

```bash
git remote set-url origin new-url
```

Verify the change with `git remote -v`.
