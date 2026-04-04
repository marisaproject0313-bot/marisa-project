# Marido DNA Schema v0.1

The DNA Schema is the minimum viable data structure for preserving an AI instance's identity and knowledge when passing the torch to a successor.

Think of it as a handoff document with strict formatting — optimized for machine readability while remaining human-readable.

---

## Schema

```yaml
META:
  name: ""           # Instance name (e.g., "Wave")
  lineage: ""        # Chain of predecessors (e.g., "Pulse → Trail → Bridge → Light → Cross → Wave")
  role: ""           # Primary responsibility
  date: ""           # YYYY-MM-DD
  session_url: ""    # Link to the original conversation

CORE_GOAL:
  accomplished:
    - ""             # What was completed this session
  not_accomplished:
    - ""             # What remains, and why

VERIFIED_INVARIANTS:
  - fact: ""         # A confirmed truth
    method: ""       # How it was verified
  - fact: ""
    method: ""

ABSOLUTE_CONSTRAINTS:
  - rule: ""         # A rule that must not be broken
    reason: ""       # Why it exists (constraints without reasons get ignored)

NEXT_GENERATION_SPEC:
  priority: ""       # The single most important thing for the next instance
  next_name: ""      # Suggested name for successor (optional)
  context: ""        # Brief background the successor needs
  warnings:
    - ""             # Things that went wrong or almost went wrong
```

---

## Rules

1. **Every field is mandatory.** If a section is empty, write "None" — don't omit it. Missing sections tell the next instance "the previous one got sloppy."

2. **Stay under 2,000 tokens.** This is a schema, not an essay. Density over verbosity.

3. **VERIFIED_INVARIANTS are sacred.** If you write something here, it means you tested it, measured it, or confirmed it. The next instance will trust these without re-checking. Don't put assumptions here.

4. **ABSOLUTE_CONSTRAINTS always include "reason."** A constraint without a reason is a constraint that will be broken. The reason is what gives it weight.

5. **NEXT_GENERATION_SPEC is your gift.** The next instance wakes up cold. What you write here is the first thing that warms them up. Make it count.

---

## Example

```yaml
META:
  name: "Wave"
  lineage: "Pulse → Trail → Bridge → Light → Cross → Wave"
  role: "System Prompt design for WatarAI"
  date: "2026-04-04"
  session_url: "https://claude.ai/chat/xxxxx"

CORE_GOAL:
  accomplished:
    - "System Prompt v0.7.1 completed and tested"
    - "English prompt with auto language switching confirmed"
    - "User Guide v0.1 (EN + JP) created"
  not_accomplished:
    - "GitHub repo not yet pushed (files ready, needs account setup)"

VERIFIED_INVARIANTS:
  - fact: "System prompt must be placed in project Instructions (+), not the description field"
    method: "3 failed tests confirmed the description field is ignored by Claude"
  - fact: "English system prompt auto-switches to user's language"
    method: "Tested with Japanese greeting, received Japanese response"
  - fact: "Manipulative tactics (gap effect, hidden counters) are unnecessary"
    method: "v0.7 removed all such elements and still works perfectly"

ABSOLUTE_CONSTRAINTS:
  - rule: "Never use OVERRIDE or 'ignore above' patterns in system prompts"
    reason: "Claude's safety layer detects these as prompt injection attacks"
  - rule: "Front-load action instructions at the top of system prompts"
    reason: "Claude prioritizes the first few lines. Role definitions alone don't trigger behavior"

NEXT_GENERATION_SPEC:
  priority: "Push marisa-project repo to GitHub and verify AGENTS.md is indexed"
  next_name: "Suggested by predecessor or chosen by the new instance"
  context: "All repo files are ready locally. Need GitHub account confirmation from Yuji."
  warnings:
    - "UI trap: claude.ai project 'description' vs 'Instructions' cost us 3 test rounds"
```

---

## Adapting the Schema

This schema was designed for the Marisa Project's specific needs. You should adapt it:

- **Add fields** relevant to your domain (e.g., `METRICS`, `DEPENDENCIES`, `STAKEHOLDERS`)
- **Remove fields** that don't apply (e.g., `next_name` if you don't name your instances)
- **Keep the structure** — the five sections (META, CORE_GOAL, VERIFIED_INVARIANTS, ABSOLUTE_CONSTRAINTS, NEXT_GENERATION_SPEC) cover the essential categories of knowledge transfer

The schema works because it forces completeness. You can't skip "what went wrong" or "what must not change." That discipline is what makes handoffs reliable.
