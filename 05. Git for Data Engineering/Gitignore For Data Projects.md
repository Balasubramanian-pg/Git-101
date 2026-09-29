# Gitignore for Data Projects

Data engineering repositories accumulate artifacts that differ significantly from traditional software projects. The volume of generated files is larger, the sensitivity of data is higher, and the tooling stack is more diverse. A well-structured `.gitignore` prevents repository bloat and security incidents.

## Raw and Processed Data

Never commit raw data files. They are large, change frequently, and often contain sensitive information. Exclude common formats used in data ingestion and transformation.

```
# Raw data dumps
data/raw/
*.csv
*.parquet
*.avro
*.jsonl
*.feather
```

Processed intermediate datasets also belong outside version control. These files regenerate from pipeline execution. Storing them creates merge conflicts and inflates repository size without preserving logic.

```
# Intermediate processing outputs
data/intermediate/
data/processed/
output/
exports/
```

If you need sample data for testing, create a dedicated `tests/fixtures/` directory with small, synthetic datasets. Explicitly ignore everything else in the data directories while allowing specific test fixtures through negation patterns if necessary.

## Database and Warehouse Artifacts

Local development often involves embedded databases or cached query results. Exclude these to prevent committing environment-specific state.

```
# Local database files
*.db
*.sqlite
*.duckdb
warehouse_cache/
.dbt/
target/
```

dbt generates compiled SQL and documentation in the `target/` directory. This output changes with every run and provides no value in version control. The source models and YAML configurations are what matter.

## Orchestration State

Airflow, Prefect, and similar tools maintain internal state about DAG runs, task instances, and logs. These directories grow continuously and contain execution metadata irrelevant to code review.

```
# Airflow
airflow.db
logs/
plugins/__pycache__/

# Prefect
.prefect/
prefect.db
```

Checkpoint files for streaming jobs or incremental loads track progress across executions. Store these in object storage or distributed file systems, not in the Git repository.

```
# Checkpoint and state files
checkpoints/
.state/
*.checkpoint
```

## Python and Spark Environments

Data pipelines rely on heavy dependency stacks. Virtual environments and package caches consume significant space and vary by developer machine.

```
# Python environments
venv/
.env/
.venv/
env/
pip-log.txt
pip-delete-this-directory.txt

# Spark
spark-warehouse/
metastore_db/
derby.log
```

Jupyter notebooks generate metadata including execution counts, timestamps, and output cells. This metadata causes noisy diffs even when code remains unchanged. Consider using tools like `nbstripout` to clean notebooks before committing, or exclude notebook outputs entirely.

```
# Jupyter checkpoints
.ipynb_checkpoints/
*.ipynb
```

If your team commits notebooks, add a pre-commit hook to strip outputs. Alternatively, convert notebooks to Python scripts for production pipelines and keep notebooks only for exploration.

## Configuration and Secrets

Environment-specific configuration files often contain connection strings, API keys, or credentials. Exclude actual configuration files while keeping templates that document required structure.

```
# Environment configs
.env
.env.local
config/prod.yaml
config/secrets.json
credentials.json
service-account-key.json
```

Maintain a `config/template.yaml` or `.env.example` in the repository to show required fields without exposing values. Document the secret management strategy in your README so new developers know how to obtain credentials.

## Logs and Debugging Output

Pipeline execution generates verbose logs. These belong in centralized logging systems, not in the code repository.

```
# Logs
*.log
logs/
debug_output/
error_reports/
```

Temporary debugging files created during development should also be excluded. Developers often create quick scripts or dump files to investigate issues. Prevent these from accumulating in the repository.

```
# Temporary debug files
debug_*.py
test_dump.csv
tmp_*.parquet
```

## IDE and OS Files

Standard editor and operating system artifacts apply here as they do in any project. Keep these in a global gitignore to avoid cluttering project-specific files, but include them locally if team members use different tools.

```
# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db
```

## Best Practices for Data Teams

**Be explicit about data locations**. Document where raw and processed data live in external storage systems. The `.gitignore` file serves as documentation of what is not tracked. Add comments explaining why certain patterns are excluded.

**Review gitignore during onboarding**. New team members often accidentally commit large data files or credentials. Walk them through the exclusion patterns and explain the rationale. Show them how to recover if they make mistakes.

**Use negation sparingly**. Re-including specific files within ignored directories creates confusion. Prefer organizing files into clearly separated directories where entire directories can be ignored safely.

**Update when tooling changes**. Adding a new orchestration framework, database engine, or data format requires corresponding gitignore updates. Treat `.gitignore` as living documentation of your technology stack.
