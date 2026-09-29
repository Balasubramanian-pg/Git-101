# Viewing History

## Beyond the Linear Log

`git log` is the entry point, but its default output hides more than it reveals. The raw list of commits tells you what changed, not why or how. Effective history inspection requires filtering, formatting, and understanding the graph structure. Git stores history as a directed acyclic graph. Branches are merely pointers into this graph. Viewing history means navigating the relationships between commits, not just reading them sequentially.

## Formatting for Clarity

The default log includes full commit messages, author names, and dates. This verbosity slows scanning. Use `--oneline` to compress each commit into a single line showing the hash prefix and subject line. This allows rapid traversal of large histories.

```bash
git log --oneline
```

Add `--graph` to visualize branch topology. ASCII characters represent merge points and divergences. Combine with `--all` to see every branch, not just the current one. This reveals when features were integrated and how parallel work converged.

```bash
git log --oneline --graph --all
```

Custom formats provide specific data fields. Show only author and date for audit purposes. Display relative time like "2 days ago" instead of absolute timestamps for recent activity assessment.

```bash
git log --pretty=format:"%h - %an, %ar : %s"
```

## Filtering by Context

History becomes useful when narrowed to relevant changes. Filter by author to trace a specific developer’s contributions. Filter by date range to isolate work during a sprint or incident window. Filter by file path to see evolution of a single module.

```bash
git log --author="Balu"
git log --since="2 weeks ago" --until="1 week ago"
git log -- path/to/critical_module.py
```

Search commit messages with `--grep`. Find all commits related to a ticket number or keyword. This locates implementation details when you know the intent but not the specific files changed.

```bash
git log --grep="PAY-142"
```

## Inspecting Changes

Log shows metadata. Diff shows content. Combine them to understand both what happened and how. Use `-p` to include the full patch in log output. This is verbose but comprehensive for small changes.

```bash
git log -p -n 3
```

Use `--stat` to see file modification summaries without full diffs. Lines added and removed per file give a sense of change magnitude. Large numbers indicate refactoring or feature additions. Small numbers suggest bug fixes or tweaks.

```bash
git log --stat -n 5
```

## Blame and Annotation

`git blame` attributes each line of a file to the commit that last modified it. This identifies who introduced specific logic and when. Use it to understand context before modifying code. Contact the author if the intent is unclear.

```bash
git blame path/to/file.py
```

Blame can be misleading. It shows who moved the line, not who wrote it originally. Refactoring often shifts lines between files, changing blame attribution without changing logic. Use `--ignore-revs-file` to exclude known refactoring commits from blame calculation. This preserves original authorship information.

## Bisecting for Debugging

When a bug appears but its origin is unknown, `git bisect` performs a binary search through history. Mark a known good commit and a known bad commit. Git checks out intermediate commits automatically. You test each one and report pass or fail. Git narrows the search space logarithmically. A bug introduced thousands of commits ago can be isolated in fewer than twenty steps.

```bash
git bisect start
git bisect bad HEAD
git bisect good abc1234
# Git checks out middle commit
# Test and run: git bisect good or git bisect bad
```

Bisect requires reproducible tests. Manual verification introduces human error. Automate the test step with `git bisect run` for consistent results.

## Understanding Merge History

Merge commits have multiple parents. Standard log follows only the first parent, creating a linear view that hides branch structure. Use `--first-parent` to see the main branch’s perspective. This shows when features were merged but not the internal commits of those features. Useful for high-level release notes.

Use `--merges` to show only merge commits. This reveals integration points without noise from individual development commits. Analyze merge frequency to identify bottlenecks or overly long-lived branches.

## Practical Workflows

**Pre-review inspection**: Before reviewing a PR, check the author’s recent commit history. Look for patterns of small, focused commits versus large monolithic changes. This informs your review strategy.

**Post-incident analysis**: After a production issue, trace the commits deployed in the problematic release. Identify which changes touched the affected modules. Correlate commit timestamps with incident onset.

**Onboarding support**: New developers use history to understand system evolution. Show them how to filter by module and read commit messages for architectural decisions. History serves as documentation when maintained properly.

## Limitations

History does not capture intent beyond what was written in messages. Poor messages render history opaque. History does not show deleted code unless you specifically search for it. History does not reflect runtime behavior or performance characteristics. It is a record of changes, not a guarantee of correctness. Use it as one tool among many for understanding the codebase.
