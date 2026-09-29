# What Never to Commit

## The Immutable Nature of Git History

Git is designed for permanence. Once a commit enters the history, it remains accessible unless explicitly purged through complex rewriting operations. Purging is disruptive and error-prone. Prevention is the only reliable strategy. Treat the repository as a public space even if it is private. Access controls change. Employees leave. Accounts get compromised. Assume anything committed will eventually be seen by unintended eyes.

## Credentials and Secrets

API keys, database passwords, SSH private keys, and OAuth tokens have no place in version control. This includes partial credentials. A username combined with a host address aids reconnaissance. Connection strings embedding passwords are obvious targets. Hardcoded secrets in configuration files, environment templates, or utility scripts create immediate security liabilities.

Developers often commit secrets during rapid prototyping. The convenience of pasting a key directly into code outweighs the abstract risk in their mind. This habit persists until a breach occurs. Enforce discipline through pre-commit hooks that scan for common secret patterns. Use automated tools like GitLeaks or TruffleHog in CI pipelines. Block merges when credentials are detected.

Replace hardcoded values with environment variable references or secret management service lookups. Store actual credentials in AWS Secrets Manager, HashiCorp Vault, or platform-specific secure storage. Inject them at runtime. Keep the codebase free of authentication material.

## Raw Data and Large Binary Files

Git compresses text efficiently. It handles binary files poorly. A single large Parquet file or database dump inflates repository size significantly. Cloning becomes slow. Storage costs increase. Performance degrades as the object database grows.

Raw data changes frequently. Each modification creates a new full copy in the history. Unlike code, where changes are incremental, data updates often replace entire files. This duplication wastes space without providing meaningful version history. You do not need to track every intermediate dataset generated during pipeline development.

Store data in object storage like S3, GCS, or Azure Blob Storage. Reference data locations in code through configuration or metadata files. Keep small synthetic fixtures in the repository for testing purposes. These represent edge cases and schema structures, not production volumes.

## Generated Artifacts and Build Outputs

Compiled binaries, distribution packages, and build directories regenerate from source. Committing them creates synchronization nightmares. Developer A builds on macOS. Developer B builds on Linux. The artifacts differ due to platform-specific compilation details. Merge conflicts arise in binary files that cannot be resolved manually.

Exclude `dist/`, `build/`, `target/`, and `node_modules/`. These directories contain transient outputs. They belong in artifact repositories or container registries, not source control. The source code and build scripts define how to produce them. That definition is what matters.

Jupyter notebook outputs fall into this category. Execution counts, timestamps, and rendered charts change every run. Strip outputs before committing or use tools like `nbstripout` to automate cleanup. Keep notebooks focused on logic, not execution state.

## Local Configuration and Environment Files

Environment-specific settings vary by developer machine and deployment target. Database ports, local file paths, and debug flags differ across environments. Committing these forces teammates to override them constantly or break their local setups.

Use template files like `.env.example` or `config/template.yaml` to document required structure. Exclude actual configuration files containing real values. Let each developer maintain their own local overrides. Use `.gitignore` to prevent accidental commits of personal settings.

IDE configuration files like `.vscode/` or `.idea/` reflect individual preferences. Font sizes, theme choices, and plugin settings provide no value to others. Exclude them unless your team standardizes on specific IDE configurations for consistency. Even then, keep them minimal and non-personal.

## Operating System and Editor Metadata

Hidden files created by operating systems clutter repositories. `.DS_Store` on macOS, `Thumbs.db` on Windows, and desktop.ini files serve local indexing purposes. They have no relevance to code functionality. Exclude them globally or per-project.

Editor swap files and backup copies appear during crashes or unsaved sessions. `.swp` files from Vim, temporary copies from Word processors, and autosave artifacts pollute directory listings. Configure your editor to store these in a centralized temporary directory rather than alongside source files. Add appropriate patterns to `.gitignore`.

## Personal Scripts and Debugging Artifacts

Ad-hoc scripts created for one-time investigations often end up committed accidentally. `debug_test.py`, `temp_analysis.sql`, or `check_data.sh` may contain useful logic but lack proper structure, testing, or documentation. They become orphaned files that confuse new contributors about which scripts are production-ready.

Keep experimental work in separate branches or local directories. If a script proves valuable, refactor it properly, add tests, and integrate it through the standard workflow. Do not let the repository become a dumping ground for unfinished experiments.

## Legal and Compliance Risks

Proprietary algorithms, patented methods, or licensed code snippets may have restrictions on distribution. Committing them to shared repositories could violate licensing agreements or expose intellectual property unintentionally. Verify legal permissions before including third-party code or internal proprietary logic in open source or broadly accessible repositories.

Personal information subject to GDPR, HIPAA, or other regulations must never enter version control. Even anonymized datasets can sometimes be re-identified. Treat all data with caution. When in doubt, exclude it and consult legal or compliance teams.

## The Recovery Myth

Some developers believe they can commit sensitive items and remove them later. Git history retains deleted content. Tools exist to purge objects, but they rewrite commit hashes. This breaks synchronization for everyone who cloned the repository. Teammates must reclone or perform complex recovery operations. The disruption far exceeds the initial mistake.

Assume every commit is permanent. Act accordingly. Review changes carefully before staging. Use `.gitignore` aggressively. Scan for secrets automatically. Build habits that prevent errors rather than relying on remediation after the fact.
