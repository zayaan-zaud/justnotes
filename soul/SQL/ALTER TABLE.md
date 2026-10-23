---
tags: [sql, ddl, reference]
source: [general SQL reference]
---
# ALTER TABLE

**`ALTER TABLE`** is the DDL statement that changes the structure of an existing table — columns, constraints, or the table itself — without dropping and recreating it (and, in most engines, without losing existing data).

## Common forms
- **Change a column's type:**
  ```sql
  ALTER TABLE table_name
  ALTER COLUMN column_name TYPE new_data_type;
  ```
- **Add a column:** `ALTER TABLE table_name ADD COLUMN column_name data_type;`
- **Drop a column:** `ALTER TABLE table_name DROP COLUMN column_name;`
- **Rename a column:** `ALTER TABLE table_name RENAME COLUMN old_name TO new_name;`
- **Rename the table:** `ALTER TABLE table_name RENAME TO new_table_name;`
- **Add/drop a constraint:** `ALTER TABLE table_name ADD CONSTRAINT constraint_name ...` / `DROP CONSTRAINT constraint_name;`

## When to use / gotchas
- Changing a column's type can fail or require an explicit `USING` cast (Postgres) if existing data isn't automatically convertible — e.g. text to integer.
- On large tables, some `ALTER TABLE` operations take an exclusive lock and rewrite the whole table (blocking reads/writes), while others (like adding a nullable column with no default in modern Postgres) are near-instant metadata-only changes. Know which kind you're running before doing it on a production table.
- Prefer additive, backward-compatible changes (add column, don't rename/drop) when a table is actively used by running application code, to avoid breaking it mid-deploy.

## Related
[[CREATE INDEX (PostgreSQL)]]
