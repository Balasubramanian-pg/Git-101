# Small Atomic Commits

## Definition of Atomicity

An atomic commit encapsulates a single logical change. It is the smallest unit of work that makes sense independently. If you cannot describe the commit without using "and," it likely contains multiple concerns. "Add user validation and update email template" should be two commits. "Fix null pointer in payment processor" is one.

Atomicity does not mean tiny. A refactor touching fifty files can be atomic if every line serves the same singular purpose. Conversely, three lines changing unrelated configuration values are not atomic despite their size. The boundary is conceptual coherence, not line count.

## Why Size Matters

Small commits simplify code review. Reviewers understand context quickly when changes focus on one problem. Large diffs force mental context switching between unrelated modifications. Fatigue sets in. Defects slip through because reviewers skim rather than analyze.

Bisecting becomes practical. When a bug appears, `git bisect` identifies the offending commit efficiently only if commits are granular. A massive commit containing ten features provides no useful signal. You still face manual investigation within that commit to isolate the actual cause.

Reverting is safe. Removing a broken feature requires reverting only its specific commit. With monolithic commits, reverting removes desired functionality alongside the defect. Cherry-picking fixes to release branches works cleanly when each fix exists as an independent unit.

History becomes documentation. Future developers searching for why something changed find focused commit messages explaining intent. Scanning a log of atomic commits tells a coherent story. Scanning a log of "WIP," "fixes," and "updates" reveals nothing.

## Identifying Boundaries

Ask what question the commit answers. "Why was this validation added?" should have one clear answer tied to a specific requirement or bug report. If the answer requires listing multiple reasons, split the work.

Separate refactoring from feature additions. Changing how existing code is structured differs fundamentally from adding new behavior. Mixing them obscures both intentions. Refactor first, verify tests pass, commit. Then add the feature, verify tests pass, commit.

Isolate test additions from implementation when possible. Tests document expected behavior. Implementation fulfills it. Keeping them separate clarifies whether a commit introduces new requirements or satisfies existing ones. This distinction matters during reviews and future maintenance.

Extract formatting and whitespace changes into dedicated commits. Linting fixes, import reordering, and indentation corrections have no functional impact. Bundling them with logic changes pollutes diffs. Automated tools can handle these separately through pre-commit hooks.

## Practical Techniques

Commit frequently during development. Do not accumulate hours of work before committing. Stage and commit each completed subtask. You can always squash later if needed, but you cannot unmix entangled changes easily.

Use interactive staging (`git add -p`) to select specific hunks within a file. This allows committing related changes separately even when they exist in the same modified file. Stage the validation logic, commit. Stage the logging addition, commit. Both came from the same editing session but serve different purposes.

Write commit messages before coding when possible. Drafting "Fix timezone conversion for UTC timestamps in event pipeline" forces clarity about scope. If you struggle to articulate the change concisely, the scope may be too broad. Refine your plan before writing code.

Amend commits during active development to incorporate feedback or forgotten files. This keeps the working history clean before pushing. Once pushed, treat commits as immutable. Amending shared history causes synchronization problems for teammates.

## Common Pitfalls

Over-splitting creates noise. Committing every saved file produces a history cluttered with incomplete states. Balance granularity with coherence. Each commit should leave the codebase in a working state, even if the feature is not yet complete.

Ignoring dependencies between changes. Some modifications inherently belong together. Splitting a database migration from its corresponding model update breaks the build at intermediate commits. Recognize when coupling is structural rather than accidental.

Treating atomicity as dogma rather than guidance. Perfect atomicity is aspirational. Pragmatic atomicity serves the team's needs. If splitting a change adds significant overhead without improving reviewability or debuggability, keep it together. The goal is maintainable history, not theoretical purity.

Neglecting commit message quality. Small commits with vague messages provide little value. "Update config" tells reviewers nothing. "Increase Spark executor memory to 8GB for large join operations" explains the what and why. Invest time in messages proportional to the change's importance.
