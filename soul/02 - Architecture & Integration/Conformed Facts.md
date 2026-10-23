---
tags: [fact-table, integration, conformed]
source: [Kimball ch.2]
---
# Conformed Facts

If [[Conformed Dimensions]] let you line up rows across fact tables, **conformed facts** ensure the **measurements** themselves are comparable. A fact used in multiple fact tables is conformed only if it has **identical definition, units, and labeling** everywhere.

## The requirement
- Same **technical definition** (what exactly is counted/summed).
- Same **unit of measure** and **currency**.
- Same **name** — and, conversely, if two facts are *not* truly the same, give them **different names** so no one accidentally combines or compares them.

## Why it matters
When [[Drilling Across|drilling across]] processes (e.g., comparing *forecast revenue* to *actual revenue*), the two "revenue" facts must be defined identically or the comparison is meaningless. Divergent definitions with the same name are a common, dangerous source of wrong answers.

## Related
[[Conformed Dimensions]] · [[Drilling Across]] · [[Additive, Semi-Additive, and Non-Additive Facts]] · [[Multiple Units of Measure Facts]]
