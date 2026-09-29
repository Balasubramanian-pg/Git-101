# Staging vs Committing

## The Intermediate Layer

The staging area exists because Git separates the act of selecting changes from the act of recording them. This decoupling provides precision that other version control systems lack. You curate what belongs in the next snapshot before making it permanent. Without staging, every modification in your working directory would automatically become part of the next commit. This all-or-nothing approach forces large, unfocused commits or requires complex workarounds to achieve granularity.

## When to Stage

Stage changes when they form a coherent logical unit. A bug fix affecting three files should be staged together. A refactor touching ten files should be staged together if the refactor is a single conceptual operation. The staging area allows you to assemble these units from across your working directory.

Use staging to separate unrelated modifications made during the same editing session. You might fix a typo in documentation while debugging a complex algorithm. These changes share no logical connection. Stage the documentation fix, commit it, then stage the algorithm fix and commit separately. This creates a history where each entry has a clear purpose.

Stage partial file changes using interactive mode. A single file may contain both a feature addition and a cleanup of old code. Interactive staging lets you select specific hunks within that file. Stage the feature, commit. Stage the cleanup, commit. This level of control prevents bundling distinct concerns simply because they happen to reside in the same file.

## When to Commit

Commit when the staged changes represent a complete, testable state. The codebase should compile, pass tests, and function correctly at each commit point. Do not commit broken code unless you are explicitly marking a checkpoint for personal recovery purposes. Even then, use descriptive messages like "WIP: incomplete payment logic" to signal the state to anyone reviewing history.

Commit frequently during active development. Each commit serves as a save point. If an experimental approach fails, you can revert to the last stable commit without losing hours of work. Frequent commits reduce the cost of mistakes. They also make it easier to identify when a regression was introduced by narrowing the search space between working and broken states.

## The Psychological Difference

Staging is provisional. Committed changes are permanent record. This distinction affects how developers approach each action. Staging encourages experimentation. You can stage a change, review it with `git diff --cached`, and decide it does not belong. Unstage it without consequence. Committing requires confidence. Once committed, the change becomes part of the project narrative. It will be reviewed, analyzed, and potentially reverted by others.

Treat staging as a drafting table. Treat committing as publishing. The discipline of moving deliberately from one to the other produces higher quality history than rushing directly from editing to committing.

## Common Misconceptions

**Staging saves work permanently**: It does not. Staged changes exist only in the local index. If your hard drive fails, staged but uncommitted work is lost. Commit regularly to ensure durability. Staging is for organization, not backup.

**You must stage everything before committing**: False. Only stage what belongs in the current logical unit. Leave other modifications in the working directory for subsequent commits. This selective approach is the primary benefit of the staging area.

**Committing is final**: Commits can be amended, squashed, or rebased before pushing to shared branches. However, treating commits as mutable encourages sloppy habits. Aim for correctness before committing rather than relying on later cleanup. Cleanup is possible but costly in terms of cognitive load and potential history rewriting conflicts.

## Practical Workflow

Modify files in your working directory. Review changes with `git status` and `git diff`. Select related changes with `git add`. Verify the selection with `git diff --cached`. Commit with a descriptive message. Repeat. This cycle creates a deliberate rhythm where each step has a clear purpose. Skipping steps leads to accidental inclusions or vague commit messages.
