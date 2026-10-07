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

