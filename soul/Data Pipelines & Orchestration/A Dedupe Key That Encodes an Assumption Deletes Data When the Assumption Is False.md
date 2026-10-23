---
tags: [etl, data-quality, keys, deduplication, idempotency]
source: [claude-conversation, 2026-09-08 — rin claim-paid register re-grain]
---
# A Dedupe Key That Encodes an Assumption Deletes Data When the Assumption Is False

A dedupe key is usually thought of as a description of the grain. Sometimes it is smuggling in a **business assumption** instead — "rows that look like this must be duplicates." When that assumption is wrong, the key doesn't merely mis-group rows: it silently **deletes real records**, and the loss is invisible because the pipeline stays green and the row count only ever goes down.

## The shape of the mistake
A duplicate problem is diagnosed correctly, and the fix reaches for whatever column happened to separate the duplicates in the observed cases:

- observed: the same payment appearing twice, once per agent, with the **identical amount**
- inferred rule: "identical amount across variants = duplicate; a genuine split would divide the total, so each slice would differ"
- key becomes `(entity, document, amount)`

That inference is a claim about the business, not about the data model. It held for the sampled rows and was false in general — a group claim paid for several members carries one row per member with an **equal** share each. Keying on the amount collapsed four real rows into one and destroyed 75% of those payments.

## Why it survives review
- The pipeline is green: dedupe is *supposed* to remove rows, so removal looks like success.
- The count moves in the expected direction. Nobody investigates a table that got smaller after a dedupe fix.
- The original bug being fixed was real, which lends the fix credibility it hasn't earned.
- Tests written against the new key can never catch it — see [[A Constraint Is Only as Strong as Its Key]].

## How to catch it before shipping
- **State the assumption out loud.** If the key includes a measure (an amount, a date, a quantity), you are asserting that two real rows can never share that value. Say that sentence and see whether it sounds true.
- **Reconcile against an independent witness, not against itself.** The four-row split was caught only because a general-ledger line showed a total the deduped table could no longer produce. A dedupe fix should be checked against a source that did not go through the dedupe — see [[Two-Stage Reconciliation (Existence Before Amounts)]].
- **Quantify what the key removes.** "How much money is in the rows this key discards?" is a cheap query and turns an invisible deletion into a number to defend.
- Prefer a key made of **identity** columns over one made of **measures**; measures are values, and values collide legitimately. See [[Natural, Durable, and Supernatural Keys]].

## Related
[[A Constraint Is Only as Strong as Its Key]] · [[Duplicates Within One Load Are Real, Across Loads They Are Revisions]] · [[The Delete Key and the Grain Are Different Things]] · [[Natural, Durable, and Supernatural Keys]] · [[Grain]] · [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]]
