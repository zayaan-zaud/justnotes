---
tags: [sql, postgresql, functions, null-handling]
source: [claude-conversation, 2026-08]
---
# STRICT Functions Short-Circuit on Any NULL Argument

A PostgreSQL function declared `STRICT` (equivalently `RETURNS NULL ON NULL INPUT`) returns NULL **without ever executing its body** if **any** argument is NULL — not just the argument the function actually cares about.

## The trap
A common helper shape is a "try-cast with fallback" function: `try_cast(value, default)`, where `default` is meant to be returned when the cast fails. If every call site passes a literal `NULL::type` as the default (`try_cast(val, NULL::numeric)`), and the function is `STRICT`, then **every single call returns NULL** — the function body (the actual try/catch cast logic) never runs at all, regardless of whether `value` was castable. The failure is silent: no error, no warning, just NULL on every row, indistinguishable from "every source value happened to be uncastable."

## When to use
Before trusting a custom SQL function whose output looks suspiciously all-NULL (or all-default) across an entire dataset, check its `STRICT` modifier and every call site's arguments — a NULL literal in *any* position poisons the whole call, not just the position it was written for.

## Fix
Either drop `STRICT` and handle NULL explicitly inside the function body, or never pass a bare NULL literal into a STRICT function's argument list — pass a typed non-NULL sentinel, or restructure so the NULL-check happens in the caller (`COALESCE`/`CASE`) rather than inside the STRICT call.

## Related
[[A Constraint Is Only as Strong as Its Key]]
