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

