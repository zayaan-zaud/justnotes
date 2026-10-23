---
tags: [methodology, measurement, data-quality, event-data]
source: [claude-conversation, 2026-08-29]
---
# Elapsed-Time Effort Proxies Must Control for Task Switching

Event logs rarely record how long anything took, but they do record *when* things happened — so the gap between one actor's consecutive events gets used as a handling-time proxy. It is a good instinct with one structural flaw: **the gap is charged to whichever event came second**, and so it silently absorbs the cost of getting there.

If the actor switched context between the two events, that switch is now attributed to the second event's category. Rare categories get charged for it constantly; bulk-processed categories almost never do. The result is a measure that reports *how a category is scheduled*, dressed up as *how hard it is*.

## The control
Only count gaps where the **previous event was the same category**. Then measure what the discarded gaps were worth — that difference is the finding, not the leftovers.

Worked example — deriving per-product handling time from consecutive data-entry timestamps:
- Gaps following a **different** product: median **26–30 minutes, near-identical for every product**. A constant that flat is not a property of any product; it is the switch itself.
- Gaps following the **same** product: 2 to 14 minutes, genuinely varying by product.

Uncontrolled, every weight was inflated by that constant, and worst for the products handled occasionally between batches of something else.

## It manufactures differences between actors, too
The contamination is not uniform across people. Uncontrolled, one product showed an **18x spread** between the fastest and slowest operator — a finding that read as a real performance gap and would have reached a person's review. Controlled, it collapsed: the "slow" operators were those who handled that product occasionally between other work, and the "fast" ones were those who batched it. The measure was reporting workload composition as individual capability.

That is the sharpest reason to run this control. An uncontrolled elapsed-time proxy does not merely add noise — it produces a specific, plausible, defamatory artefact that correlates with how someone's day is organised.

## Also worth doing
- **Cap the gap** (a couple of hours). Uncapped, it measures lunch, meetings and the overnight break.
- **Exclude system/batch actors.** Their gaps measure a job scheduler. A giveaway is timestamps collapsing to midnight — check the time component's distribution before trusting any actor's gaps.
- **Use the median, not the mean** — the tail is interruptions.
- **Report coverage.** If only 30% of a category's events follow another of the same category, even the controlled median rests on a minority of its rows.

## When to use
Deriving duration from timestamps alone: agent handling time from ticket logs, data-entry effort from record-created stamps, developer time from commit intervals, machine cycle time from event streams. Any time the phrase is "time between consecutive X", ask what happened in between.

## Related
[[A Median Is Not a Weight Until You Check the Spread]] · [[Confounded Measurement Cascades]] · [[LAG and LEAD]] · [[Window Function Frame Clause (ROWS, RANGE, GROUPS)]]
