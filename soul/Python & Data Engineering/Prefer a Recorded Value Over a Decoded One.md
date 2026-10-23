---
tags: [data-engineering, data-quality, modeling, keys]
source: [claude-conversation 2026-08-27]
---
# Prefer a Recorded Value Over a Decoded One

When an attribute can be obtained two ways — **read from a field the source system actually recorded**, or **decoded out of an identifier's internal structure** — the recorded field is almost always better, and the gap is usually much larger than it looks before measuring.

Decoding an identifier feels authoritative because the pattern is visible and self-consistent. But it is an inference about a naming convention, and conventions have generations: an identifier scheme that changed at some point leaves the decoder correct only for the era it was written against, silently returning nothing (or the wrong thing) for the rest.

## Why decoding under-performs
- **The scheme has versions.** A legacy encoding and a current one coexist; a decoder keyed on one covers only its own era.
- **It cannot express "unknown".** A recorded field can be null; a substring is always *something*, so failure looks like data rather than absence.
- **The mapping table drifts from the identifiers.** A lookup keyed on decoded tokens can be perfectly internally consistent while matching almost nothing live.

## What to do
- **Inventory where the attribute is recorded before building a decoder.** It is common for the same attribute to sit, already populated, in a field nobody joined on — sometimes on several sources at once.
- **Measure the routes against each other rather than assuming.** State coverage as a percentage of rows *and* of value (money, volume), because they can rank the routes differently.
- **Layer them, best first,** and carry a column saying which route answered. That makes the weak route's contribution visible instead of invisible, and lets a consumer drop back to only the strong one.
- **Keep the decoder as a fallback rather than deleting it** — it typically covers exactly the rows the recorded field cannot reach, usually old records that predate the recording system.
- When a decoded token and a recorded field disagree, **the recorded field wins**, and the disagreement is worth reading: it is often a real generational boundary rather than an error.

## Cross-check trick
A decoded value is best validated against a **closed domain**, not a plausible range. Recovering a categorical field to a value that genuinely exists in a known vocabulary is strong evidence; recovering a numeric field to a plausible magnitude is weak, because many wrong answers are also plausible.

## When to use
Choosing a join key or a classification source; auditing a model that derives an attribute by slicing an identifier; explaining low coverage on a lookup that appears internally sound.

## Related
[[A Constraint Is Only as Strong as Its Key]] · [[Empirical Grain Discovery (Candidate-Key Cardinality Probing)]] · [[Conformed Dimensions]] · [[Round Inferences Toward the Cheap Error]] · [[Classify the Response Body, Never the Status Code]]
