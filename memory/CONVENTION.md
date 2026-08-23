# Memory Convention

This project follows the Hub-wide "smart" memory convention so that
lessons learned and canonical references auto-load into every Claude
Code session via the rules-inject.sh hook + Brain rules.

## File naming

| Prefix | Purpose | Example |
|--------|---------|---------|
| `feedback_<slug>.md` | Anti-pattern / lesson learned — encoded mistake. Read BEFORE repeating. | `feedback_dont_commit_without_tests.md` |
| `reference_<slug>.md` | Canonical how-to / state of the system. Read when relevant keyword appears. | `reference_deploy_checklist.md` |
| `daily/YYYY-MM-DD.md` | Per-session log entries. Append-only, one line per significant event. | `daily/2026-05-20.md` |
| `MEMORY.md` | One-line index pointing at every other file. Stays under 200 lines. | (this index) |

## When to write a feedback_*.md

After making a mistake that the agent should not repeat next session:

```markdown
---
name: <short-slug>
description: <one-liner of the rule>
metadata:
  type: feedback
---

**Rule:** <actionable rule in one sentence>

**Why:** <context — what happened, when, who said what>

**How to apply:** <concrete steps / conditions>

**Anti-symptom:** <how to recognise youll repeat this mistake>
```

## When to write a reference_*.md

When canonical state / how-to needs to survive session boundaries:

```markdown
---
name: <short-slug>
description: <what this references>
metadata:
  type: reference
---

<canonical info — IPs, ports, recovery procedures, etc.>
```

## Promoting to Brain rules

Once a feedback_/reference_ file is stable, it should be POSTed to Brain
as a rule with `project_tag=<this-project-name>` so it auto-injects in
the `<rules-context>` block of every future prompt. See
`Hub/scripts/celestron-rules.py` for the pattern.

Hub-wide hooks that read these files:
- `~/.claude/hooks/rules-inject.sh` queries Brain for project-tagged
  rules + injects them into the prompt
- `~/.claude/hooks/arm-aibus-monitor.sh` reads `.aibus-cursor` to decide
  whether to arm a session-length Monitor for cross-machine events
