# Git for Data Engineering: Code, Configs, DAGs, dbt Models, SQL, Tests, Docs

## Repository Structure

Data engineering repositories contain heterogeneous artifacts. Organize them to reflect execution boundaries and ownership. A typical structure separates orchestration, transformation, and infrastructure.

```
project/
├── dags/              # Airflow or Prefect DAG definitions
├── dbt_project/       # dbt models, tests, macros
│   ├── models/
│   ├── tests/
│   └── macros/
├── scripts/           # Ad-hoc Spark jobs, utility scripts
├── config/            # Environment-specific configurations
├── tests/             # Unit and integration tests for pipelines
├── docs/              # Data dictionary, lineage diagrams
└── infra/             # Terraform or CloudFormation for resources
```

Keep orchestration code separate from transformation logic. DAGs define when and how jobs run. dbt models or Spark jobs define what transformations occur. This separation allows independent versioning and review cycles.

## Managing DAGs

DAG files are Python code that defines workflow structure. Treat them as application code. Include unit tests verifying task dependencies and scheduling logic. Avoid embedding business logic inside DAG files. Keep them thin wrappers that call external modules or scripts.

Version DAGs alongside the code they orchestrate. When a transformation changes, the corresponding DAG update should be in the same commit or PR. This ensures deployment consistency. Do not maintain DAGs in a separate repository unless your team has explicit reasons for decoupling orchestration from implementation.

Handle backfills carefully. Changes to DAG structure may affect historical runs. Document whether modifications are backward compatible. Use semantic versioning for DAG packages if you distribute them independently.

## dbt Models and SQL

dbt projects benefit from Git because they are pure SQL and YAML. Commit frequency should match development velocity. Each model addition or modification gets its own commit with descriptive messages explaining the business logic change.

Use dbt's built-in version control features alongside Git. The `ref()` function handles inter-model dependencies so you can restructure directories without breaking references. However, renaming models requires updating all downstream references manually. Coordinate these changes through PRs with clear migration notes.

Store macro libraries in the same repository. Macros are reusable SQL snippets. Version them with the models that depend on them. Breaking changes to macros require updating all consuming models in the same PR to prevent deployment failures.

## Configuration Management

Never commit secrets, credentials, or environment-specific connection strings. Use configuration files with placeholders or reference external secret management systems. Maintain template configuration files showing required structure without sensitive values.

```yaml
# config/template.yaml
database:
  host: ${DB_HOST}
  port: ${DB_PORT}
  username: ${DB_USER}
  password: ${DB_PASSWORD}
```

Document required environment variables in a README or dedicated configuration guide. New team members need clear instructions on setting up local environments without accessing production secrets.

Separate configuration by environment using directory structures or file naming conventions. `config/dev.yaml`, `config/staging.yaml`, `config/prod.yaml` makes intent explicit. Validate configuration files during CI to catch missing keys before deployment.

## Testing Strategy

Commit tests alongside the code they validate. Unit tests for Python scripts verify transformation logic with small synthetic datasets. Integration tests validate end-to-end pipeline behavior against test fixtures. dbt tests check data quality constraints like uniqueness, non-nullability, and referential integrity.

Store test data fixtures in the repository if they are small and non-sensitive. Large datasets belong in object storage with references in the test code. Ensure test fixtures represent realistic edge cases including nulls, duplicates, and extreme values.

Automate test execution through CI pipelines. Block merges when tests fail. Data quality regressions are expensive to fix after deployment. Catch them during review.

## Documentation

Treat documentation as code. Store data dictionaries, schema descriptions, and lineage diagrams in the repository alongside the models they describe. This ensures documentation stays synchronized with implementation.

dbt generates documentation automatically from model YAML files. Commit the generated docs directory or publish it to a static site through CI. Keep source YAML files updated with column descriptions and business context.

Maintain a CHANGELOG documenting significant pipeline changes. Include breaking changes, new data sources, and deprecated models. Consumers of your data products need visibility into what changed and when.

## Branching for Data Projects

Feature branches work well for adding new models or pipelines. Bug fix branches address incorrect transformations or failed jobs. Use longer-lived branches for major refactoring like migrating from one orchestration tool to another.

Coordinate schema changes carefully. Adding columns is generally safe. Removing or renaming columns breaks downstream consumers. Use deprecation periods where old and new schemas coexist. Document migration timelines in PR descriptions.

## CI/CD Integration

Automate validation on every push. Run SQL syntax checks, dbt compile, and DAG parsing to catch structural errors early. Execute tests against isolated environments. Deploy to staging automatically after successful validation. Require manual approval for production deployments.

Use Git tags for release versioning. Tag stable states of your data platform. This enables rollback to known good configurations when deployments introduce issues.

## Common Pitfalls

**Committing large data files**: Git is not designed for binary data or large datasets. Use Git LFS for necessary binary artifacts but prefer storing data in external systems. Reference data locations in code rather than embedding samples.

**Ignoring SQL formatting**: Inconsistent SQL style makes reviews difficult. Use sqlfluff or similar tools to enforce formatting standards automatically. Configure pre-commit hooks to format SQL before commits.

**Neglecting dependency tracking**: dbt handles model dependencies through `ref()`. Custom scripts often lack explicit dependency declarations. Document upstream and downstream dependencies in code comments or external metadata systems.

**Overlooking access control**: Git repositories may contain references to sensitive tables or PII fields. Ensure repository access aligns with data access policies. Audit who can view and modify critical pipeline code.
