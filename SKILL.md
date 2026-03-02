---
name: clever-compact
description: Preserve critical session context before a compact and restore it cleanly after. Use when: (1) the user asks to compact or context is approaching limits, (2) a compact just completed and the agent needs to reload context, (3) the user says "save state" or "remember this before we compact", (4) the user installs Clever Compact for the first time and needs AGENTS.md wired. Clever Compact is a pre/post-compact intelligence layer — it writes a structured state file before compression and reloads it on resume so the agent wakes up oriented, not amnesiac.
---

# Clever Compact

## What It Does

Before a compact: scan the session, extract what matters, write a state file that survives compression.
After a compact: read the state file and restore context before the user's first message.

## Pre-Compact Flow

1. Run the extraction prompt in `references/extraction-prompt.md` against recent session history
2. Write the result to `memory/compact-state-YYYY-MM-DD-HH.md` using the format in `references/state-template.md`
3. Confirm to the user: "Saved compact state. You're clear to compact."

**What to extract:**
- Active workstreams (name, status: done/in-progress/blocked, blocker/URL if applicable)
- Open tasks with owners
- Key decisions made this session (with rationale if available)
- Credentials or IDs captured this session (or note "see TOOLS.md" if already saved)
- Relationship context (who said what, what they need, what to remember)
- Explicit "remember this" flags from the user

**What NOT to extract:** Full message transcripts, long code blocks, detailed research already saved to files. The state file is a navigation aid, not a transcript.

## Post-Compact Restore Flow

On session start (or immediately after compact fires), check for the most recent state file:

```bash
ls -t memory/compact-state-*.md 2>/dev/null | head -1
```

If found: read it and load its contents into working context before responding. Tell the user:
> "Restored from compact state [date]. I have [N] active workstreams and [N] open tasks reloaded. Ready."

If not found: proceed normally.

## AGENTS.md Hook

Add this block to the workspace AGENTS.md to make restore automatic:

```markdown
## Clever Compact — Post-Compact Restore
After any compact, before responding to the user:
1. Run: ls -t memory/compact-state-*.md | head -1
2. If found: read that file and load context
3. Tell the user you have restored state and list active workstreams
```

## State File Location

Always write to: `memory/compact-state-YYYY-MM-DD-HH.md`
Create the `memory/` directory if it does not exist.

## Reference Files

- **`references/extraction-prompt.md`** — the extraction prompt to run against session history
- **`references/state-template.md`** — the state file format
