---
tags: [sql, schema-design, migrations, deployment, ci-cd]
source: [claude-conversation, 2026-08-31; industry pattern, aka parallel change]
---
# Expand-Contract for Backward-Compatible Schema Migrations

A three-phase way to change a schema that a live system reads/writes without a moment where the schema and the code disagree — split one risky migration into several individually-safe deploys instead of coupling a schema change to a code change in the same release.

## The three phases
1. **Expand** — add the new shape (a new column, a new table) alongside the old one; nothing reads it yet, so this deploy is a no-op for behavior.
2. **Migrate** — backfill the new shape from the old, and cut code over to write (and eventually read) the new shape, while the old shape is still present and correct. Old and new code paths can coexist during this phase.
3. **Contract** — once every reader/writer is confirmed on the new shape, drop the old column/table. Only this final step is destructive, and by the time it happens nothing depends on the old shape anymore.

## Why it exists
A single migration that renames/retypes/drops a column *and* ships the code requiring the new shape has no safe middle state: if the schema change and the code deploy don't land atomically (they almost never do, across a rolling deploy or a replica lag), something is reading with the wrong assumption. Splitting the change means every individual deploy is independently rollback-safe — rolling back the code in step 2 doesn't require rolling back the schema, because the old shape is still there.

## When to use
Any schema change to a table something in production actively reads or writes — renaming, retyping, dropping, or changing the nullability of a column; splitting or merging tables. Not needed for a genuinely additive change with no existing readers (a new empty table, a nullable column nothing yet writes to) — that's already backward compatible by construction.

## Gotchas
- The temptation is to skip straight to a single migrate-and-drop step "because this is a small change" — the risk isn't the size of the change, it's whether a deploy boundary can ever be crossed with the schema and code in disagreement.
- The contract phase is easy to forget once things are working — an abandoned expand phase leaves two shapes of the same fact live indefinitely, which is its own maintenance and correctness hazard (a query written against the wrong one silently gets stale data).
- This is about *live-read/write* safety, not warehouse-style append-only ingestion — an analytics table nothing queries mid-load doesn't need this ceremony; see [[Baked, Mounted, or Ephemeral]] for the adjacent question of what actually needs to survive a redeploy.

## Related
[[Baked, Mounted, or Ephemeral]] · [[Paired Config Flags That Silently Fall Back]] · [[A Constraint Is Only as Strong as Its Key]]
