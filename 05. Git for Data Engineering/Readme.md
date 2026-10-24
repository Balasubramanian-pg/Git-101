# Git Practices in Data Engineering

## 1. What is Version Control for Data Projects
> Version control in data engineering applies strict repository management to pipeline code, configuration templates, orchestration DAGs, and analytical models while explicitly excluding raw data and credentials.

- **Deterministic Environments** ensure that pipeline execution yields identical results regardless of the machine running the code.
- **Artifact Separation** dictates that source code and configuration templates remain in Git, while large data files and compiled binaries reside in dedicated storage systems.
- **Reproducible Workflows** rely on tracking exact versions of dependencies, database schemas, and transformation logic to guarantee auditability.
- **Notebook Hygiene** prevents execution outputs and local metadata from polluting version history, keeping repositories focused on source logic.
- **Secrets Management** isolates authentication tokens from the codebase, relying on environment variables or dedicated vaults at runtime.

## 2. Big Picture
![Data pipeline version control architecture](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e0/Git_branching_model.svg/1200px-Git_branching_model.svg.png)
*A branching model adapted for data projects, separating experimental notebook work from production pipeline code.*

Data projects require stricter boundaries between exploration and production than traditional software engineering. Version control systems track the logic transforming data, not the data itself. This separation prevents repository bloat and protects sensitive information from accidental exposure.

## 3. Core Mechanics and Execution

| Mechanism | Function | Standard Rule or Command |
| :--- | :--- | :--- |
| **Notebook Hygiene** | Prevents execution outputs and local metadata from polluting version history. | Strip outputs before committing using `nbstripout` or Jupyter native settings. |
| **Secrets Management** | Keeps credentials out of the repository history permanently. | Store secrets in environment variables. Add `.env` to `.gitignore`. |
| **GitIgnore Configuration** | Filters untracked files automatically during staging. | Ignore local virtual environments, compiled Python files, and temporary data exports. |
| **DVC Integration** | Tracks large datasets and machine learning models outside the main Git repository. | Use DVC for files exceeding 50MB. Git tracks the small `.dvc` pointer files. |
| **dbt Model Versioning** | Manages incremental changes to SQL transformation logic and testing. | Commit `.sql` model files, `schema.yml` tests, and documentation together in atomic commits. |

### Detailed Execution of Core Mechanics

**Notebook Hygiene**
Notebook environments store execution counts and output payloads in the `.ipynb` JSON structure. Committing these creates massive merge conflicts and obscures code changes. Stripping outputs reduces the file to pure source code. Developers install `nbstripout` and configure it as a Git filter. This automatically cleans notebooks upon staging. Production logic eventually moves from notebooks into standard Python modules.

**Secrets Management**
Secrets management requires proactive exclusion. Once a credential enters Git history, rotating the credential is the only secure response. Rewriting history with `git filter-repo` serves as a secondary mitigation. Developers use `.env` files for local development. These files load variables into the application environment at runtime. Production systems fetch these values from AWS Secrets Manager or HashiCorp Vault.

**GitIgnore Configuration**
A comprehensive `.gitignore` file prevents accidental commits of local artifacts. Data projects require specific exclusions beyond standard software templates. Developers must ignore `.csv`, `.parquet`, and `.db` files generated during local testing. Python-specific exclusions include `__pycache__/`, `.pytest_cache/`, and `venv/`. Orchestration tools like Airflow generate local SQLite databases that must also be excluded.

**DVC and Git LFS Integration**
DVC operates by storing data in remote storage like Amazon S3. The Git repository only contains a text file with the data checksum. This maintains a link between specific code versions and specific data versions. Git LFS replaces file content with a text pointer in Git. LFS suits small binaries like serialized machine learning models. DVC handles gigabyte-scale datasets and feature stores.

**dbt Model Versioning**
dbt projects require coordinated commits. A change to a SQL model often requires a corresponding update to the `schema.yml` file for testing. Developers commit the model, the test, and the documentation update in a single atomic commit. The `target/` compilation directory is always ignored, as it contains generated artifacts.

## 4. The Key Conceptual Point
> The repository must contain only the instructions for generating artifacts, never the artifacts themselves.

- Code defines the transformation. Data acts as the input.
- Configuration templates define the structure. Actual credentials fill that structure at runtime.
- Documentation explains the system. Local environment paths remain irrelevant to other developers.
- Version history tracks the evolution of logic. Storing data in Git corrupts this history with irrelevant binary diffs.

## 5. Storage and Tracking Method Comparison

| Method | Best Use Case | Size Constraints | Versioning Mechanism |
| :--- | :--- | :--- | :--- |
| **Standard Git** | Source code, SQL files, YAML configs, documentation. | Excellent for text files under 100KB. | Content-addressable storage using SHA-1 hashes. |
| **Git LFS** | Small binaries, serialized machine learning models, large configuration files. | Handles files up to a few hundred megabytes. | Replaces file content with a text pointer in the Git tree. |
| **DVC** | Large datasets, feature stores, heavy model weights. | Designed for gigabyte to terabyte scale. | Generates `.dvc` files containing checksums, linking to remote storage. |
| **Object Storage** | Raw data lakes, intermediate pipeline outputs, final analytical tables. | Unlimited scale. | Managed via bucket versioning or external metadata catalogs. |

Bottom line: Git manages text and logic. DVC or object storage manages data and large binaries.

## 6. Real-World Applications

- **Orchestration Systems**: Airflow or Prefect DAG files are committed as Python scripts. Connection details remain in the orchestration UI or secret manager.
- **Transformation Layers**: dbt projects commit all `.sql` models, `packages.yml`, and `dbt_project.yml`. The `target/` compilation directory is ignored.
- **Exploratory Analysis**: Jupyter notebooks are committed only after clearing cell outputs. Production logic is eventually refactored into standard Python modules.
- **Machine Learning Pipelines**: DVC tracks the training dataset version and the resulting model artifact. This ensures the exact model can be rebuilt from the committed code.

## 7. Key Takeaways

✅ Commit source code, configuration templates, and documentation while ignoring raw data and compiled artifacts.
✅ Strip Jupyter Notebook outputs before committing to prevent merge conflicts and repository bloat.
✅ Add `.env`, `venv/`, and `__pycache__/` to `.gitignore` to protect local environment states and secrets.
✅ Use DVC or Git LFS for files exceeding standard Git size limits, keeping the main repository lightweight.
✅ Rotate any credential immediately if it is accidentally committed, regardless of subsequent history rewriting.
✅ Group dbt model changes, schema tests, and documentation updates into single atomic commits for coherent review.
✅ Store production connection strings in environment variables or dedicated secret management systems, never in plaintext files.
✅ Configure automated Git filters to enforce notebook hygiene across the entire development team.

## 8. Where This Fits

| Stage | What happens | Connection to Git Hygiene |
| :--- | :--- | :--- |
| **Local Development** | Developer writes SQL, Python, or configuration files. | Applies notebook hygiene and `.gitignore` rules to exclude local artifacts. |
| **Commit Staging** | Developer stages changes for version control. | Separates logic from data, ensuring no secrets or large files enter the index. |
| **Peer Review** | Team evaluates the proposed pipeline changes. | Reviewers verify that DAGs, models, and tests are complete and correctly formatted. |
| **Continuous Integration** | Automated systems run linting, SQL parsing, and unit tests. | CI validates the committed code against project standards before merging. |
| **Deployment** | Orchestration system pulls the approved code to execute pipelines. | The deployed environment fetches secrets independently of the Git repository. |

The next phase of the course examines advanced continuous integration pipeline design, building directly upon the branch protection and automated gating concepts established in this module.
