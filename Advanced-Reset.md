# Git Reset: Advanced Patterns

## The Three Modes

Reset moves HEAD and optionally modifies the index and working directory. The mode determines how far back it reaches.

### `--soft`
Moves HEAD only. Index and working directory remain untouched. Your staged changes stay staged. Use when you want to rewrite commit history but preserve your staging area exactly as it was—perhaps to amend a commit message or squash commits while keeping the same file state.

### `--mixed` (default)
Moves HEAD and resets the index. Working directory files are unchanged. This unstages everything after the target commit while leaving your actual file modifications intact. Common when you've committed too early and want to re-stage selectively.

### `--hard`
Moves HEAD, resets the index, and overwrites the working directory. All uncommitted changes after the target commit vanish permanently. Dangerous in shared branches. Useful locally when you want to discard recent work entirely and start fresh from a known point.

## Typical Scenarios

**Undo last commit, keep changes staged:**
```bash
git reset --soft HEAD~1
```

**Undo last commit, unstage changes:**
```bash
git reset HEAD~1
```

**Discard last commit completely:**
```bash
git reset --hard HEAD~1
```

**Reset to a specific commit:**
```bash
git reset --hard <commit-hash>
```

**Unstage a single file:**
```bash
git reset HEAD <file>
```

## Reflog as Safety Net

After any reset, `git reflog` shows where HEAD used to be. You can recover from most mistakes:
```bash
git reflog
git reset --hard HEAD@{n}  # restore to nth previous position
```

## Reset vs Revert

Reset rewrites history by moving the branch pointer backward. Revert creates a new commit that undoes changes. Never reset commits already pushed to shared branches—use revert instead. Reset is for local history cleanup; revert is for public history correction.

## Edge Cases

- **Merge commits**: Resetting past a merge commit abandons the merge entirely. Both parent histories collapse.
- **Detached HEAD**: Reset works but affects no branch. You're moving around an unnamed state.
- **Partial resets**: You can reset individual paths with `git reset HEAD <path>` without affecting other files.
- **Interactive staging after mixed reset**: Since files remain modified but unstaged, use `git add -p` to stage hunks selectively before recommitting.

## Common Pitfall

Running `git reset --hard` on a branch others are tracking causes divergence. Their next pull will fail or create messy merges. Coordinate with your team or use feature branches isolated from main development lines.
