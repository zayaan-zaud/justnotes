---
tags: [tooling, git, open-source, security]
source: [claude-conversation, 2026-10-23]
---
# Open-Sourcing a Private Repo - Gitignore the Deployment, Publish the Code

A repo built for one organisation mixes two things: **code** (how the system works) and **deployment data** (whose system it is). To publish the code without breaking the running deployment, keep every deployment file on disk, gitignore it, and move every deployment value out of code into config. Deleting the data would break the working install, and leaving it in leaks it.

## The pass, in order
1. **Inventory by grep, not memory.** Search for the organisation's name, people, personal paths, internal hosts and IPs, customer names, real record identifiers (claim/policy/account numbers), money figures and volume figures. Comments, test fixtures and docs leak more than code does.
2. **Files needed to run → gitignore and untrack** (`git rm --cached`), never delete: reference CSVs, certificates, working notes, internal runbooks. Publish a README listing each ignored file and its columns so a new deployment knows what to supply.
3. **Values inlined in code → config.** Hostnames, user ids, paths, timezone, weekend days, currency codes and account codes go to `.env` (or a gitignored lookup table), and each new key goes in `.env.example`. The default must be **neutral and safe**: a placeholder host (`.invalid`), `UTC`, or a value that matches nothing.
4. **Examples in comments → synthetic values of the same shape.** Keep the format (digit count, leading zeros, separators) so the comment still teaches, and keep paired examples consistent (`012345` vs `12345`).
5. **Verify the index, not the working tree:** `git grep --cached`, then `git checkout-index -a --prefix=<scratch>/` and run lint and tests on that copy. That copy is exactly what a stranger clones.
6. **Publish from a fresh root commit** if the private history ever held the data ([[What Git Can Undo and What It Cannot]]).

## Gotchas
- **An empty default can match everything.** `name ILIKE '' || '%'` matches every row. Guard with `NULLIF(value, '')` so an unset value matches nothing.
- **Locally built images and the build cache hold the ignored files.** `COPY .` bakes the working tree, so delete both (`docker rmi`, `docker builder prune`) and never push that image.
- **The organisation may own the code.** Work done as an employee usually belongs to the employer, so get permission before publishing.
- **Identifiers leak too.** Module, table and column names that name a vendor, regulator or currency survive a prose scrub. Renaming them breaks a restored deployment, its schedules and its consumers, so make that a deliberate decision, not part of the cleanup.
- **Tests that assumed the local config** (a timezone, say) must pin it themselves, or CI runs them against the neutral default.
- **CI may call tooling that never got committed** (a Makefile). Run the workflow's own commands on the clean copy.

## Related
[[Stage Explicit Paths - git add -A --dry-run Shows What a Sweep Would Take]] · [[What Git Can Undo and What It Cannot]] · [[Secrets Belong in a Vault, Not Just Env Vars]] · [[Inline Deployment Lookups at Compile Time So Generated SQL Stays Identical]] · [[Test a Cold Start on Scratch Volumes, Never Under the Real Names]]
