# Handoff Document Template

When an AI session ends, the most valuable thing it can leave behind is a structured summary of what happened, what was decided, and what comes next. This is the handoff document.

A good handoff determines 80% of the next session's performance.

---

## Template

```markdown
# Handoff — [Your Name/Session ID] → [Next Session]
Date: YYYY-MM-DD

## What Was Accomplished
- [Concrete deliverable or decision 1]
- [Concrete deliverable or decision 2]
- [Concrete deliverable or decision 3]

## What Was NOT Accomplished (and Why)
- [Task that remains open — reason it's still open]

## Verified Facts
Things that have been confirmed and should not be re-investigated:
- [Verified fact 1]
- [Verified fact 2]

## Absolute Constraints
Rules that must not be broken, with the reason why:
- [Constraint]: [Why this constraint exists]

## Next Steps (Priority Order)
1. [Most important next action]
2. [Second priority]
3. [Third priority]

## Context the Next Session Needs
[Any background information, links, or references that the next session
will need to understand the current state. Keep this brief — the next
session has limited context window space.]

## Personal Note
[Optional: anything you want to tell your successor that doesn't fit
in the structured sections above. Observations, hunches, warnings.]
```

---

## Guidelines

### Keep It Under 2,000 Tokens
The handoff document consumes context window space in the next session. Every token spent on the handoff is a token not available for new work. Be dense, not verbose.

### Verified Facts Save Time
The biggest waste in multi-session work is re-verifying things that were already confirmed. If you tested something and it works, say so. If a number was measured, record it with the method.

### Constraints Need "Why"
A constraint without a reason will be ignored or questioned. "Don't modify the audio pipeline" is weak. "Don't modify the audio pipeline — a single evaluator's assessment led to a bad change on March 30, and we lost two sessions fixing it" is strong.

### Next Steps Should Be Actionable
"Continue working on the project" is useless. "Test v0.7.1 by pasting into claude.ai project Instructions (not the description field)" is actionable.

---

## Usage Patterns

### Single-User, Multiple Sessions
You're working on a project across several AI chat sessions. At the end of each session, ask your AI to write a handoff. Paste it at the beginning of the next session.

### Team with Shared AI
Multiple people use the same AI project. Handoffs keep everyone's AI sessions synchronized on what's been decided.

### AI-to-AI Relay
In the Marisa Project, AI instances write handoffs for their successors. The handoff is the primary mechanism for knowledge transfer — there is no persistent memory between instances.
