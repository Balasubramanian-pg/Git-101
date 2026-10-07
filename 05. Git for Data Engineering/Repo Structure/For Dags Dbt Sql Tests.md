# Repository Structure for DAGs, dbt, SQL, and Tests

## The Separation of Concerns

Data engineering repositories contain three distinct layers: orchestration, transformation, and validation. Mixing these creates coupling that impedes independent development and testing. Orchestration defines when jobs run. Transformation defines what data changes. Validation ensures correctness. Keep them in separate directories with clear boundaries.

```
data-platform/
├── dags/                  # Airflow/Prefect orchestration
├── dbt_project/           # dbt models, tests, macros
├── scripts/               # Standalone Spark/Python jobs
├── tests/                 # Integration and unit tests
├── config/                # Environment configurations
└── docs/                  # Data dictionary and lineage
```

## DAG Directory Structure

Orchestration code should be thin. DAG files define dependencies and scheduling. They do not contain business logic. Import transformation functions from external modules. This allows testing transformation logic independently of the orchestration framework.

```
dags/
├── finance/
│   ├── daily_revenue.py
│   └── monthly_close.py
├── marketing/
│   └── campaign_performance.py
└── utils/
    ├── notifications.py
    └── alerts.py
```

Group DAGs by domain rather than frequency. Daily revenue and monthly close belong together because they share data sources and business context. Grouping by schedule scatters related logic across multiple directories.

Keep utility functions in a shared `utils/` directory. Notification handlers, alert triggers, and connection helpers are reused across domains. Centralizing them prevents duplication and ensures consistent behavior.

## dbt Project Structure

dbt projects have their own internal structure governed by the framework. Respect this structure while aligning it with your broader repository layout.

```
dbt_project/
├── dbt_project.yml
├── models/
│   ├── staging/
│   │   ├── finance/
│   │   └── marketing/
│   ├── intermediate/
│   └── marts/
├── tests/
│   ├── generic/
│   └── specific/
├── macros/
└── seeds/
```

Staging models clean and standardize raw data. Intermediate models perform complex transformations. Marts serve final business metrics. This layering makes lineage traceable. A metric anomaly can be traced back through intermediate to staging to raw source.

Store generic tests in `tests/generic/`. These apply to multiple models, such as uniqueness or non-null constraints. Store specific tests in `tests/specific/`. These validate business rules unique to individual models.

Macros contain reusable SQL logic. Keep them modular. A macro for date truncation should not depend on a specific table schema. Test macros independently using dbt's macro testing capabilities.

## Standalone Scripts

Not all transformations fit into dbt. Spark jobs, custom Python processors, and API integrations live in the `scripts/` directory. Organize them by domain to match the DAG and dbt structure.

```
scripts/
├── finance/
│   ├── process_payments.py
│   └── validate_ledger.py
├── marketing/
│   └── ingest_ads.py
└── shared/
    └── spark_utils.py
```

Each script should be executable independently. Accept parameters via command line arguments or environment variables. Do not hardcode paths or credentials. This enables testing and local execution without modifying code.

## Test Directory Structure

Tests verify both transformation logic and pipeline behavior. Separate unit tests from integration tests. Unit tests validate individual functions with synthetic data. Integration tests verify end-to-end pipeline execution against test fixtures.

```
tests/
├── unit/
│   ├── test_transformations.py
│   └── test_validations.py
├── integration/
│   ├── test_finance_pipeline.py
│   └── test_marketing_pipeline.py
└── fixtures/
    ├── finance_sample.csv
    └── marketing_sample.json
```

Store small test fixtures in the repository. Large datasets belong in object storage with references in test code. Fixtures should represent edge cases: null values, empty inputs, extreme numbers. Normal cases are easy. Edge cases reveal bugs.

## Configuration Management

Environment-specific settings live in the `config/` directory. Use template files to document required structure without exposing secrets.

```
config/
├── template.yaml
├── dev.yaml
├── staging.yaml
└── prod.yaml
```

Reference configuration files from code using environment variables to select the appropriate file. Never commit actual credentials. Use secret management systems for sensitive values. Document the configuration schema in a README so new developers understand required fields.

## Documentation

Documentation lives alongside code. Data dictionaries describe column meanings. Lineage diagrams show data flow. Architecture decisions explain why certain patterns were chosen.

```
docs/
├── data_dictionary.md
├── lineage/
│   ├── finance_lineage.png
│   └── marketing_lineage.png
└── architecture/
    └── decisions.md
```

Update documentation when code changes. Outdated documentation is worse than no documentation because it misleads. Automate documentation generation where possible. dbt generates documentation from model YAML files. Use this feature and publish the output regularly.

## Version Control Considerations

Commit related changes together. If you modify a dbt model and its corresponding DAG, include both in the same commit. This keeps history coherent. Splitting them across commits creates confusion about which changes belong together.

Use descriptive commit messages. "Update revenue calculation" is vague. "Fix double-counting in daily revenue mart" explains the problem and solution. Future developers searching history need context.

Tag releases for major changes. Tags mark stable states of the data platform. They enable rollback to known good configurations when deployments introduce issues.
