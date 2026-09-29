# Pull Request and Merge Request Workflow

## The Social Contract

A pull request is not a technical notification. It is a request for review, a record of decision-making, and a boundary between isolated work and shared code. Treat it as such. The quality of your PR description determines the quality of the review. Vague titles like "Fix bug" or "Update code" force reviewers to dig through diffs to understand intent. Clear descriptions provide context so reviewers can focus on correctness rather than discovery.

## Anatomy of a Strong PR

**Title**: Summarize the change in imperative mood. "Add user authentication middleware" not "Added auth stuff." Keep it under fifty characters if possible.

**Description**: Explain what changed and why. Link to relevant tickets or design documents. Describe the problem being solved, not just the solution implemented. Include screenshots for UI changes or sample output for data pipelines.

**Testing**: Document how you verified the change. List manual test steps. Reference automated test coverage. For data engineering, include before-and-after row counts or schema comparisons.

**Checklist**: Use platform-specific checkboxes to track completion. "Updated documentation," "Added tests," "Verified backward compatibility." This signals thoroughness to reviewers.

## Reviewer Responsibilities

Reviewers are gatekeepers of quality and maintainability. Their job is not to find every typo but to identify architectural issues, logical errors, and violations of team standards. Respond within twenty-four hours when possible. Delayed reviews block progress and encourage authors to bypass the process.

Focus on high-impact feedback first. Logic errors, security vulnerabilities, and performance issues take precedence over style preferences. Use questions rather than commands. "Have you considered handling null values here?" invites discussion. "Change this line" dictates without context.

Approve only when you understand the change and believe it meets standards. Requesting changes should be specific and actionable. Do not approve with minor comments expecting the author to fix them later. Either resolve all issues or request changes formally.

## Author Responsibilities

Respond to feedback promptly. Acknowledge suggestions even if you disagree. Explain your reasoning when rejecting feedback. Push additional commits to address requested changes. Do not force push unless absolutely necessary and only on personal branches.

Keep PRs small. Aim for changes reviewable in thirty minutes. Large PRs overwhelm reviewers and increase the likelihood of missed defects. If a feature requires extensive changes, break it into stacked PRs where each builds on the previous one.

## Handling Feedback

Not all feedback requires action. Distinguish between mandatory fixes and optional suggestions. Mandatory items include bugs, security issues, and violations of documented standards. Optional items include style preferences or alternative implementations that achieve the same result.

When disagreements arise, discuss synchronously if possible. Text-based debates escalate quickly. A five-minute call resolves issues that generate twenty comments. Escalate to a tech lead or senior engineer if consensus cannot be reached. Do not let PRs stagnate due to unresolved philosophical debates.

## Platform Differences

GitHub calls them Pull Requests. GitLab calls them Merge Requests. Bitbucket uses Pull Requests. The terminology differs but the mechanics are identical. Each platform offers slightly different features for inline commenting, approval workflows, and merge strategies. Learn your team's specific tool but understand that the underlying principles remain constant regardless of interface.

## Post-Merge Cleanup

Delete the source branch after successful merge. Stale branches clutter the repository and confuse contributors about active work. Most platforms offer automatic deletion settings. Enable them. Update any related tickets or project management items to reflect completion.

Monitor the merged code in production. Be prepared to address issues that surface only under real workload conditions. The PR workflow does not end at merge. It ends when the change proves stable in production.
