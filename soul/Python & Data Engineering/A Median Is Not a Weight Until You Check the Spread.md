---
tags: [methodology, statistics, data-quality, measurement]
source: [claude-conversation, 2026-08-29]
---
# A Median Is Not a Weight Until You Check the Spread

A median summarises a distribution honestly. It becomes a **weight** — a per-row multiplier — only if the mass actually sits near it. Those are different claims, and the second one needs its own check that almost nobody runs.

The failure is quiet: the median is computed correctly, looks reasonable, and gets multiplied through thousands of rows. Nothing errors. The output is a confident-looking score built on a number that describes almost none of its inputs.

## The check
Report the share of observations landing within a band around the median — ±50% is a serviceable default — alongside the median itself. Publish it as a column, not a footnote, so a consumer cannot pick up the weight without also picking up its confidence.

Worked example — deriving how long each product takes to process, to make a raw item count comparable across products:

| class | p25 | p50 | p75 | within ±50% of median |
|---|---|---|---|---|
| routine, high volume | 1 | 2 | 3 | **81%** |
| routine, high volume | 1 | 2 | 6 | **65%** |
| complex | 6 | 14 | 32 | 37% |
| complex | 2 | 7 | 24 | 20% |

The top two are safe to multiply by. The bottom two are not: that work genuinely ranges from 6 to 32 units, and no single multiplier represents it. Same median calculation, same sample sizes, opposite verdicts.

## What to do when it fails
Do not fall back to a mean, and do not fabricate a weight from a smaller sample. **Stratify instead of weighting** — put the dimension you were trying to weight *into the grain*, so comparison is like-for-like by construction and needs no exchange rate at all. Weighting exists to collapse a mix into one number; if the mix cannot be collapsed honestly, keep it visible and let the reader judge it.

Note the two conclusions can coexist: differences *between* groups can be robust while the per-row weight *within* each is not. When one group's p75 sits below another's p25, the groups genuinely differ even though neither has a usable internal weight.

## When to use
Any time an aggregate becomes a coefficient — effort/complexity weights, cost-per-unit allocations, story-point calibration, expected-duration SLAs, normalising counts across heterogeneous categories. The tell is the word "average" appearing in a formula rather than in a report.

## Related
[[Confounded Measurement Cascades]] · [[Elapsed-Time Effort Proxies Must Control for Task Switching]] · [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[Round Inferences Toward the Cheap Error]]
