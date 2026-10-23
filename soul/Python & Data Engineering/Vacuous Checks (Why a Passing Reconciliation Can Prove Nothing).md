---
tags: [data-engineering, testing, data-quality]
source: [rin project docs, 2026-08]
---
# Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)

A check built on a sum, count, or equality can pass while proving nothing, if the values feeding it are silently all-null, all-zero, or otherwise defaulted. A column reading from the wrong position in a source file yields zeros; zeros reconcile against a zero subtotal; the run reports "0 mismatches" while having extracted no real data at all.

## The trap
"0 mismatches" or "test passed" is not evidence of correctness by itself — it's only evidence relative to what the check could actually have detected. A check that can't distinguish "correct" from "vacuously empty" will pass in both cases.

## Verification-strength ladder
Grade every automated check by what it actually proves, not just whether it's green:
- **Tier 1 — reconciled:** sums/counts tie against an independent source, AND the values involved are non-zero/non-trivial.
- **Tier 2 — inferred:** correctness is asserted from something weaker (e.g. header-text pattern matching) without an independent numeric tie-out.
- **Tier 3 — vacuous / unchecked:** the field is never checked by a sum or count at all (common for text fields), so "the run was green" says nothing about it.

State which tier each check earns. A pipeline's overall health claim is only as strong as its weakest load-bearing check.

## When to use
Any automated verification built on equality/sum/count against derived or defaultable values — data parsing, financial reconciliation, test suites asserting on mocked or zeroed fixtures, CI checks that can pass by doing nothing.

## Related
[[Empirical Grain Discovery (Candidate-Key Cardinality Probing)]] · [[Two-Stage Reconciliation (Existence Before Amounts)]] · [[An Absence Check Needs a Namespace Only You Write To]] · [[A Constraint Is Only as Strong as Its Key]] · [[Round Inferences Toward the Cheap Error]] · [[Unreachable Tests (When a Stronger Guarantee Hides the One Below It)]]
