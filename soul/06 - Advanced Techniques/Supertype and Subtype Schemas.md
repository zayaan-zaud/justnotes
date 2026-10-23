---
tags: [dimension, heterogeneous, advanced]
source: [Kimball ch.2 & 10, Adamson ch.13]
---
# Supertype and Subtype Schemas (Heterogeneous Products)

Used when a business has **heterogeneous products/services** that share some common attributes but each type also has **many type-specific attributes** — e.g., a bank offering checking, savings, mortgages, credit cards, each with different features.

## Pattern
- **Supertype (core) schema:** a fact table + dimension holding only the attributes/facts **common to all** types, at a common grain. Enables enterprise-wide, cross-product reporting ("total balances across all account types").
- **Subtype (custom) schemas:** for each product type, a fact table + extended dimension with that type's **specific** attributes and facts, for detailed single-type analysis.

Rows exist in both; the supertype gives the unified view, subtypes give the detailed view. Do **not** try to force all products into one giant sparse table with hundreds of mostly-null columns.

## Related
[[Dimension Tables]] · [[Type-Specific Stars (Adamson)]] · [[Null Attributes in Dimensions]]
