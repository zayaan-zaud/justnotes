---
tags: [tooling, cli, dbt, configuration]
source: [rin project docs, 2026-08]
---
# Paired Config Flags That Silently Fall Back

Some CLI tools have two related configuration flags where setting only one makes the tool silently fall back to a per-user default for the other — and that default can happen to resolve in one environment (a developer's own machine) while failing in another (a container, a CI runner, a scheduled job), with no error pointing at the actual cause.

## Example
dbt's `--project-dir` and `--profiles-dir` are meant to travel together. Passing only `--project-dir` makes dbt fall back to `~/.dbt` for the connection profile. On a developer's laptop, `~/.dbt` is a real directory with real credentials, so it resolves by accident. Inside a container where `HOME=/root` and nothing lives there, the same command fails — and the failure looks like a connection problem, not a "you forgot a flag" problem.

## When to use / gotchas
- Whenever a tool takes two flags that logically belong together (a project location and a config/credentials location, for instance), pass both explicitly rather than relying on one implying the other — even if omitting one "works" in your current environment.
- The dangerous version of this bug is exactly the "works on my machine, fails in CI/production" pattern — treat any config flag with an implicit per-user fallback as a candidate for this trap.
- Check whether the tool version changed the flags from global to per-subcommand (or vice versa) between versions — a flag that used to work before a subcommand can silently stop being recognized after an upgrade.

## Related
[[uv (Python Package Manager)]] · [[Secrets Belong in a Vault, Not Just Env Vars]] · [[Dockerized Postgres - pg_dump Runs Inside the Container, Never the Host]]
