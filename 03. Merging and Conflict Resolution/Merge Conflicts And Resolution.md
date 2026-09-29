# Merge Conflicts and Resolution

## Why Conflicts Occur

Git merges automatically when changes affect different files or different lines within the same file. A conflict arises when two branches modify the same lines of code in incompatible ways. Git cannot determine which version represents the intended final state. It stops the merge and marks the conflicting regions for manual resolution.

Conflicts are not errors. They are natural consequences of parallel development. The frequency of conflicts correlates with branch lifespan and team size. Long-running branches accumulating many commits create larger conflict surfaces. Teams working on overlapping modules experience more collisions than those with clear ownership boundaries.

## Anatomy of a Conflict

When Git encounters a conflict, it inserts markers into the affected files. These markers delineate the competing versions.

```
<<<<<<< HEAD
def calculate_tax(amount):
    return amount * 0.15
=======
def calculate_tax(amount):
    return amount * 0.20
>>>>>>> feature/update-tax-rate
```

The section between `<<<<<<< HEAD` and `=======` shows your current branch's version. The section between `=======` and `>>>>>>>` shows the incoming branch's version. The text after `>>>>>>>` identifies the source branch or commit hash.

Some tools display a three-way view showing the common ancestor version alongside both changes. This context helps determine whether one change supersedes the other or if both need integration.

## Resolution Strategies

**Accept one version entirely**: Choose either the current branch or incoming branch code. Delete the markers and the rejected version. Use this when one change is clearly correct and the other is obsolete or incorrect.

**Combine both changes**: Integrate logic from both sides. This often requires rewriting the conflicted section rather than simply copying lines. Ensure the combined result compiles and passes tests. This approach is common when both branches added complementary functionality to the same function.

**Rewrite from scratch**: Discard both versions and implement a new solution. Use this when both approaches are flawed or when the conflict reveals a deeper design issue requiring reconsideration.

## Step-by-Step Resolution

Fetch the latest changes from the remote repository. Attempt the merge or rebase operation. Git will pause at the first conflict.

```bash
git fetch origin
git merge origin/main
# Conflict occurs
```

Open each conflicted file. Search for conflict markers. Resolve each conflict using one of the strategies above. Remove all markers including the angle brackets and equal signs. Save the file.

Stage the resolved files.

```bash
git add path/to/resolved_file.py
```

Continue the merge process.

```bash
git commit
```

If multiple conflicts exist across many files, resolve them incrementally. Stage each file as you complete it. Git tracks progress through the merge. Do not commit until all conflicts are resolved and staged.

## Tooling Assistance

Command-line resolution works but becomes tedious for large conflicts. Integrated development environments provide visual diff tools showing side-by-side comparisons. Tools like VS Code, IntelliJ, and Meld highlight differences and offer buttons to accept left, right, or both versions.

Configure your preferred merge tool in Git settings.

```bash
git config --global merge.tool vscode
```

Specialized merge tools like KDiff3 or P4Merge handle complex three-way merges more effectively than basic text editors. They preserve formatting and reduce manual marker cleanup.

## Preventing Future Conflicts

Communicate about overlapping work. If two developers plan to modify the same module, coordinate timing or divide responsibilities clearly. Pair programming on contentious areas prevents divergent implementations.

Rebase feature branches onto main frequently. This integrates upstream changes incrementally rather than accumulating them until merge time. Small, frequent integrations produce smaller conflicts that are easier to resolve.

Establish clear ownership boundaries. Assign specific modules or directories to individual developers or subteams. Reduce the likelihood of simultaneous modifications to the same files.

Use atomic commits. Small, focused changes affecting limited files reduce conflict probability. Large refactoring commits touching many files create broad conflict surfaces.

## Testing After Resolution

Resolving a conflict does not guarantee correctness. The merged code may compile but behave incorrectly. Run the full test suite after every conflict resolution. Verify edge cases manually if automated tests do not cover the affected logic.

Pay special attention to data engineering pipelines. A conflict in SQL transformation logic or Spark job configuration can silently produce incorrect results. Validate output schemas and row counts against expectations.

## Aborting When Necessary

If a merge becomes too complex or you realize the approach is wrong, abort the operation.

```bash
git merge --abort
```

This returns the repository to its pre-merge state. No partial changes remain. You can retry with a different strategy or seek assistance from teammates. Aborting is safer than committing a poorly resolved merge that breaks production.
