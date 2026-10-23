---
tags: [dbt, sql, configuration, data-engineering]
source: [claude-conversation, 2026-10-23]
---
# Inline Deployment Lookups at Compile Time So Generated SQL Stays Identical

Models often hard-code deployment facts: GL account codes, the list of bank accounts, a reporting-currency code. To take them out of the code, put them in a lookup table (a gitignored dbt seed keyed by **role**, e.g. `claims_payable`, `bank`) and have a macro read it **at compile time**, emitting the values as literals. The SQL sent to the database stays character for character what it was, so the query plan cannot change.

The alternative, a subquery or join to the lookup at run time, is correct but not equivalent: the planner sees a parameter instead of a constant, so its estimates (and sometimes the plan) change, on exactly the large fact tables where a plan regression hurts.

## The shape (dbt)
```jinja
{% macro gl_accounts(role) %}
  {% set rel = ref('gl_account_role') %}        {# before the execute check: records the dependency at parse time #}
  {% if not execute %}{{ return(['0000000000']) }}{% endif %}
  {% set res = run_query("select account_code from " ~ rel ~ " where role = '" ~ role ~ "'") %}
  {% set codes = res.columns[0].values() | list %}
  {% if not codes %}{{ exceptions.raise_compiler_error("no account for role " ~ role) }}{% endif %}
  {{ return(codes) }}
{% endmacro %}
```
A `gl(role)` wrapper asserts exactly one code. Use it inside the literal: `WHERE account_code = '{{ gl("claims_payable") }}'`. To build a list, loop over `gl_accounts('bank')` into a `VALUES` list.

## When to use
Deployment-specific constants used in filters and `CASE` mappings, where the value is fixed for the whole build. A value that changes per row is data and belongs in a join.

## Gotchas
- **Call `ref()` before the `execute` check**, or parse never sees the dependency and the seed is not ordered before the model.
- The seed must be loaded before the model **compiles** (`dbt seed` before `dbt run`). Give account codes an explicit text type, because a 10-digit code is otherwise inferred as an integer.
- **Prove the refactor is a no-op.** Render each edited model with the seed's values, strip comments and whitespace, and compare it with the previous version. Any difference other than the intended ones is a bug.
- Validate env-sourced scalars inside the macro (a 3-letter currency code, integer weekdays through `map('int')`), because they are pasted into SQL.

## Related
[[Open-Sourcing a Private Repo - Gitignore the Deployment, Publish the Code]] · [[EXPLAIN and EXPLAIN ANALYZE]] · [[ANALYZE and Table Statistics]] · [[Delegate Shared Logic Between Layers Instead of Hand-Syncing Copies]]
