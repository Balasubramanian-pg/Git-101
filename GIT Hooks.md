# Git Hooks

## Concept

Git hooks are scripts that execute automatically at specific points in the Git workflow. They live in the `.git/hooks` directory of your repository. Git triggers them before or after events like committing, merging, or pushing. Use them to enforce standards, run validations, or automate routine tasks.

Hooks are local by default. Cloning a repository does not copy hooks from the source. Each developer must install them individually unless you use a framework to manage distribution. This limitation means hooks complement but do not replace server-side CI checks.

## Common Hook Types

**pre-commit**: Runs before a commit is created. Ideal for linting, formatting, and quick syntax checks. If this script exits with a non-zero status, Git aborts the commit. Use it to catch obvious errors early.

**commit-msg**: Validates or modifies the commit message. Enforce conventions like including ticket numbers or following specific formats. Reject messages that do not meet team standards.

**pre-push**: Executes before pushing to a remote. Run longer tests or integration checks here. Since pre-push runs less frequently than pre-commit, you can afford more expensive operations without slowing down every commit.

**post-merge**: Runs after a successful merge. Use it to update dependencies, rebuild assets, or notify team members. Helpful for ensuring the working directory stays consistent after integrating changes.

**pre-rebase**: Fires before rebasing begins. Prevent accidental rebases on shared branches or warn about potential conflicts.

## Implementation

Hooks are executable scripts in any language. Bash, Python, and Node.js are common choices. The script receives relevant context through arguments or standard input depending on the hook type.

```bash
#!/bin/bash
# .git/hooks/pre-commit

echo "Running linter..."
if ! npm run lint; then
    echo "Linting failed. Commit aborted."
    exit 1
fi
```

Make the script executable:
```bash
chmod +x .git/hooks/pre-commit
```

## Managing Hooks Across Teams

Since hooks do not transfer via clone, teams need a distribution strategy. Three approaches work well.

**Documented manual setup**: Include hook installation instructions in the README. Developers copy scripts from a `hooks/` directory into `.git/hooks/`. Simple but relies on discipline. New team members often forget this step.

**Symbolic links**: Store hooks in a version-controlled directory like `git-hooks/`. Create symlinks from `.git/hooks/` to these files during setup. This keeps hooks in Git while making them active locally. Requires initial setup script but works reliably afterward.

**Hook management frameworks**: Tools like Husky for JavaScript projects or pre-commit for Python handle installation and execution automatically. They read configuration from a file in the repository root and install hooks during dependency installation. This approach ensures consistency across all developer environments.

## Practical Examples

**Enforce commit message format**:
```bash
#!/bin/bash
# commit-msg hook
commit_msg=$(cat $1)
if ! echo "$commit_msg" | grep -qE "^(feat|fix|docs|refactor):"; then
    echo "Commit message must start with feat|fix|docs|refactor:"
    exit 1
fi
```

**Prevent committing secrets**:
```bash
#!/bin/bash
# pre-commit hook
if git diff --cached | grep -i "password\|secret\|api_key"; then
    echo "Potential secret detected. Commit aborted."
    exit 1
fi
```

**Run dbt compile before committing SQL changes**:
```bash
#!/bin/bash
# pre-commit hook
if git diff --cached --name-only | grep -q "\.sql$"; then
    echo "SQL files changed. Running dbt compile..."
    if ! dbt compile; then
        echo "dbt compilation failed. Fix errors before committing."
        exit 1
    fi
fi
```

## Limitations

Hooks run on the developer's machine. A malicious or careless developer can bypass them by using `--no-verify` flag or deleting the hook script. Never rely on hooks for security-critical validations. Use them as convenience tools and quality gates, not as enforcement mechanisms.

Server-side hooks exist but require administrative access to the Git server. Most teams using GitHub, GitLab, or similar platforms cannot install custom server hooks. Instead, they use platform-specific features like branch protection rules and required status checks.

## Best Practices

Keep hooks fast. Pre-commit hooks should complete in seconds. Slow hooks discourage commits and tempt developers to skip them. Move expensive checks to pre-push or CI pipelines.

Provide clear error messages. When a hook blocks a commit, explain why and how to fix it. Vague failures frustrate developers and lead to hook removal.

Make hooks optional for legitimate exceptions. Allow bypassing with `--no-verify` when necessary but document when this is appropriate. Balance enforcement with practicality.

Version control your hook scripts. Store them in the repository even though Git does not activate them automatically. This provides audit trail and enables team-wide updates when validation rules change.
