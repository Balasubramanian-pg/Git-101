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

