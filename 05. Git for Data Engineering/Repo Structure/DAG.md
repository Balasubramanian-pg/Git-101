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

