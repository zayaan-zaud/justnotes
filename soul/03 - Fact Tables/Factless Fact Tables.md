---
tags: [fact-table, factless]
source: [Kimball ch.2 & 6 & 13, Adamson ch.12]
---
# Factless Fact Tables

A fact table that records the **occurrence of events** but has **no numeric measured facts** — the "fact" is simply that a combination of dimensions came together. The measurement is a **COUNT of rows**.

## Two classic uses
1. **Event tracking.** One row each time an event happens that has no natural measure: a student attending a class, an employee's HR event, a customer viewing a product. You count rows (attendance, events) and slice by dimensions. Often carries a "counter" fact hardcoded to 1 for convenience.
2. **Coverage / "what did NOT happen".** Records **eligible combinations** so you can find absences. The canonical example: a **promotion coverage** table listing which products were *on promotion* in which stores on which days — needed to answer "which promoted products sold **nothing**?", a question the sales fact table alone can't answer (it has no rows for non-sales).

## Notes
- Adamson devotes a chapter to these; they're more common than beginners expect.
- Distinguish from a fact table that merely *looks* factless but should carry a measure — always check whether a real fact exists at the grain.

## Related
[[Fact Tables]] · [[Grain]] · [[Transaction Fact Tables]] · [[Dimension Tables]]
