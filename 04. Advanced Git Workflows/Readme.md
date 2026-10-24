# Advanced Git Workflows — Explained

## 1. What is Advanced Git Workflow Management?
> Advanced Git workflow management comprises the internal mechanics, recovery protocols, and architectural strategies required to maintain complex, multi-team codebases at scale.

- **Git Internals** expose the underlying object database, revealing how the system stores snapshots as directed acyclic graphs rather than simple file diffs.
- **Advanced Reset and Recovery** protocols allow developers to rewrite local history, undo destructive actions, and salvage lost commits using the reference log.
- **Cherry Picking** enables the surgical extraction and application of specific commits across divergent branches, facilitating targeted patch releases.
- **Git Hooks** automate enforcement and integration tasks at specific lifecycle events, embedding quality gates directly into the version control process.
- **Monorepo Strategies** consolidate multiple projects into a single repository, requiring specialized tooling for dependency management, build orchestration, and sparse checkouts.

## 2. Big Picture
![Git internal object graph](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8c/Git_objects.svg/1200px-Git_objects.svg.png)
*The internal object database mapping blobs, trees, and commits into a directed acyclic graph.*

Understanding Git requires moving beyond the staging area and branch pointers. The system operates as a content-addressable filesystem. Every action creates or modifies objects in the `.git` directory. Mastery of these internals transforms Git from a mysterious black box into a predictable tool. When developers understand how references point to commit objects, and how commit objects point to tree objects, operations like rebasing, resetting, and recovering lost work become straightforward mathematical manipulations of pointers.

## 3. Core Mechanics and Execution

### Git Internals: The Object Database
Git stores data in four primary object types. Each object is identified by a SHA-1 hash of its content.

| Object Type | Function | Content Stored |
| :--- | :--- | :--- |
| **Blob** | Stores file content. | The exact bytes of a file. No metadata like filename or permissions. |
| **Tree** | Stores directory structure. | A list of modes, object types, SHA-1 hashes, and filenames. Acts as a directory. |
| **Commit** | Stores snapshot metadata. | Points to a top-level tree, includes author, committer, message, and parent commit hashes. |
| **Tag** | Stores a named reference. | Points to a specific commit object. Annotated tags include a message and PGP signature. |

The `.git/objects` directory contains all these items. Git compresses them using zlib and stores them in folders named after the first two characters of their SHA-1 hash. The remaining 38 characters form the filename. This structure prevents any single directory from holding millions of files, optimizing filesystem performance.

References live in `.git/refs`. Branches are simply text files containing the 40-character SHA-1 hash of a commit. When a new commit is created, Git updates the branch reference to point to the new commit hash. The `HEAD` file points to the current branch reference.

### Advanced Reset and Undoing Changes
The `git reset` command moves the current branch pointer to a different commit. It operates in three primary modes.

1. **Soft Reset**: Moves the branch pointer. The staging area and working directory remain untouched. The changes from the undone commits appear as staged modifications.
2. **Mixed Reset**: The default mode. Moves the branch pointer and clears the staging area. The working directory remains untouched. Changes appear as unstaged modifications.
3. **Hard Reset**: Moves the branch pointer, clears the staging area, and overwrites the working directory. All uncommitted changes and changes from the undone commits are permanently destroyed in the working tree.

`git revert` creates a new commit that applies the inverse of a specified commit. This preserves history and is the safe method for undoing changes on shared branches.

### Cherry Picking
The `git cherry-pick` command extracts the changes introduced by a specific commit and applies them as a new commit on the current branch.

Execution steps:
1. Git identifies the diff between the target commit and its parent.
2. Git attempts to apply this diff to the current working directory.
3. If conflicts arise, Git pauses and requires manual resolution.
4. Upon resolution, Git creates a new commit with the original author information but a new committer timestamp and hash.

### Git Hooks
Hooks are executable scripts triggered by specific Git events. They reside in the `.git/hooks` directory.

Client-side hooks execute on the developer machine.
- `pre-commit`: Runs before the commit message editor opens. Used for linting and formatting.
- `prepare-commit-msg`: Modifies the default commit message template.
- `commit-msg`: Validates the commit message format against team conventions.
- `post-commit`: Runs after a successful commit. Used for notifications.
- `pre-push`: Runs before data is transmitted to a remote. Used for running test suites.

Server-side hooks execute on the remote repository host.
- `pre-receive`: Runs before any references are updated. Used to enforce branch protection rules.
- `update`: Runs for each branch being updated.
- `post-receive`: Runs after all references are updated. Used to trigger continuous integration pipelines or deployment scripts.

### Monorepo Strategies
A monorepo stores multiple distinct projects within a single Git repository.

Core mechanics include:
- **Sparse Checkout**: Configures Git to only populate the working directory with files from specific subdirectories. This reduces disk usage and indexing time for massive repositories.
- **Dependency Graphing**: Tooling maps the relationships between internal packages to determine the exact build and test sequence.
- **Affected Commands**: Build tools analyze the Git diff to identify which projects are impacted by a change, executing tests only for those specific modules.

## 4. The Key Conceptual Point
> Git is a content-addressable filesystem with a version control interface layered on top.

- Every file, directory, and commit is an object stored by the cryptographic hash of its content.
- Branches are merely lightweight, movable pointers to specific commit objects.
- History rewriting operations like rebase and reset simply move these pointers to different existing objects.
- The reference log tracks all pointer movements, providing a master timeline that enables the recovery of lost commits.
- Understanding the object model removes the fear of destructive commands, as the underlying data remains intact until garbage collection runs.

## 5. Types, Variants, and Comparison

### Reset Modes Comparison

| Mode | Branch Pointer | Staging Area | Working Directory | Primary Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **Soft** | Moved | Unchanged | Unchanged | Combining multiple small commits into a single logical commit before pushing. |
| **Mixed** | Moved | Cleared | Unchanged | Unstaging files that were added to the index by mistake. |
| **Hard** | Moved | Cleared | Overwritten | Discarding all local changes and returning the workspace to a known good state. |
| **Keep** | Moved | Cleared mostly | Preserved if clean | Switching branches while carrying over uncommitted changes safely. |

### Undo Strategies Comparison

| Strategy | Mechanism | History Impact | Shared Branch Safety |
| :--- | :--- | :--- | :--- |
| **Reset** | Moves branch pointer backward. | Rewrites history. Commits become unreachable. | Unsafe. Causes divergence for other developers. |
| **Revert** | Creates a new inverse commit. | Preserves history. Adds a new commit. | Safe. Does not alter existing shared history. |
| **Restore** | Updates working tree or index from a commit. | No impact on commit history. | Safe. Only affects local uncommitted state. |

### Repository Architecture Comparison

| Architecture | Structure | Tooling Requirements | Best Suited For |
| :--- | :--- | :--- | :--- |
| **Polyrepo** | One repository per project or microservice. | Standard Git, standard CI/CD. | Independent teams, distinct release cycles, open source components. |
| **Monorepo** | All projects in a single repository. | Bazel, Nx, Turborepo, custom build orchestration. | Tightly coupled services, shared libraries, unified versioning, large engineering organizations. |

Bottom line: Select reset for local cleanup, revert for shared history correction, and monorepo architectures only when the team possesses the tooling to manage the build graph.

## 6. Real-World Applications

- **Hotfix Backporting**: A critical security vulnerability is patched on the main development branch. The release engineer uses cherry-pick to apply that exact commit to the older, stable release branch without pulling in unfinished features.
- **Automated Code Formatting**: A pre-commit hook runs a code formatter. If the formatter modifies files, the hook exits with a non-zero status, aborting the commit and forcing the developer to stage the formatted files.
- **Commit Message Enforcement**: A commit-msg hook parses the proposed message using a regular expression. It rejects the commit if the message lacks a required issue tracking ticket number in the footer.
- **Massive Codebase Cloning**: A developer joining a team working on a monorepo with millions of files uses sparse checkout. They configure Git to only download the specific microservice they are assigned to, reducing clone time significantly.
- **Disaster Recovery**: A developer accidentally executes a hard reset, wiping out three days of unpushed work. They use the reference log to find the SHA-1 hash of the commit just before the reset, then execute a hard reset to that hash, restoring the work instantly.

## 7. Key Takeaways

✅ Git stores data as a directed acyclic graph of blobs, trees, and commits, identified by SHA-1 hashes of their content.
✅ Branches are lightweight text files containing a 40-character commit hash, making branch creation and switching instantaneous.
✅ Soft reset moves the branch pointer while preserving the staging area, ideal for squashing local commits before sharing.
✅ Hard reset overwrites the working directory and staging area, permanently destroying uncommitted changes in the local workspace.
✅ Git revert creates a new commit that undoes the changes of a previous commit, preserving the historical record for shared branches.
✅ The reference log records every update to the tip of branches, providing a master timeline to recover lost commits after destructive operations.
✅ Cherry picking applies the diff of a specific commit to the current branch, enabling targeted patches across divergent release lines.
✅ Client-side Git hooks automate local enforcement of formatting and message conventions before data reaches the repository.
✅ Server-side hooks intercept push operations to enforce branch protection rules and trigger external continuous integration systems.
✅ Monorepo strategies require specialized build tools to map dependency graphs and execute tests only for affected modules.
✅ Sparse checkout restricts the working directory to specific subdirectories, making massive monorepos manageable on local machines.

## 8. Where This Fits

| Stage | What happens | Connection to Advanced Workflows |
| :--- | :--- | :--- |
| **Local Experimentation** | Developer writes code, creates messy commits. | Uses soft reset to clean up history before sharing. |
| **Pre-Commit Validation** | Developer stages files and prepares to commit. | Git hooks execute automatically to lint code and validate message formats. |
| **History Correction** | Developer realizes a mistake in the last three commits. | Uses interactive rebase or reset to rewrite local history safely. |
| **Cross-Branch Patching** | A bug fix must be applied to an older release branch. | Developer uses cherry-pick to extract and apply the specific fix. |
| **Remote Integration** | Developer pushes branches to the central server. | Server-side hooks validate permissions and trigger automated build pipelines. |
| **Disaster Recovery** | Developer accidentally destroys local work. | Uses the reference log to locate and restore the lost commit objects. |
| **Repository Scaling** | The codebase grows to millions of files across many teams. | The organization adopts monorepo strategies, implementing sparse checkout and affected-only builds. |

The next phase of the course examines repository hosting platforms and the configuration of remote collaboration tools, building directly upon the hook enforcement and branch management concepts established in this module.
