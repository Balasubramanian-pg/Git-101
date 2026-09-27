# Pull Request Review Etiquette

## The Human Element

Code review is a social interaction wrapped in technical evaluation. The tone you set determines whether the author feels supported or attacked. Critique the code, not the person. "This function lacks error handling" is constructive. "You forgot error handling" is accusatory. The former invites improvement. The latter triggers defensiveness.

Assume competence. The author likely had reasons for their implementation even if those reasons are not immediately visible. Ask questions to uncover context before declaring something wrong. "What happens if this API returns an empty list?" prompts deeper thinking than "This will crash."

## Timing and Responsiveness

Review promptly. A PR sitting unreviewed for days signals that quality is optional. Set aside time daily for reviews rather than batching them weekly. If you cannot review immediately, acknowledge receipt and provide a timeline. "I will review this by tomorrow morning" manages expectations better than silence.

Keep reviews focused. Do not use PRs as teaching moments for unrelated concepts. If you notice a pattern of mistakes across multiple PRs, schedule a separate discussion. The PR comment section is for evaluating the current change, not for broad educational lectures.

## Comment Quality

Be specific. Vague feedback like "looks good" or "needs work" provides no actionable direction. Point to exact lines. Explain why a change is needed. Reference documentation or team standards when applicable. "Use `coalesce()` here to handle nulls per our SQL style guide" is clear. "Handle nulls better" is ambiguous.

Group related comments. Instead of posting ten separate one-line suggestions about variable naming, consolidate them into a single comment summarizing the pattern. This reduces notification noise and helps the author address themes rather than individual nitpicks.

Distinguish between blocking and non-blocking feedback. Label comments clearly. "Nit:" indicates a minor preference that does not prevent approval. "Blocker:" signals a critical issue requiring resolution. This clarity prevents authors from guessing which items are mandatory.

## Handling Disagreement

Disagreement is normal. Two competent engineers can reasonably disagree on implementation details. When this occurs, explain your reasoning with evidence. Benchmarks, documentation links, or precedent from similar projects strengthen your position. Avoid appeals to authority like "because I said so."

If consensus remains elusive, suggest a synchronous discussion. Text-based debates lack nuance and tone. A brief call resolves ambiguity faster than extended comment threads. Escalate to a tech lead only after genuine effort to reach agreement. Do not use escalation as a tactical maneuver to win arguments.

## Approval Standards

Approve only when you have thoroughly reviewed the code and believe it meets team standards. Do not approve conditionally with the expectation that the author will fix issues later. Either resolve all concerns before approving or request changes formally. Conditional approvals create ambiguity about whether the PR is actually ready to merge.

If you are not confident reviewing certain aspects of the code, say so. "I reviewed the Python logic but did not check the SQL transformations. Please have someone with database expertise review that portion." Honesty about limitations prevents false confidence.

## Cultural Considerations

Remote teams span time zones and cultures. Direct communication styles common in some regions may seem harsh in others. Calibrate your language accordingly. Use softening phrases like "Consider..." or "Perhaps we could..." when making suggestions. Emojis can convey tone in text but use them sparingly and professionally.

Non-native English speakers may struggle with idiomatic expressions. Write clearly and avoid colloquialisms. Be patient with language barriers. Focus on technical substance rather than grammatical perfection in comments.

## Learning from Reviews

Treat reviews as bidirectional learning opportunities. Authors learn from reviewer feedback. Reviewers learn by examining different approaches to problems. If you consistently see the same mistake across multiple PRs, propose a linting rule or documentation update to prevent recurrence. Systemic improvements reduce individual review burden over time.

Reflect on your own review patterns. Are you too lenient? Too critical? Do you focus on style over substance? Seek feedback from teammates about your review quality. Continuous improvement applies to reviewers as much as authors.
