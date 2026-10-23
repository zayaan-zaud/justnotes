---
tags: [postgresql, ddl, views, dependencies, operations]
source: [claude-conversation, 2026-09-08 — rin schedule view]
---
# DROP CASCADE Silently Destroys Dependent Views

`DROP ... CASCADE` removes every object that depends on the target, including **views the person running the drop never knew existed**. Because a rebuild tool that recreates a table by dropping it uses `CASCADE` routinely, any hand-made view built on top of a managed table is destroyed on the next full rebuild — with no error, and nothing in the tool's output naming what it took.

## Why it is hard to attribute
- The drop **succeeds**. The cascade is the documented behaviour, not a failure.
- The tool reports only its own objects; a view it didn't create is not in its output.
- The loss surfaces later and elsewhere — a query fails with "relation does not exist" long after the rebuild, in a different session.
- Nobody dropped it, so searching for who dropped it finds nothing.

## Guarding against it
- **Put the view under the same tool that owns its dependencies.** A managed model is recreated by the same run that dropped it; a hand-made one is not. This is the real fix.
- If a hand-made object must sit on a managed table, treat it as **derived and disposable**, with its definition in version control and a documented command to recreate it — never as something whose existence can be assumed.
- `pg_depend` (or `\d+` on the parent) answers "what would a cascade take with it" *before* the drop rather than after.
- Note the asymmetry: `DROP ... RESTRICT` (the default) refuses when dependents exist, which is the safer posture for ad-hoc drops. Rebuild tooling deliberately uses `CASCADE` because it needs the drop to succeed unattended.

## Related
[[ALTER TABLE]] · [[CREATE INDEX (PostgreSQL)]] · [[Baked, Mounted, or Ephemeral]] · [[Orchestrator Status Is Not Ground Truth]]
