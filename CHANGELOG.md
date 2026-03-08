# Clever Compact — Changelog

---

## v2.0.0 — March 8, 2026
**Full rewrite: native OpenClaw Context Engine plugin**

> Requires OpenClaw ≥ 2026.3.7

---

### For everyone

Your AI agent has a memory problem. When the context window fills up, OpenClaw compacts the session — and your agent forgets what you were doing. Mid-task. Mid-conversation. Sometimes mid-sentence. You end up re-explaining things you already explained. It's frustrating, and in a live demo or client session, it's embarrassing.

Clever Compact 2.0 fixes it permanently.

Before every compaction, the agent now writes a structured snapshot of everything it was working on — active tasks, decisions made, things to remember, open questions. The moment it wakes up after the compact, it reads that snapshot and picks up exactly where it left off.

v2 is different from v1 in one important way: it's no longer just instructions in a skill file. It's a native plugin that hooks directly into OpenClaw's compaction system. The save happens automatically, every time, no matter what. You don't have to ask. You don't have to time it. It just works.

**Installation dropped from 5 steps to 2:**
1. `openclaw plugins install ./clever-compact`
2. Add `"contextEngine": "clever-compact"` to `plugins.slots` in your config

No AGENTS.md editing. No heartbeat entries. No manual triggers.

---

### For developers

v2 ships as a native `ContextEngine` plugin using the new plugin slot introduced in OpenClaw 2026.3.7. Two lifecycle hooks replace the v1 heartbeat-based approach entirely:

**`compact({ session })`** — fires synchronously before every compaction. Writes a structured Markdown state file to `<workspace>/memory/compact-state-YYYY-MM-DD-HHmm.md`. Returns `{ ok: true, compacted: false }` — passes through to the legacy compaction engine. Zero interference with existing compaction behavior.

**`assemble({ messages })`** — fires on every context window build. Finds the most recent state file (within configurable TTL, default 72h), injects it as a prepended system message. Returns `estimatedTokens` for context budget accounting.

**`ingest()`** — pass-through. No modification to ingestion behavior.

Registered via `api.registerContextEngine("clever-compact", factory)`. Activated via:

```json
{
  "plugins": {
    "slots": {
      "contextEngine": "clever-compact"
    }
  }
}
```

**Breaking from v1:** Remove the Clever Compact blocks from `AGENTS.md` and any heartbeat entries before enabling v2 — they'll conflict with the plugin's restore injection and produce duplicate context.

**Requires:** OpenClaw ≥ 2026.3.7

---

## v1.3.1 — March 6, 2026
Clarified config/reference language in state template and extraction prompt. No functional changes.

## v1.3.0 — March 5, 2026
Fully free. All features available at no cost. Pro tier removed.

## v1.2.0 — March 4, 2026
**Framing update — now speaks the language of the problem**
- Reframed the opening around the three failure modes: `/new` amnesia, compaction loss, memory drift
- Language mirrors how power users actually describe the problem — makes it immediately recognizable
- No functional changes to the skill itself

## v1.1.0 — March 3, 2026
**Reduce compaction frequency**
- Added `reserveTokens` tuning guide — lower from 50,000 to 15,000 to push compaction to ~185k/200k context
- Roughly 3–4× fewer compactions per heavy session
- Updated urgency language: compaction takes up to 10 minutes, not seconds. Real discovery from live power-user sessions.

## v1.0.0 — March 1, 2026
**Initial release**
- Core pre-compact scan: active workstreams, key decisions, open tasks, credentials, relationship context, remember flags
- State file written to `memory/compact-state-YYYY-MM-DD-HH.md`
- Post-compact restore hook via AGENTS.md
- State file format spec (Markdown + embedded JSON)
