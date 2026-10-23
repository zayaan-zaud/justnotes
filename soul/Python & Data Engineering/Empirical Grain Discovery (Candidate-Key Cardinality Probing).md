---
tags: [data-engineering, etl, data-modeling, testing]
source: [rin project docs, 2026-08]
---
# Empirical Grain Discovery (Candidate-Key Cardinality Probing)

When a table's true dedupe key is uncertain, don't guess and test after the fact — build a shortlist of candidate keys and run `count(distinct candidate_key)` for each against a known window, then pick the one that hits the true row count exactly.

## Method
1. Establish ground truth for a window: the real number of distinct records the source describes (an independent count, or a known total).
2. Shortlist candidate keys, narrowest to widest (e.g. `(a, b)`, then `(a, b, c)`).
3. Compute `count(distinct <candidate>)` for each over the same window.
4. The correct grain is the *narrowest* candidate whose count matches ground truth exactly — a narrower candidate that undercounts is too coarse (rows fan out on a dimension it's missing); a wider candidate that also matches is redundant padding, not a real grain requirement.

## When to use / gotchas
- A `UNIQUE` index or dbt `unique_key` enforcing the wrong grain still shows green — it just silently discards the rows that collide.
- A test asserting a *superset* of the true grain (extra columns beyond what's needed) can never catch a duplicate-grain bug, because it's a strictly weaker assertion than the true key — see [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]].
- Repeat the probe across multiple independent windows before trusting the result — a candidate key that happens to work for one window's data shape isn't proof.

## Related
[[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[Two-Stage Reconciliation (Existence Before Amounts)]] · [[A Constraint Is Only as Strong as Its Key]] · [[Prefer a Recorded Value Over a Decoded One]]
