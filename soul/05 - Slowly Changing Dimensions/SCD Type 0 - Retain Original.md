---
tags: [dimension, scd]
source: [Kimball ch.2 & 5]
---
# SCD Type 0 — Retain Original

The attribute value is **fixed and never updated**, no matter what the source does. The warehouse deliberately ignores changes.

## Use for
Attributes that are **durable by nature** or defined as "original value": date of birth, original credit score, "date first opened", the original SKU. Also used when the business rule explicitly says "always show the original."

## Contrast
- [[SCD Type 1 - Overwrite|Type 1]] always shows the latest; Type 0 always shows the first.
- Different attributes **in the same dimension** can each have different SCD types — a customer's birth date is Type 0 while their address is Type 2.

## Related
[[Slowly Changing Dimensions]] · [[SCD Type 1 - Overwrite]]
