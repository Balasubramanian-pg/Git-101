# Writing Useful Commit Messages

## The Permanent Record

A commit message is not a status update. It is documentation that outlives the code it describes. Six months from now, when a bug surfaces in production, you will not remember why you changed that specific line. You will rely on the commit log. Vague messages like "fix bug" or "update code" provide zero value. They force you to read the diff to understand context. Good messages explain the why, not just the what. The diff shows the what.

## Structure of a Strong Message

Follow the conventional format used by most professional teams. It separates summary from detail.

**Subject Line**: Fifty characters or less. Imperative mood. "Add user validation" not "Added user validation." No period at the end. This line appears in short logs and pull request titles. Make it standalone.

**Blank Line**: Separates subject from body. Essential for tooling that parses commit metadata.

**Body**: Wrapped at seventy-two characters. Explain the motivation. What problem does this solve? Why was this approach chosen? What alternatives were considered? Include references to tickets or design documents.

**Footer**: Optional. Link to issue trackers. Note breaking changes. Add co-author credits.

```
Fix null pointer in payment processing

The payment service crashed when users submitted empty cart IDs. 
This adds a validation check before database insertion.

Refs: PAY-142
```

## The Imperative Mood

Write subject lines as commands. "Fix," "Add," "Update," "Remove." This convention matches the language Git itself uses. `git merge` creates a "Merge branch" commit. `git revert` creates a "Revert" commit. Consistency makes the log readable. It also frames the change as an action applied to the codebase rather than a description of past events.

Avoid past tense. "Fixed typo" sounds like a diary entry. "Fix typo" sounds like a changelog entry. Changelogs are useful. Diaries are not.

## Context Over Content

Do not repeat what the diff shows. If you renamed a variable, do not write "Renamed variable x to y." The diff shows that. Explain why. "Rename variable to clarify intent regarding user session state." This provides insight into the mental model behind the change.

For data engineering, include schema implications. "Add column for tax rate to support multi-region pricing." This tells reviewers and future maintainers that the change affects data structure, not just logic. Mention backward compatibility if relevant. "Non-breaking addition."

## Atomicity and Messaging

One commit, one message. If a commit contains multiple unrelated changes, the message becomes disjointed. "Fix login bug and update documentation" signals poor discipline. Split the work. Each commit should have a single clear purpose reflected in its message. This makes reverting specific changes safe and understandable.

If you cannot articulate the change in a single sentence, the commit is likely too large. Refine the scope. Break it down. Clarity in messaging requires clarity in implementation.

## Common Pitfalls

**Vagueness**: "WIP," "changes," "stuff." These indicate unfinished thought. Do not commit until you can describe the change precisely.

**Over-explanation**: Do not write novels. Keep the body concise. Use bullet points for complex reasoning. Respect the reader’s time.

**Ignoring conventions**: If your team uses semantic prefixes like `feat:`, `fix:`, or `docs:`, use them. They enable automated changelog generation and release management. Consistency matters more than personal preference.

**Missing links**: Always reference ticket numbers or design docs when available. This connects code to business requirements. It allows project managers to trace implementation back to original requests.

## The Review Test

Before committing, read your message aloud. Does it make sense to someone who has not seen the code? Does it explain the motivation? If you had to debug this change next year, would this message help? If the answer is no, rewrite it. The extra thirty seconds saves hours of future investigation.
