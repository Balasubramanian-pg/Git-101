# Git Internals

## The Object Database

Git stores everything as objects in a content-addressable database under `.git/objects`. Each object is identified by a SHA-1 hash of its contents. Four object types form the foundation.

**Blob**: Stores file contents without metadata. No filename, no permissions, no directory structure. Just raw bytes. If two files have identical content, Git stores only one blob regardless of how many times that content appears in the repository.

**Tree**: Represents a directory listing. Contains entries mapping filenames to blob hashes or other tree hashes. A tree object captures the state of a directory at a point in time. The root tree represents the entire project snapshot.

**Commit**: Points to a single tree object representing the project state. Includes metadata like author, committer, timestamp, and parent commit hashes. A commit with multiple parents indicates a merge. The commit message lives here.

**Tag**: Annotated tags are objects pointing to commits with their own metadata. Lightweight tags are simple references without separate objects.

When you run `git add`, Git creates blob objects for modified files and tree objects reflecting the new directory structure. When you run `git commit`, Git creates a commit object pointing to the current tree. Nothing is deleted. Old objects remain until garbage collection removes unreachable ones.

## References and HEAD

References are pointers to commits stored in `.git/refs`. Branches are references that move forward as you commit. Tags are references that stay fixed. HEAD is a special reference indicating the current working state.

HEAD usually points to a branch name rather than a commit hash directly. This symbolic reference allows branches to advance while HEAD follows them. Detached HEAD occurs when HEAD points directly to a commit hash instead of a branch. You can still commit but no branch tracks your work.

The reflog records every change to HEAD and branch references. It exists locally and is not shared via push or pull. Reflog enables recovery from accidental resets or rebases by showing where references used to point.

## The Index (Staging Area)

The index is a binary file at `.git/index` acting as an intermediate layer between working directory and repository. It tracks which versions of files will be included in the next commit. Running `git add` copies file contents into the object database and updates the index with the new blob hashes.

The index maintains stat information like file size and modification time. Git uses this to detect working directory changes efficiently without reading every file. When you modify a file, Git notices the stat change and marks it as dirty. Running `git status` compares working directory against the index. Running `git diff` compares working directory against index. Running `git diff --cached` compares index against HEAD.

## Packfiles and Compression

Individual objects consume disk space inefficiently. Git periodically compresses them into packfiles. A packfile stores objects as deltas against similar objects rather than complete copies. This achieves significant compression especially for text files with small incremental changes.

The `.git/objects/pack` directory contains packfiles and corresponding index files mapping offsets to object hashes. Git transparently reads from both loose objects and packfiles. Users rarely interact with packfiles directly except during garbage collection.

## Garbage Collection

Unreachable objects accumulate over time. Objects created during aborted operations, rebases, or resets may have no references pointing to them. Git's garbage collector (`git gc`) identifies these orphaned objects and removes them after a grace period.

Reflog entries keep objects reachable temporarily. An object referenced by reflog entry from thirty days ago remains safe even if no branch or tag points to it. The default grace period is ninety days for reflog entries and two weeks for other unreachable objects. Configure these thresholds through `gc.reflogExpire` and related settings.

Run `git gc` manually when repository size grows unexpectedly. Git runs automatic garbage collection periodically based on heuristic triggers like number of loose objects.

## Plumbing vs Porcelain

Git commands fall into two categories. Porcelain commands provide user-friendly interfaces for common workflows. `git commit`, `git push`, `git merge` are porcelain. They handle multiple steps internally and provide helpful output.

Plumbing commands operate directly on the object database. `git hash-object`, `git cat-file`, `git update-ref` are plumbing. They expose Git's internal mechanics. Most users never need plumbing commands but understanding them clarifies how porcelain commands work.

For example, `git commit` internally creates blobs for staged files, builds a tree from the index, creates a commit object pointing to that tree, and updates the branch reference. Each step corresponds to a plumbing command.

## Performance Characteristics

Git scales well because most operations work locally on the object database. Reading history, comparing versions, and creating commits do not require network access. Only fetch, push, and clone involve remote communication.

Large repositories slow down due to object count rather than total size. Millions of objects increase lookup time even if packfiles compress them efficiently. Shallow clones reduce initial download by limiting history depth but create incomplete local repositories that cannot perform certain operations like full history searches.

Sparse checkout allows working with subsets of large monorepos. Git downloads all objects but checks out only specified paths into the working directory. This reduces disk usage and IDE indexing overhead while maintaining full history access.
