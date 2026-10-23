---
tags: [data-engineering, software-design, dbt]
source: [rin project docs, 2026-08]
---
# Delegate Shared Logic Between Layers Instead of Hand-Syncing Copies

When the same logic needs to exist in two layers of a system (e.g. a dbt macro and a database function doing the same regex/transformation), don't reimplement it a second time and rely on remembering to keep both in sync — have the outer layer call the inner one directly wherever the calling convention allows it. Two copies of the same logic will eventually diverge silently; the only question is whether anyone notices before it matters.

## Example shape of the bug
A dbt macro reimplemented a Postgres function's regex logic independently. The two drifted apart unnoticed and were only re-synced by chance later. On inspection, the two versions happened to produce identical output on the actual data — correct by luck rather than by design.

## When to use
Any two-layer system where logic could live in one place but got duplicated instead: app code mirroring a SQL function, client-side validation mirroring server-side validation, a macro mirroring a stored procedure. Prefer delegation (the outer layer calling the inner one) over "mirror it here" as an ongoing discipline, whenever the outer layer is technically able to call the inner one.

## Related
