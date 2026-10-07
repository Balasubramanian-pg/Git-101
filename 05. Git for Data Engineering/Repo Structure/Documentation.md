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
