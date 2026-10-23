---
tags: [python, data-engineering, etl, pattern]
source: [claude-conversation, 2026-07]
---
# Reshaping Print-Layout Exports (Flatten-to-Record Pattern)

A recurring ETL problem: a source system (e.g. Crystal Reports) exports Excel that's laid out like a *printed page*, not a *table* — one logical record is smeared across several physical rows, interleaved with label/subtotal rows. The pattern: **walk the rows and rebuild one clean row per record.**

## Building blocks
1. **Grid the sheet.** Convert the messy sheet into a plain list of lists of clean strings (`None` → `""`, everything else `str().strip()`). This normalizes the working surface before any logic touches it.
2. **Find the header by content, not position.** Don't assume data starts at a fixed row — search for the row containing known header labels. Real exports have variable title/filter blocks on top.
3. **Map columns by name, not number.** Read the header row once and build a `{field_name: column_index}` dict. Hardcoded column numbers ("premium is always column 12") break the moment a column is empty and gets dropped — column position is quicksand; column *labels* are stable.
4. **Use `while` with a manual index for variable step sizes.** When one logical record spans a variable number of physical rows (e.g. 4 rows per record vs. 1 row per label), a `for` loop (fixed step of 1) can't express that — use `while i < len(rows)` and advance `i` by 4 for a record, 1 for a label. Forgetting to skip the extra rows (`i += 4` vs `i += 1`) silently corrupts every later record by misreading a data row as a label.
5. **Guard cell access.** Wrap `row[j]` in a bounds check (`0 <= j < len(row)`, else `""`) instead of letting a short row crash the loop — columns shift, so "does this cell exist" must be checked constantly.

## When to use
- Any time a data source is exported for human/print reading rather than machine consumption: multi-row records, floating labels, inconsistent column presence.
- General techniques that recur here and transfer broadly: comprehensions as "loops that build a collection", `enumerate` for index+item together, slicing for grabbing a fixed-size block (`rows[i:i+4]`), dict-based lookup tables instead of positional logic, `next(generator, default)` for "first match or fallback."

## Gotchas
- The genuinely hard part is the *domain* (variable-row records, drifting columns, interleaved labels) — the Python itself is basic control flow dressed in intermediate-style syntax (type hints, dict merging, nested comprehensions).
- To actually internalize the pattern: build a tiny fake 8-row export as a list of lists and hand-write the loop that rebuilds 2 records from it, rather than just reading the real implementation.

## Verification method: PROFILE → DECLARE → VERIFY
Reshaping a printed-layout export needs a disciplined three-step method, not just "read and reshape":
1. **Profile** the file with counts, not by scrolling — establish record height and field positions from measured evidence (see row-shape fingerprinting below), not from eyeballing one sample record.
2. **Declare** a spec: which fields exist, which are amounts, what row span each record occupies, which rows are section/label rows.
3. **Verify** by summing parsed records back to the report's own printed subtotals. This step is not optional, and — critically — passing it is not sufficient on its own: see [[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] for how a wrong-position column can still "reconcile" against a zero subtotal.

## Row-shape fingerprinting
Reduce every row to the tuple of its populated column indices, and count how often each shape occurs. One technique, three uses:
- **Confirms record-block height** by requiring independent shape-counts to agree, instead of trusting one sample record.
- **Exposes silently-dropped columns** — a shape's count must fully account for the total record count; an unexplained shape means an unread field or a misclassified row.
- **Surfaces columns populated in every row shape**, which breaks naive "row has exactly one populated cell = section label" detection — a column that's always present can masquerade as a label-row signal if you're not counting shapes.

## Modal-gap block-height inference
When record blocks are separated by anchor rows (e.g. an integer serial column), the true block height is the *modal* gap between anchors — and that inference is trustworthy specifically when no gap is *smaller* than the mode. Gaps larger than the mode are legitimate section breaks; a gap *smaller* than the mode means variable-length blocks, and the fixed-height assumption must be abandoned rather than patched.

## Related
[[Vacuous Checks (Why a Passing Reconciliation Can Prove Nothing)]] · [[Empirical Grain Discovery (Candidate-Key Cardinality Probing)]]
