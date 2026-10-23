---
tags: [testing, data-quality, verification]
source: [claude-conversation 2026-08-27]
---
# Unreachable Tests (When a Stronger Guarantee Hides the One Below It)

When two mechanisms assert the same property at different stages, the earlier and stronger one makes the later one **unreachable**: the later check only ever runs against inputs the earlier one already accepted, so it cannot fail. It reports green forever and inflates the apparent size of a test suite without adding any coverage.

This is distinct from a [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)|vacuous check]], which *does* run but on values too empty or defaulted to discriminate. An unreachable test never sees a failing case at all.

## The shape
Stage A rejects bad input and refuses to publish. Stage B then asserts the same property on what Stage A published. Because B runs strictly downstream of A's gate, B's assertion is implied by A's success.

Common instances:
- A **uniqueness constraint or index** built at load time, plus a uniqueness *test* over the same columns. The build fails first, the old data stays in place, and the test then passes against it.
- A **type system or schema validator**, plus a runtime assertion restating the same invariant.
- A **NOT NULL column**, plus a not-null test on it.
- A **parser that rejects malformed rows**, plus a downstream check for malformed rows.

## What to do
- **Delete the redundant one and say where the guarantee actually lives.** Keep the enforcement at the earliest stage that can refuse to publish; keep the documentation of the grain in the schema description, not in a test that cannot fire.
- **Keep the test where no earlier gate exists.** The same assertion is essential on any artifact built without the constraint — the point is not "tests are redundant", it is that redundancy is per-object, not per-idea.
- Watch for the asymmetric cases: a not-null test is **not** redundant beside a unique index, because most databases permit many nulls in a unique index. Check the exact semantics before deleting.

## The general test
For any green check, ask: **"what input would make this red, and can that input actually reach here?"** If nothing can, the check is decoration. A suite where a meaningful fraction of checks cannot fail is worse than a smaller honest one, because its size is read as coverage.

Corollary: **never trust a test you have not seen fail.** Provoke it once — against a deliberately broken copy, or by asserting the inverse — before counting it as protection.

## When to use
Auditing a test suite that has never gone red; deciding whether to add an assertion that some earlier layer already enforces; explaining why a large suite caught nothing.

## Related
[[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[A Constraint Is Only as Strong as Its Key]] · [[An Absence Check Needs a Namespace Only You Write To]] · [[Two-Stage Reconciliation (Existence Before Amounts)]] · [[Round Inferences Toward the Cheap Error]]
