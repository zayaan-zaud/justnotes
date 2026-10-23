---
tags: [testing, verification, deployment, containerization]
source: [claude-conversation 2026-09-10, rin project]
---
# An Absence Check Needs a Namespace Only You Write To

A check that asks "is this value missing?" is only meaningful if nothing but you can put a value there. When the slot lives in a namespace something upstream also writes — a base image's labels, an inherited environment, a merged config, a subclass attribute — the missing case never presents as missing. It presents as a **plausible wrong value**, and the branch you wrote to catch absence becomes unreachable.

This is the third way a green check can prove nothing, and it is distinct from the other two. A [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)|vacuous check]] runs on values too empty to discriminate. An [[Unreachable Tests (When a Stronger Guarantee Hides the One Below It)|unreachable test]] never sees a failing input because an earlier gate removed it. Here the input *arrives*, looks entirely reasonable, and is someone else's.

## The shape
A deployment guard checked whether a container image carried a build stamp, keyed on the standard `org.opencontainers.image.revision` label. The base image already sets that label. So an unstamped image did not report "no stamp" — it reported the **base image's own commit SHA**, a real-looking 40-hex string from an unrelated repository. The guard's absence branch could never fire, and its mismatch branch printed a confusing upstream SHA as if it were the application's.

It surfaced only because a test run against a knowingly unstamped image returned a SHA at all. Had the value been merely wrong rather than obviously foreign, it would have shipped.

## Where the slot has an upstream owner
- **Container image labels** — `org.opencontainers.*` and any vendor key the base image sets.
- **Environment variables** — anything the base image, orchestrator, shell profile, or CI runner exports; `PATH`-shaped and `*_HOME` names especially.
- **Merged configuration** — defaults from a parent file, chart, or profile that fill a key you meant to require.
- **Class attributes** — a subclass "missing" an attribute that the parent defines.
- **Well-known filenames** — a config the runtime supplies its own copy of when yours is absent.

## What to do
- **Key the check on a name only your build writes**, e.g. a vendor-prefixed label (`com.acme.*`) rather than a standard one. A namespace nobody upstream owns cannot be inherited into.
- Set the standard key **as well**, for external tooling — just never make it the source of truth for your own check.
- **Prove the absence branch fires.** Construct the "missing" case deliberately and watch it go red, per *never trust a test you have not seen fail*. Reading the code is not enough: inheritance is invisible at the call site.
- Prefer an **explicit sentinel you control** (`unknown`, set by your own build arg) over relying on the key being unset, so "unset" and "set to nothing" are distinguishable.
- Treat "the check passed on the very first try, against a case that should have failed" as a **defect signal**, not a success.

## When to use
Writing any guard whose failing condition is "this is absent/unset/empty": deployment provenance and freshness checks, required-config validation, feature-flag defaults, schema fields expected to be null. Also when auditing a guard that has never gone red — ask not just whether a failing input could reach it, but whether the failing input would still *look* failing by the time it arrives.

## Related
[[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[Unreachable Tests (When a Stronger Guarantee Hides the One Below It)]] · [[Baked, Mounted, or Ephemeral]] · [[Orchestrator Status Is Not Ground Truth]] · [[Paired Config Flags That Silently Fall Back]]
