# Monorepo Strategies for Data Engineering

## The Structural Argument

A monorepo houses multiple projects, pipelines, and services in a single version control repository. For data engineering, this means DAGs, dbt models, Spark jobs, infrastructure definitions, and shared libraries coexist under one root. The alternative is polyrepo, where each component lives in isolation.

Monorepos simplify cross-project refactoring. Changing a shared schema definition updates all consuming pipelines in a single commit. Dependency management becomes explicit rather than negotiated across repository boundaries. Code review captures system-wide impact rather than isolated module correctness.

The tradeoff is complexity. Build times increase. Access control becomes coarse-grained. Tooling must handle scale. Teams accustomed to small, focused repositories face cultural adjustment when navigating a codebase spanning millions of lines.

## Directory Organization

Structure reflects execution boundaries and ownership. Avoid flat layouts where everything sits at the root. Group by domain or function rather than technology type.

```
monorepo/
├── platforms/           # Shared infrastructure and tooling
│   ├── terraform/
│   └── docker/
├── libs/                # Reusable libraries
│   ├── python-utils/
│   └── spark-connectors/
├── domains/             # Business domain grouping
│   ├── finance/
│   │   ├── dags/
│   │   ├── dbt/
│   │   └── tests/
│   └── marketing/
│       ├── dags/
│       └── scripts/
└── tools/               # Internal CLI and automation
```

Domain-based grouping keeps related artifacts together. A finance analyst modifying a revenue model finds DAGs, transformations, and tests in adjacent directories. Technology-based grouping separates SQL from Python, forcing developers to jump across the repository for context.

## Dependency Management

Shared libraries live in the `libs/` directory. Other projects reference them via relative paths or workspace-aware package managers. In Python, use Poetry workspaces or pip install with editable mode. In JavaScript, use Yarn or npm workspaces.

Versioning internal libraries requires discipline. Semantic versioning helps but introduces overhead. Many teams skip versioning entirely in monorepos, relying on trunk-based development where everyone uses the latest code. This works when CI catches breaking changes quickly. Fails when deployment cycles differ across teams.

Pin dependencies explicitly. Do not rely on transitive resolution across workspace boundaries. Document which projects depend on which libraries. Automated dependency graphs help visualize coupling.

## Build and Test Optimization

Running full test suites on every commit becomes prohibitive at scale. Use affected-target detection. Tools like Nx, Bazel, or Turborepo analyze dependency graphs to determine which tests actually need running based on changed files.

If a Spark job in the finance domain changes, only run tests for that job and its direct consumers. Skip unrelated marketing pipelines. This reduces feedback loops from hours to minutes.

Cache build artifacts aggressively. Compiled protobufs, Docker layers, and dbt compiled SQL should persist across runs. Remote caching allows developers to download pre-built artifacts instead of rebuilding locally.

## Access Control and Security

Monorepos complicate permission management. Git does not support path-level access control natively. Everyone with repository read access sees all code. This conflicts with regulatory requirements for PII handling or proprietary algorithm protection.

Mitigate through code ownership rules rather than technical restrictions. Use CODEOWNERS files to enforce review requirements for sensitive directories. Audit access logs regularly. Consider splitting highly sensitive components into separate repositories if legal constraints demand it.

Secrets management remains critical. Do not store credentials in the monorepo regardless of access controls. Use external vaults and inject secrets at runtime.

## CI/CD Integration

Continuous integration must handle scale. Parallelize test execution across multiple runners. Distribute workloads based on resource requirements. CPU-intensive Spark tests run on different infrastructure than lightweight SQL validation.

Deploy independently. Just because code lives together does not mean it deploys together. Each pipeline, service, or model should have its own deployment trigger. Monorepo CI orchestrates these independent deployments based on change detection.

Use release tags sparingly. Tagging the entire monorepo implies atomic releases which rarely match reality. Tag individual components or use commit hashes for deployment references.

## Migration Strategy

Moving from polyrepo to monorepo requires planning. Start with closely coupled projects. Migrate shared libraries first since they have the most cross-repository dependencies. Then move dependent projects. Keep both repositories running during transition. Sync changes bidirectionally until migration completes.

Automate the import process. Manual copy-paste introduces errors and loses history. Use Git subtree merges or specialized migration tools to preserve commit history. Verify that CI/CD pipelines work in the new structure before decommissioning old repositories.

## When to Avoid Monorepos

Do not use monorepos if teams operate independently with no shared code. The overhead outweighs benefits when coupling is minimal. Avoid monorepos if your organization has strict siloed access requirements that cannot be managed through code ownership policies.

Consider polyrepo if build times exceed acceptable thresholds despite optimization efforts. Some technology stacks do not play well with monorepo tooling. Evaluate based on actual friction rather than theoretical advantages.

## Maintenance Discipline

Monorepos rot without active maintenance. Unused libraries accumulate. Deprecated pipelines linger. Documentation drifts. Establish regular cleanup cycles. Archive inactive projects. Remove dead code. Update shared dependencies proactively.

Assign repository stewards responsible for overall health. They enforce standards, manage tooling upgrades, and coordinate large-scale refactoring. Without dedicated ownership, monorepos become dumping grounds for abandoned experiments.
