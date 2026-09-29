# Keeping Main Stable

## The Principle of Protected Integration

Main represents the current state of truth. It should always be deployable, testable, and free of known defects. Stability does not mean stagnation. It means every commit on main has passed validation gates before integration. Treat main as a release candidate at all times rather than a development workspace.

## Branch Protection Rules

Configure repository settings to prevent direct pushes to main. Require pull requests for all merges. Enforce status checks ensuring CI pipelines pass before merge buttons become available. Require at least one approval from someone other than the author. These mechanical constraints remove reliance on individual discipline alone.

Branch protection applies uniformly regardless of seniority or urgency. Emergency fixes still go through the same process. Speed comes from smaller changes and faster reviews, not from bypassing safeguards.

## Small, Focused Changes

Large pull requests are difficult to review thoroughly. Reviewers skim complex diffs and approve based on trust rather than understanding. Break work into small, self-contained units that can be reviewed in under thirty minutes. Each PR should address a single concern: one bug fix, one feature addition, one refactoring step.

Small changes reduce blast radius when something goes wrong. Reverting a focused commit is safer than untangling a massive merge. Frequent small integrations keep main closer to active development branches, reducing merge conflict severity.

## Automated Validation Gates

CI pipelines must catch issues before human reviewers see them. Run unit tests, integration tests, linting, and type checking automatically on every push. For data engineering projects, include schema validation, dbt compile checks, and DAG parsing verification. Fail fast with clear error messages so developers can self-correct before requesting review.

Do not rely solely on post-merge testing. Catching defects after they reach main means unstable code exists in production or staging environments temporarily. Prevention costs less than remediation.

## Code Review Standards

Reviews verify correctness, maintainability, and adherence to team conventions. They are not rubber stamps. Reviewers should understand the change well enough to explain it to someone else. Ask questions about edge cases, error handling, and backward compatibility. Request changes when standards are not met.

Establish shared expectations through documented guidelines. New team members need explicit criteria for what constitutes acceptable code. Experienced reviewers benefit from consistent standards reducing subjective debates.

## Trunk-Based Development vs Feature Flags

Long-lived feature branches diverge from main and create painful merges. Prefer trunk-based development where work integrates frequently in small increments. Use feature flags to hide incomplete functionality from users while allowing code to exist on main. This keeps the branch short-lived and reduces integration risk.

Feature flags add complexity but provide safety. They allow deploying code that is not yet visible to end users. Toggle features gradually in production rather than risking big-bang releases. Remove flags once features stabilize to avoid technical debt accumulation.

## Monitoring and Rollback Strategy

Stability includes rapid recovery when issues slip through. Monitor key metrics after every deployment. Set up alerts for error rates, latency spikes, or data quality regressions. Have rollback procedures tested and documented. Know exactly how to revert to the previous stable state within minutes.

Automated rollbacks triggered by monitoring thresholds reduce mean time to recovery. Manual intervention during incidents introduces delay and potential for additional errors. Practice rollback procedures regularly so they remain fresh.

## Communication During Incidents

When main breaks, communicate immediately. Notify affected stakeholders about impact and expected resolution time. Post incident summaries explaining root cause and preventive measures. Transparency builds trust even when things go wrong.

Avoid blame-focused language. Focus on systemic improvements rather than individual mistakes. Stable systems result from robust processes, not perfect people.

## Measuring Stability

Track metrics like deployment frequency, lead time for changes, change failure rate, and mean time to restore. These DORA metrics indicate whether your stability practices are effective. Declining metrics signal process degradation requiring attention.

Celebrate stability achievements publicly. Teams maintaining high-quality main branches deserve recognition equal to teams shipping new features. Stability enables sustainable velocity over time.
