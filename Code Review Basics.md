# Code Review Basics for Data Engineering

## Scope Beyond Syntax

Data engineering code reviews differ from application development because correctness matters more than elegance. A bug in a web service returns an error page. A bug in a data pipeline silently corrupts downstream analytics or breaks regulatory reporting. Reviewers must prioritize data integrity over code style.

Focus on whether the transformation logic matches business requirements. Verify that edge cases like null values, duplicate records, and schema drift are handled explicitly. Style preferences take secondary position to functional accuracy.

## Schema and Type Safety

Check that input and output schemas are defined clearly. Weak typing in Spark or Pandas allows silent type coercion that produces incorrect results later. Ensure explicit casts where needed. Validate that column names follow team conventions and do not conflict with reserved keywords in downstream systems.

Watch for schema evolution issues. If the source system adds columns or changes types, will the pipeline fail gracefully or crash? Look for schema validation steps or at least logging when unexpected columns appear.

## Partitioning and Performance

Examine how data is partitioned during reads and writes. Incorrect partitioning causes small file problems or skewed joins. Verify that partition columns align with query patterns used by downstream consumers. Check for unnecessary shuffles in Spark operations. Look for broadcast join opportunities on small dimension tables.

Review write patterns. Are files being written with appropriate sizes? Is there compaction logic for incremental loads? Does the code handle overwrite versus append correctly for the target system?

## Idempotency and Retry Safety

Pipelines fail. Networks drop. Clusters restart. The code must handle retries without producing duplicate records or partial state. Check that write operations are idempotent. If the job runs twice with the same input, does it produce the same output? Look for deduplication logic or transactional writes where supported.

Verify checkpointing configuration for streaming jobs. Ensure state cleanup happens to prevent unbounded growth. Check that watermark handling accounts for late arriving data appropriately.

## Testing Approach

Unit tests for data pipelines should validate transformation logic with known inputs and expected outputs. Integration tests verify end to end behavior against test fixtures. Check that tests cover null handling, empty datasets, and boundary conditions.

Look for data quality checks embedded in the pipeline. Row count validations, null percentage thresholds, and value range checks catch issues before they propagate. These assertions belong in production code, not just test suites.

## Configuration and Secrets

Ensure no hardcoded credentials, connection strings, or environment specific paths exist in the code. Configuration should be externalized through environment variables, config files, or secret management systems. Verify that sensitive data like PII fields are masked in logs.

Check resource allocation settings. Memory and core configurations should match cluster capabilities and data volumes. Over-provisioning wastes cost. Under-provisioning causes failures.

## Documentation and Lineage

Complex transformations need explanation. A SQL CTE chain or multi-step Spark transformation should include comments describing the business logic, not just the technical operation. Verify that column derivations are traceable. Future maintainers need to understand why a calculation exists, not just how it is computed.

Check that metadata tagging or lineage information is captured if your platform supports it. This helps impact analysis when source systems change.

## Common Anti-Patterns

**Collecting large datasets to driver**: Calls like `collect()` in Spark pull all data to a single node. This works in testing but crashes in production. Flag any operation that materializes entire datasets in memory.

**Ignoring partition skew**: Joins on high cardinality keys with uneven distribution cause stragglers. Look for salting techniques or broadcast hints where appropriate.

**Hardcoded dates or filters**: Pipelines should process data based on execution context, not fixed date ranges. Check that date parameters come from configuration or runtime arguments.

**Missing error handling**: Silent failures in data pipelines are worse than loud failures. Ensure exceptions are logged with sufficient context for debugging. Partial failures should not leave the system in an inconsistent state.

## Reviewer Mindset

Assume the code will run at ten times the current volume. Will it scale? Assume the source schema will change unexpectedly. Will it degrade gracefully? Assume someone else will maintain this code in six months. Is it understandable?

Ask questions rather than making demands. "What happens if this column contains nulls?" prompts deeper thinking than "Add null handling." The goal is shared understanding and robust systems, not proving reviewer superiority.
