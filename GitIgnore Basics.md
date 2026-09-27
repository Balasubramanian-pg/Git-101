# Gitignore Basics

## Purpose

The `.gitignore` file tells Git which files to exclude from tracking. It prevents accidental commits of build artifacts, dependencies, secrets, and editor configuration. These files clutter repositories, increase clone times, and create merge conflicts without adding value.

Gitignore patterns apply to untracked files only. If a file is already tracked, adding it to gitignore has no effect. You must remove it from the index first using `git rm --cached <file>` before gitignore takes hold.

## Syntax Rules

Each line specifies a pattern. Git matches these patterns against file paths relative to the repository root. Blank lines and lines starting with `#` are ignored. Use `#` for comments explaining why certain files are excluded.

**Literal matches**: `database.db` ignores any file with that exact name in any directory.

**Directory matches**: `build/` ignores the directory named build and all its contents anywhere in the repository. The trailing slash indicates directory-only matching.

**Wildcards**: `*.log` ignores all files ending in `.log`. `**/temp` ignores any file or directory named temp at any depth. The double asterisk matches across directory boundaries.

**Negation**: Prefixing a pattern with `!` re-includes previously excluded files. `!important.log` keeps important.log even if `*.log` appears earlier in the file. Order matters. Later patterns override earlier ones.

## Common Patterns

**Dependencies**: `node_modules/`, `venv/`, `.venv/`, `vendor/`. These directories contain thousands of files managed by package managers. They belong in lockfiles, not repositories.

**Build outputs**: `dist/`, `build/`, `target/`, `*.class`, `*.o`. Compiled artifacts regenerate from source. Storing them wastes space and causes conflicts when multiple developers build independently.

**Environment files**: `.env`, `.env.local`, `config/secrets.yaml`. These contain credentials and environment-specific settings. Use template files like `.env.example` to document required variables without exposing values.

**IDE configuration**: `.vscode/`, `.idea/`, `*.swp`, `.DS_Store`. Editor settings vary by developer. Operating system metadata files provide no value to other team members.

**Logs and temporary files**: `*.log`, `tmp/`, `*.tmp`. These accumulate during development and testing. They do not represent intentional changes to the codebase.

## Scope and Location

Place `.gitignore` in the repository root for project-wide rules. You can add additional `.gitignore` files in subdirectories for local exclusions. Git combines patterns from all gitignore files it encounters along the path.

Create a global gitignore for patterns that apply across all your projects. Configure it with:
```bash
git config --global core.excludesFile ~/.gitignore_global
```

Add editor backups, OS metadata, and personal tooling files here. This keeps individual repository gitignore files focused on project-specific concerns.

## Testing Patterns

Verify your gitignore works correctly using:
```bash
git check-ignore -v <filename>
```

This command shows which pattern matches a specific file and which gitignore file contains it. Useful for debugging unexpected exclusions or confirming that sensitive files are properly ignored.

## Best Practices

Commit the `.gitignore` file to the repository. It documents what the project considers extraneous. New contributors benefit from seeing established exclusion patterns. Keep it organized with section headers and comments explaining non-obvious exclusions.

Be specific rather than broad. Ignoring `*` then re-including specific files creates confusion. Prefer explicit patterns that clearly communicate intent. Review gitignore during code reviews when new file types appear in the project.

Update gitignore when tooling changes. Adding a new linter, test framework, or build tool often generates new artifact types. Add corresponding exclusions immediately to prevent accidental commits.

## Common Mistakes

**Ignoring tracked files**: Adding an already-committed file to gitignore does nothing. Remove it from Git first with `git rm --cached` then commit the removal. The file remains in working directory but disappears from version control.

**Over-broad patterns**: `*.txt` ignores all text files including documentation and configuration files that should be tracked. Be precise about what you exclude.

**Missing nested directories**: `logs/` ignores a top-level logs directory but not `src/logs/`. Use `**/logs/` to match at any depth if needed.

**Forgetting to commit gitignore**: An uncommitted gitignore file provides no protection for teammates. They will accidentally commit excluded files until they create their own local gitignore.
