# When to Use DVC or Git LFS

## The Size Problem

Git compresses text efficiently. It stores each version of a file as a delta against previous versions. This works well for code. It fails for binary data. A 10GB Parquet file modified slightly still requires storing significant new data. Repeated changes inflate repository size exponentially. Cloning becomes slow. Storage costs rise. Performance degrades.

Two solutions address this: Git LFS and DVC. They solve different problems. Choosing the wrong one creates operational friction.

## Git LFS: Large File Storage

Git LFS replaces large files with text pointers in the repository. The actual file content lives on a separate server. When you clone, Git downloads the pointers. When you checkout a specific commit, LFS fetches the corresponding large files. This keeps the Git repository small while allowing access to large assets.

Use Git LFS for static binary assets that change infrequently. Model weights in machine learning projects. Large images or video files in media applications. Compiled libraries or datasets that are versioned but not transformed frequently.

LFS integrates tightly with Git. Commands feel native. `git add`, `git commit`, and `git push` work as expected. The complexity is hidden. However, LFS has limitations. It does not understand data content. It treats every file as an opaque blob. It does not track lineage between datasets. It does not support data versioning beyond simple file replacement.

## DVC: Data Version Control

DVC sits on top of Git. It tracks metadata about data files rather than the files themselves. It stores data in remote storage like S3, GCS, or Azure Blob. It uses Git to track pointers to these remote locations. Unlike LFS, DVC understands data pipelines. It links code, parameters, and data together.

Use DVC for dynamic data workflows. Machine learning experiments where datasets evolve through preprocessing steps. Data engineering pipelines where intermediate outputs feed into subsequent stages. Scenarios requiring reproducibility of entire experiments, not just individual files.

DVC excels at tracking lineage. It knows that model v2 was trained on dataset B, which was derived from dataset A using script C. Changing script C invalidates downstream artifacts. DVC detects this and flags necessary re-runs. LFS cannot do this. LFS sees only files. DVC sees relationships.

## Comparison Criteria

**File Type**: LFS handles any binary file. DVC prefers structured data but works with binaries. If you track model weights only, LFS suffices. If you track weights, training data, and preprocessing scripts together, DVC provides better context.

**Change Frequency**: LFS suits static assets. Download once, use many times. DVC suits iterative data. Preprocess, train, evaluate, repeat. DVC caches intermediate results to avoid redundant computation. LFS redownloads entire files on every checkout.

**Collaboration Model**: LFS works well when teams share identical datasets. Everyone needs the same model weight file. DVC works well when teams experiment independently. Each developer may generate different intermediate datasets. DVC tracks these variations without cluttering the main repository.

**Storage Backend**: LFS requires a dedicated LFS server or hosted service like GitHub LFS. Pricing scales with storage and bandwidth. DVC uses existing cloud storage buckets. You control costs directly. No vendor lock-in for storage.

**Complexity**: LFS adds minimal complexity. Install the extension, track files, push. DVC introduces new concepts: stages, metrics, plots, and remote configurations. The learning curve is steeper. The payoff is greater for complex workflows.

## Decision Framework

**Choose Git LFS if**:
- You need to version large binary files like models, images, or archives.
- Files change infrequently.
- You do not need to track data lineage or pipeline dependencies.
- Your team prefers simple Git-like workflows.
- You already use a platform with built-in LFS support.

**Choose DVC if**:
- You manage data pipelines with multiple processing steps.
- Reproducibility of experiments is critical.
- You need to track relationships between code, parameters, and data.
- Datasets evolve through iterative preprocessing.
- You want to leverage cloud storage for cost-effective scaling.

**Use Neither if**:
- Data fits comfortably in standard Git repositories (under 100MB total).
- Data is generated on-the-fly and does not need versioning.
- Data resides exclusively in databases or data warehouses managed separately.

## Hybrid Approaches

Some teams use both. LFS for static model weights deployed to production. DVC for experimental datasets and training pipelines. This combines simplicity for stable artifacts with flexibility for active development. Ensure clear documentation distinguishing which tool manages which files to prevent confusion.

## Migration Considerations

Moving from LFS to DVC or vice versa requires rewriting history. This disrupts all collaborators. Plan migrations carefully. Communicate timelines. Provide scripts for updating local clones. Test thoroughly before announcing completion. Prevention through initial correct choice avoids these painful transitions.
