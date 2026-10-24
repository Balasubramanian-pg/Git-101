# 06. Best Practices and Hygiene - Explained

## 1. What is Software Development Hygiene?

>[!Tip]
> Software development hygiene consists of the standardized, repeatable practices that maintain codebase stability, readability, and collaborative efficiency over time.

- **Atomic Commits** isolate a single logical change, ensuring version history remains a precise map of development decisions rather than a chaotic dump of working states.
- **Descriptive Commit Messages** provide necessary context for future maintainers, explaining the rationale behind a change rather than merely restating the code modification.
- **Main Branch Stability** relies on automated gating and strict access controls to guarantee the primary integration point always reflects a deployable state.
- **Structured Workflows** govern how feature branches transition into pull requests, defining clear ownership, review requirements, and merge criteria.
- **Review Etiquette** establishes a professional standard for feedback, separating objective code evaluation from personal critique to maintain team velocity and morale.
- **Comprehensive Documentation** (such as Readme files) serves as the primary onboarding mechanism, reducing the cognitive load required to build, test, and contribute to the project.

## 2. Big Picture
![Git feature branch workflow diagram](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d4/Git_branches.svg/1200px-Git_branches.svg.png)
*A standard Git branching model illustrating isolated feature development merging into a protected main branch.*

Development hygiene prevents integration debt. When teams neglect commit structure, review standards, or documentation, the cost of adding new features increases exponentially. The practices outlined here function as a compounding asset. Each well-structured pull request and clear commit message reduces the time required for future debugging and onboarding.

## 3. Core Mechanics and How It Works

| Mechanism | Function | Standard Rule or Formula |
| :--- | :--- | :--- |
| **Small Atomic Commits** | Isolates logical changes to enable precise reverts and clear history. | One logical change equals one commit. Do not mix refactoring with feature addition. |
| **Useful Commit Messages** | Communicates the intent and context of a change to future readers. | Subject line under 50 characters in imperative mood. Body wrapped at 72 characters explaining the "why". |
| **Keeping Main Stable** | Ensures the default branch is always safe to deploy. | Require passing continuous integration checks and minimum two approvals before merging. Disable force pushes. |
| **PR/MR Workflow** | Formalizes the transition from local development to shared codebase. | Open draft PR early. Link tracking issues. Provide test evidence and screenshots for UI changes. |
| **Review Etiquette** | Maintains psychological safety and technical rigor during evaluation. | Frame feedback as questions or suggestions. Distinguish between blocking (must fix) and non-blocking (nitpick) comments. |
| **Code Review Basics** | Systematically evaluates correctness, security, and architectural alignment. | Verify logic first, then readability, then performance. Do not focus exclusively on stylistic preferences automated by linters. |
| **Readme Maintenance** | Provides a single source of truth for project orientation. | Update setup instructions, dependency lists, and contribution guidelines concurrently with structural code changes. |

### Detailed Execution of Core Mechanics

**1. Small Atomic Commits**
Developers often accumulate multiple unrelated changes in a single working directory. The `git add -p` (patch) command allows staging specific hunks of a file. This enables a developer to commit a bug fix and a new feature separately, even if they were written in the same file. Atomic commits simplify `git bisect` operations when tracking down regressions.

**2. Writing Useful Commit Messages**
A commit message must stand alone. A reader should understand the change without inspecting the diff. 
Example structure:
```text
Fix race condition in user session cache

The previous implementation updated the cache after the response 
was sent, causing occasional stale reads under high concurrency. 
This change moves the cache update into the transaction block, 
ensuring atomicity.

Resolves: #402
```

**3. Keeping Main Stable**
Branch protection rules are the primary enforcement mechanism. Configuration must include:
- Require pull request reviews before merging.
- Require status checks to pass before merging.
- Require branches to be up to date before merging.
- Include administrators in these restrictions to prevent emergency bypasses that degrade long-term stability.

**4. Pull Request Merge Request Workflow**
The workflow begins locally. A developer creates a branch named descriptively, such as `feature/add-payment-gateway` or `fix/login-timeout`. The developer opens a draft pull request immediately to signal ongoing work. This invites early architectural feedback before the implementation is finalized. Once ready, the developer marks the PR as ready for review, ensuring all continuous integration pipelines are green.

**5. Pull Request Review Etiquette**
Reviewers must assume positive intent. Feedback should target the code, not the author. Instead of writing "You broke the error handling here," a reviewer writes "This block might throw an unhandled exception if the network drops. Consider adding a try-catch block." Reviewers should use platform features to mark comments as "Pending" or "Resolved" to clarify which items require action.

**6. Code Review Basics**
A systematic review follows a hierarchy of concern. 
First, verify correctness: does the code do what the ticket requests? 
Second, check security: are there injection vulnerabilities or exposed secrets? 
Third, evaluate performance: are there inefficient loops or unindexed database queries? 
Fourth, assess readability: are variable names clear and functions appropriately sized? 
Stylistic disagreements should be deferred to automated formatters.

**7. Readme Documentation**
The Readme file is the first artifact a new developer encounters. It must contain:
- Project description and purpose.
- Prerequisites and installation steps.
- Commands to run tests and start the development server.
- Environment variable configuration examples.
- A link to the broader contribution guidelines.

## 4. The Key Conceptual Point
>[!Note]
> Hygiene is not an optional administrative task; it is the structural foundation that determines the long-term maintainability and velocity of a software project.

- Neglecting hygiene introduces hidden friction. Future developers spend hours deciphering ambiguous commit histories or debugging unstable main branches.
- Consistent practices reduce cognitive load. When every pull request follows the same template, reviewers process the technical content faster.
- Automation enforces baseline standards. Relying on human memory for formatting or basic testing is inefficient. Linters, formatters, and continuous integration pipelines handle repetitive checks, freeing human reviewers for architectural and logical evaluation.

## 5. Types, Variants, and Comparison

| Strategy / Type | Characteristics | Best Use Case | Drawbacks |
| :--- | :--- | :--- | :--- |
| **Squash and Merge** | Combines all commits in a feature branch into a single commit on main. | Feature branches with many iterative, messy "work in progress" commits. | Loses the granular history of how the feature was developed. |
| **Rebase and Merge** | Replays each commit from the feature branch onto main sequentially. | Projects requiring a strictly linear, clean commit history without merge commits. | Can rewrite history, causing conflicts if the branch is shared with other developers. |
| **Standard Merge Commit** | Creates a new commit linking the feature branch and main, preserving all history. | Long-running branches or open-source projects where preserving the exact development timeline is necessary. | Clutters the main branch history with numerous merge commits. |
| **Automated Review** | Bots check formatting, linting, and basic security vulnerabilities. | Every pull request, as a mandatory first gate. | Cannot evaluate business logic correctness or architectural fit. |
| **Peer Review** | Human developers evaluate logic, readability, and edge cases. | Standard feature development and bug fixes. | Subject to human fatigue, bias, and varying levels of expertise. |
| **Architectural Review** | Senior engineers evaluate system-wide impact, scalability, and design patterns. | Major refactors, new service introductions, or database schema changes. | Creates a bottleneck if overused for minor changes. |

Bottom line: Select the merge strategy that aligns with your team's debugging requirements, and layer automated checks beneath human review to maximize efficiency.

## 6. Real-World Examples and Applications

- **Financial Technology Systems**: Require strict atomic commits and detailed commit messages to satisfy regulatory audit trails. Every change to transaction logic must be traceable to a specific ticket and reviewer.
- **Open Source Projects**: Rely heavily on comprehensive Readme files and standardized pull request templates. Contributors are distributed globally and asynchronously, making self-documenting workflows mandatory.
- **Embedded Systems Development**: Utilize rigorous main branch stability rules. Because deploying to hardware is costly and slow, the main branch must pass hardware-in-the-loop simulation tests before any merge is permitted.
- **High-Velocity SaaS Startups**: Implement squash and merge workflows to keep the main branch history clean and readable, prioritizing rapid iteration while maintaining basic automated testing gates.

## 7. Key Takeaways

✅ Atomic commits isolate single logical changes, enabling precise reverts and simplifying regression tracking via tools like git bisect.
✅ Commit messages must use the imperative mood in the subject line and explain the rationale behind the change in the body, not just describe the code modification.
✅ Main branch stability is enforced through branch protection rules, requiring passing continuous integration checks and mandatory peer approvals before any merge occurs.
✅ Pull request workflows should begin with draft states to encourage early feedback, and must always link to corresponding issue tracking tickets.
✅ Review etiquette demands objective, constructive feedback focused on the code itself, clearly distinguishing between mandatory blocking issues and optional suggestions.
✅ Code reviews must prioritize correctness, security, and performance before addressing readability, leaving stylistic enforcement to automated linters.
✅ Readme documentation requires concurrent updates alongside structural code changes to remain a reliable source of truth for onboarding and local development setup.
✅ Automation handles repetitive formatting and basic validation, reserving human cognitive effort for evaluating business logic and architectural alignment.

## 8. Where This Fits

| Stage | What happens | Connection to Hygiene |
| :--- | :--- | :--- |
| **Local Development** | Developer writes code and commits changes. | Enforces small atomic commits and useful commit messages. |
| **Pre-Integration** | Developer pushes branch and opens Pull Request. | Applies PR workflow standards, draft states, and issue linking. |
| **Automated Gating** | Continuous Integration runs tests, linters, and security scans. | Protects main branch stability by blocking substandard code automatically. |
| **Peer Review** | Team members evaluate the proposed changes. | Relies on review etiquette and code review basics to ensure quality. |
| **Merge and Deploy** | Code is integrated into the main branch and released. | Maintains the deployable state of the main branch. |
| **Onboarding** | New developers clone the repository. | Depends entirely on accurate, up-to-date Readme documentation. |

The next phase of the course examines advanced continuous integration pipeline design, building directly upon the branch protection and automated gating concepts established in this module.
