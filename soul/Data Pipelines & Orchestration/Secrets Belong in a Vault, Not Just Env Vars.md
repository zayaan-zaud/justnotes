---
tags: [security, secrets-management, deployment, ci-cd]
source: [claude-conversation, 2026-08-31; industry practice, OWASP/HashiCorp Vault conventions]
---
# Secrets Belong in a Vault, Not Just Env Vars

An environment variable is a transport mechanism for a secret, not a storage solution for one — treating `.env`/`export FOO=...` as the source of truth quietly skips the parts of secrets management that actually matter: rotation, audit, scoped access, and revocation.

## What an env var alone doesn't give you
- **No audit trail** — nothing recorded who set it, when, or who has since read it.
- **No rotation story** — changing a leaked credential means finding and editing it everywhere it was copied, by hand.
- **No environment-scoped access** — a `.env` checked out on a laptop for local dev often holds the same production credential a deploy pipeline uses; a dev-only compromise becomes a prod compromise.
- **No revocation** — there's no single place to cut off a credential's access without knowing every place it was pasted.

## The pattern that fixes this
A secrets manager (HashiCorp Vault, or a cloud provider's equivalent) is the **single source of truth**; every env var, CI variable, and local `.env` file is a *downstream, regeneratable copy* of what the vault holds, never the canonical value itself. A CI/deploy runner authenticates to the vault (ideally via short-lived workload identity — OIDC federation rather than a long-lived static token, which is itself a secret that needs managing) and pulls credentials just before they're needed, scoped to only that run.

## When to use
Any credential that, if leaked, causes real damage: database passwords, API keys, cloud credentials. The bar for "needs a vault" isn't project size — it's "would leaking this matter," and for anything touching production data or infrastructure the answer is yes.

## Gotchas
- Migrating to a vault is a project on its own — a smaller, immediately-actionable version of the same principle: never let a production credential be the *only* copy that exists in one person's shell history/laptop/`.env` file (the credential-scoped analogue of [[Containerization Checklist & Task-Execution Privilege Trade-offs]]'s "nothing load-bearing lives on only one workstation" test).
- A `.gitignore`'d `.env` is necessary but not sufficient — it stops the secret from reaching git history, it does nothing about rotation, scoping, or a laptop being compromised.
- Segment by environment explicitly: a credential valid in dev should not also be valid in prod, and a test/CI stage should never have production credentials available to it at all, since a test stage is the likeliest place for an accidental log/print to leak one.

## Related
[[Containerization Checklist & Task-Execution Privilege Trade-offs]] · [[Paired Config Flags That Silently Fall Back]] · [[An Unset Inter-Service URL Defaults to Localhost, Which Inside a Container Means Itself]] · [[Dockerized Postgres - pg_dump Runs Inside the Container, Never the Host]]
