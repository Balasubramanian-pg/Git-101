# What to Commit

## The Principle of Reproducibility

Commit the minimum set of files required to reproduce the project state from scratch. If a new developer clones the repository and follows documented setup steps, they should arrive at a working system without needing external artifacts. This principle excludes generated outputs but includes everything necessary to generate them. The boundary lies between source and artifact. Source is committed. Artifact is derived.

## Source Code and Logic

All handwritten code belongs in version control. Python scripts, SQL queries, dbt models, and DAG definitions represent intellectual effort and business logic. These files change intentionally through human decision. Tracking their evolution provides audit trails, enables rollback, and facilitates collaboration.

Include utility functions and helper modules even if they seem trivial. Small shared libraries prevent duplication across pipelines. Versioning them ensures that all consumers use the same implementation. When a bug is found in a utility, fixing it in one place and updating the version propagates the correction systematically.

## Configuration Templates

Configuration structure matters more than specific values. Commit template files showing required keys, data types, and default settings. `config/template.yaml` or `.env.example` documents the interface between code and environment. New developers read these files to understand what credentials or endpoints are needed.

Exclude actual values containing secrets or environment-specific paths. The template provides the map. Local configuration provides the territory. Keeping them separate allows secure distribution of sensitive values while maintaining public documentation of requirements.

## Dependency Specifications

Lockfiles pin exact versions of libraries. `requirements.txt`, `Pipfile.lock`, `package-lock.json`, or `poetry.lock` ensure deterministic builds. Without lockfiles, two developers installing dependencies on different days may receive different minor versions. Subtle behavioral changes in dependencies cause "it works on my machine" failures.

Commit lockfiles alongside source code. They are part of the reproducible state. Updating dependencies requires modifying the lockfile and committing the change. This creates a clear history of when and why library versions shifted. Review dependency updates carefully for security implications and breaking changes.

## Infrastructure as Code

Terraform files, CloudFormation templates, and Kubernetes manifests define the execution environment. These files are code. They undergo review, testing, and versioning just like application logic. Committing them ensures that infrastructure changes are tracked alongside the code they support.

Avoid manual console configurations. If a resource exists only in the cloud provider’s interface, it is invisible to version control. Drift occurs when manual changes diverge from declared state. Enforce discipline by defining all resources in code and committing those definitions.

## Documentation and Metadata

README files explain project purpose, setup instructions, and usage patterns. Commit them. They are the first point of contact for new contributors. Keep them current. Outdated READMEs create friction and mistrust.

Data dictionaries describe column meanings, valid ranges, and business context. Commit these alongside the models they describe. In dbt projects, YAML files containing column descriptions generate documentation automatically. Treat these YAML files as critical metadata. Changes to column semantics require updating the documentation simultaneously.

Architecture decision records capture why certain technical choices were made. Commit these to preserve institutional knowledge. Future developers need context about why a particular partitioning strategy or orchestration tool was selected. Without this history, they may repeat past mistakes or undo beneficial decisions.

## Tests and Fixtures

Test code validates production logic. Commit unit tests, integration tests, and end-to-end validation scripts. They prove that the code behaves correctly under defined conditions. They also serve as executable documentation showing how to use various components.

Small synthetic datasets used for testing belong in the repository. These fixtures represent edge cases like null values, empty inputs, or extreme numbers. They enable automated testing without requiring access to production data. Keep fixtures minimal. Large datasets belong in object storage with references in test code.

## Build and Automation Scripts

Scripts that compile, package, or deploy the project are part of the development workflow. Commit Makefiles, shell scripts, or CI/CD configuration files like `.github/workflows/` or `.gitlab-ci.yml`. These define how the project transitions from source to execution.

Exclude local build artifacts but include the instructions for creating them. The script is the recipe. The output is the meal. Commit the recipe. Discard the meal after consumption.

## The Exclusion Test

Before committing, ask whether the file can be regenerated from other committed files. If yes, exclude it. Compiled binaries, generated documentation HTML, and processed datasets fail this test. They are derivatives. If no, commit it. Source code, configuration templates, and dependency locks pass this test. They are primary inputs.

Ask whether the file contains secrets. If yes, exclude it immediately. No exception. Ask whether the file is specific to your local machine. If yes, exclude it. IDE settings, local paths, and personal preferences do not belong in shared repositories.

## Maintaining Hygiene

Review commits before pushing. Ensure no accidental inclusions slipped through. Use `git status` to verify the staging area matches your intent. Remove unrelated files. Split large commits into logical units. Keep the repository clean. A cluttered repository signals neglect. A curated repository signals professionalism.
