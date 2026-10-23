---
tags: [sqlite, flask, backup, tooling, web]
source: [claude-conversation, 2026-10-01, cflx project]
---
# SQLite Behind a Small Web App: WAL, Server-Side Prices, .backup

For a low-traffic site (a small shop, a booking list), one SQLite file is the whole database: no server to run, and "backup" means copying one file correctly. The defaults need three adjustments to be safe behind a multi-worker web server.

## Mechanics
- **Per-connection pragmas.** `journal_mode=WAL` lets readers proceed while one writer writes (it persists in the file). `foreign_keys=ON` is **off by default and per connection**, so set it in the function that opens every connection or your `REFERENCES` are decoration. `busy_timeout` makes a second writer wait instead of failing with "database is locked".
- **Open a connection per request, close it on teardown.** Don't share one connection across gunicorn workers or threads.
- **Never trust totals from the client.** The browser sends `product id + size + colour + qty`; the server looks up the price, validates the options against the product, and computes the total. A forged `"price": 1` in the payload is simply ignored.
- **Snapshot name and unit price into the order line.** Later catalogue edits must not rewrite what a past customer was charged.
- **Seed once.** Load starter data only when the table is empty, so a redeploy never overwrites live edits.
- **Backup with `sqlite3 db ".backup 'out.db'"`**, not `cp`: it is a consistent online copy even mid-write, whereas copying a WAL-mode file can miss the `-wal` contents.

## When to use / gotchas
- Fits one server and modest writes. Needing several app servers writing at once is the point to move to Postgres.
- The DB file must sit outside any directory a deploy overwrites or web server serves; see [[rsync --delete Turns the Server Into a Mirror of the Source Directory]].
- Keep the admin password in an env file with mode 600, not in the repo: [[Secrets Belong in a Vault, Not Just Env Vars]].
- Serialise schema changes carefully even here; the same staging applies as in [[Expand-Contract for Backward-Compatible Schema Migrations]].

## Related
[[Static Site Behind nginx - Serve Files, Skip the App Server]] · [[rsync --delete Turns the Server Into a Mirror of the Source Directory]] · [[Dockerized Postgres - pg_dump Runs Inside the Container, Never the Host]] · [[Secrets Belong in a Vault, Not Just Env Vars]]
