---
tags: [dimension, best-practice]
source: [Kimball ch.2-3]
---
# Flags and Indicators as Textual Attributes

A dimension-design best practice: **replace cryptic codes and cryptic Y/N flags with full, descriptive text**. A report grouped by "R / S / C" is useless; grouped by "Returned / Shipped / Cancelled" it's self-explanatory.

## Guidelines
- Decode operational codes into **verbose, human-readable values** stored in the dimension.
- Spell out booleans: not `1/0` but "Preferred Customer" / "Standard Customer".
- Attributes are **row/column headers** in reports — their wording *is* the report's usability.
- Cluster many such flags into a [[Junk Dimensions|junk dimension]] when they don't fit an existing dimension.

## Related
[[Junk Dimensions]] · [[Dimension Tables]] · [[Null Attributes in Dimensions]]
