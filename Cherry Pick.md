# Git Cherry-Pick

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/454cb2e2-59c7-4dc8-bdf5-a361a46a4ab9" />

## Concept

Cherry-picking applies a specific commit from one branch onto another. Unlike merging which integrates entire branches, cherry-pick selects individual commits by their hash. This creates a new commit on the target branch with identical changes but a different commit ID.

Use this when you need a single fix or feature from another branch without bringing along unrelated work. It is common for hotfixes where a bug fix exists on a feature branch but must reach production immediately.

## Basic Usage

Identify the commit hash you want to copy. Then apply it to your current branch.

```bash
git checkout main
git cherry-pick abc1234
```

Git attempts to apply the changes automatically. If successful, a new commit appears on main with the same diff as the original commit. The author and timestamp remain from the source commit unless you override them.

## Handling Conflicts

Conflicts occur when the target branch has diverged significantly from where the cherry-picked commit was created. Git stops and marks conflicted files. Resolve them manually then continue.

```bash
# After resolving conflicts in editor
git add .
git cherry-pick --continue
```

Abort if the conflict is too complex or the approach proves wrong.

```bash
git cherry-pick --abort
```

## Options

**`-n` or `--no-commit`**: Applies changes to the working directory and index without creating a commit. Useful when you want to modify the changes before committing or combine multiple cherry-picks into one commit.

**`-x`**: Appends a line to the commit message indicating the original commit hash. This maintains traceability so future developers can find the source of the change.

**`-s`**: Adds a Signed-off-by line. Required in projects following Developer Certificate of Origin practices.

**`--mainline`**: Used when cherry-picking a merge commit. Specifies which parent should be considered the main line for diff calculation.

## Multiple Commits

Cherry-pick accepts a range of commits using the same syntax as other Git commands.

```bash
git cherry-pick abc1234..def5678
```

This applies all commits after abc1234 up to and including def5678. Order matters. Git applies them sequentially so later commits may depend on earlier ones in the range.

## When to Use

**Hotfixes**: A critical bug fix exists on a development branch. Production needs it now but the rest of the feature branch is not ready. Cherry-pick the fix commit to main and deploy.

**Selective backports**: A feature completed on a newer branch needs to exist on an older maintenance branch. Cherry-pick relevant commits rather than merging the entire feature branch.

**Correcting mistakes**: You committed something to the wrong branch. Cherry-pick it to the correct branch then reset or revert the original.

## When to Avoid

**Regular integration**: Do not use cherry-pick as your primary method of combining work. Merging or rebasing preserves history relationships and context. Cherry-pick duplicates commits which confuses history analysis tools.

**Dependent commits**: If the commit you want relies on earlier commits that introduced supporting infrastructure, cherry-picking alone will fail or produce broken code. You must include all dependencies or refactor first.

**Shared branches**: Cherry-picking creates duplicate commits with different hashes. If others have already based work on the original commit, they now see two versions of the same change. This causes confusion during future merges.

## Verification

After cherry-picking, verify the result matches expectations. Run tests. Check that no unintended files changed. Compare the diff against the original commit to ensure fidelity.

```bash
git show HEAD
git diff abc1234 HEAD
```

The second command should show minimal differences limited to metadata like commit hash and possibly whitespace handling.
