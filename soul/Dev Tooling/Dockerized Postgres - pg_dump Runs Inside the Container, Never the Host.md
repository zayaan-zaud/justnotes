---
tags: [docker, postgres, backup, cli, tooling]
source: [claude-conversation, 2026-09-16]
---
# Dockerized Postgres: pg_dump Runs Inside the Container, Never the Host

`pg_dump`/`pg_restore` are version-sensitive against the server they talk to. When Postgres runs in Docker, the reliable way to back it up is to `docker exec` the dump/restore commands inside the server's own container — using its own bundled client — rather than relying on a host-installed `pg_dump` that may be a different major version.

## Mechanics
- Stream the dump straight from `docker exec` stdout to a host file in one step: `docker exec <container> pg_dump -U <user> -d <db> -Fc > host_backup.dump`. Writing to a file inside the container first means a second `docker cp` out plus cleanup — same result, more steps.
- Supply the password via the `PGPASSWORD` env var, not a CLI flag — a flag's value shows up in `ps aux` and shell history, while `PGPASSWORD` is process-environment only and never printed. The official Postgres image already sets `POSTGRES_USER`/`POSTGRES_PASSWORD`/`POSTGRES_DB` as container env vars from the compose file, so reference those (`PGPASSWORD="$POSTGRES_PASSWORD" pg_dump -U "$POSTGRES_USER" -d "$POSTGRES_DB" ...`) rather than retyping the literal password anywhere in a command.
- Prefer custom format (`-Fc`) over plain SQL text: it's compressed, and it unlocks selective restore later (`pg_restore --list` to see the table of contents, `-t sometable` to pull just one object) instead of committing to an all-or-nothing SQL replay.

## When to use / gotchas
- A version mismatch between a host-installed `pg_dump` and a newer server doesn't always fail with an obviously-relevant error — treat "dump/restore behaves strangely" as a version-check prompt before assuming a data problem.
- A custom-format dump is a binary blob, but it's still cheap to verify without touching the live database: it starts with the 5 magic bytes `PGDMP`, and `pg_restore --list < file` (run the same way, piped into the container) prints its whole table of contents — a truncated or failed dump shows up immediately as a short or unreadable listing.
- Restoring is the mirror image: `pg_restore` also has to run inside the container against the same version of server it will write to.

## Related
[[Paired Config Flags That Silently Fall Back]] · [[Secrets Belong in a Vault, Not Just Env Vars]] · [[Docker Named Volumes (Ownership & Mount-Point Gotchas)]]
