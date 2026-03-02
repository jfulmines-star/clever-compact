# Clever Compact State File

Use this format when writing `memory/compact-state-YYYY-MM-DD-HH.md`.

---

```markdown
# Compact State — [DATE] [TIME]
_Saved before compact. Restored automatically on session resume._

## Active Workstreams
| Workstream | Status | Notes |
|---|---|---|
| Example: GoH staging branch | blocked | JJ needs to create branch in GitHub |
| Example: ASG LinkedIn page | in-progress | Copy written, JJ to publish |

## Open Tasks
- [ ] [Task description] — owner: [Kit / JJ / Ben / etc.]
- [ ] ...

## Key Decisions
- [Decision]: [Brief rationale or constraint]
- Example: HubSpot key pat-na2-... — do not rotate
- Example: Nick's email is nick@gameofhomes.app — no ASG address

## Credentials & IDs This Session
- [Key name]: [value or "see TOOLS.md"]
- Example: New Vercel project ID: prj_xxx — see TOOLS.md

## Relationship Context
- [Person]: [What to remember]
- Example: Arvind (ProTex): met 2026-03-01, Odoo CRM, needs integration quote

## Remember Flags
- [Explicit user flag]
```
