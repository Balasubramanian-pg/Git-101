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

