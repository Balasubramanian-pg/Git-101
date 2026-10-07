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

