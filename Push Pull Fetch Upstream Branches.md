# Push, Pull, Fetch, and Upstream Branches

## The Remote Connection

Git operates locally by default. Your commits, branches, and history exist on your machine until you explicitly synchronize with a remote repository. The remote is typically a hosted platform like GitHub or GitLab. Understanding the distinction between local state and remote state prevents confusion about where changes live.

## Fetching Changes

`git fetch` downloads objects and references from the remote repository without modifying your working directory. It updates remote-tracking branches like `origin/main` to reflect the current state of the remote. Your local branches remain untouched. This operation is safe and non-destructive. Use it to inspect what others have done before integrating their work.

```bash
git fetch origin
```

After fetching, compare your local branch with the remote-tracking branch to see divergences.

```bash
git log HEAD..origin/main
```

## Pulling Changes

`git pull` combines fetch and merge into a single step. It downloads changes from the remote and immediately attempts to merge them into your current branch. If conflicts arise, you must resolve them before continuing. Pull is convenient but opaque. It hides the intermediate fetch step, making it harder to inspect changes before integration.

```bash
git pull origin main
```

Prefer fetching first, reviewing changes, then merging or rebasing manually. This gives you control over how remote changes integrate with your work. Use pull only when you are certain the integration will be clean or when working on simple projects with minimal divergence.

## Pushing Changes

`git push` uploads your local commits to the remote repository. It updates the remote branch to match your local branch. Pushing requires that your local branch is ahead of the remote or exactly matches it. If the remote has new commits you do not have, push fails to prevent overwriting others' work.

```bash
git push origin feature/user-auth
```

Set the upstream tracking reference on the first push using `-u`. This links your local branch to the remote branch so subsequent pushes can omit the remote and branch name.

```bash
git push -u origin feature/user-auth
```

Force pushing with `--force` or `-f` overwrites remote history. Use this only on personal branches where you are the sole contributor. Never force push to shared branches like main or develop. It destroys other developers' work and creates confusion about repository state.

## Upstream Branches

An upstream branch is the remote branch that your local branch tracks. Git uses this relationship to simplify commands. When you run `git pull` without arguments, Git knows which remote branch to fetch from and which local branch to merge into. When you run `git push` without arguments, Git knows where to send your commits.

Configure upstream tracking explicitly or let Git infer it during the first push with `-u`. View upstream relationships with:

```bash
git branch -vv
```

This shows each local branch alongside its upstream counterpart and whether it is ahead or behind.

## Handling Divergence

When both local and remote branches have new commits, they have diverged. Pushing fails because Git cannot fast-forward the remote branch. You must integrate the remote changes first. Two approaches exist.

**Merge**: Pull the remote changes, creating a merge commit. Then push the merged result. This preserves history accurately but adds merge noise.

**Rebase**: Fetch the remote changes, rebase your local commits on top of them, then push. This creates linear history but rewrites local commit hashes. Use rebase for personal branches before sharing. Never rebase after pushing to shared branches.

## Common Workflows

**Daily synchronization**: Fetch regularly to stay aware of team progress. Merge or rebase onto main frequently to keep feature branches current. This reduces conflict severity at merge time.

**Sharing work in progress**: Push incomplete work to remote branches for backup or collaboration. Communicate clearly that the branch is not ready for review. Use draft pull requests if your platform supports them.

**Cleaning up**: Delete remote branches after merging. Stale branches clutter the repository and confuse contributors about active work.

```bash
git push origin --delete feature/old-feature
```

## Troubleshooting

If push fails due to non-fast-forward errors, fetch and integrate remote changes before retrying. If you accidentally force pushed to a shared branch, notify the team immediately. Provide the commit hash of the previous state so others can recover. Use reflog to find lost commits if needed.

If pull results in unexpected merges, check your Git configuration. Ensure `pull.rebase` is set according to team preference. Some teams prefer rebasing by default to maintain linear history. Others prefer merging to preserve exact chronological order.
