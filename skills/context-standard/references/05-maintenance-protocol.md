---
id: ctx/standard/maintenance
title: Maintenance protocol — keeping a living context alive
summary: Verification, staleness handling, supersession, contradiction resolution, changelog discipline, and the periodic audit.
read_when: ["you are updating an existing context file", "a review_by date has passed", "two files disagree", "you are auditing a context folder", "a fact turned out to be wrong"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-14
review_by: 2026-12-14
depends_on: [ctx/standard/file-format]
tags: [meta, maintenance]
---

# Maintenance protocol

## Contents

- The edit rules
- Verification
- Staleness
- Supersession
- Contradiction resolution
- The audit
- Cost discipline

A context folder has exactly one failure mode that matters: **it becomes confidently
wrong**. Everything here is aimed at that.

## The edit rules

1. **Read the whole file before editing it.** Partial edits on partially-read files are
   how contradictions get introduced.
2. **Update, don't overwrite.** "Now 30-day retention (was 90 until 2026-09; changed
   because storage cost tripled)" is worth more than the new number alone.
3. **Bump `verified:` only when you actually re-checked the claim** against its source.
   Re-reading the file is not verification. Fixing a typo is not verification.
4. **Touch `INDEX.md` in the same edit** that adds, removes, renames or repurposes a file.
5. **Append to `CHANGELOG.md`** for anything a future reader would be surprised by:
   reversals, deletions, promotions, corrections. Not for typos.
6. **Never delete a decision.** Mark it superseded, record the outcome, link forward.

## Verification

To verify a claim: open its source, confirm the claim still holds, update the figure if it
moved, set `verified:` to today, recompute `review_by` from `volatility`.

If the source is gone: mark the claim `[CONTESTED]` or downgrade `confidence`, and add an
`[OPEN]` item for finding a replacement source. A claim whose source has vanished is not
automatically false, but it is no longer `[OFFICIAL]`.

Cheap verification first: the highest-value re-checks are `volatility: fast` files whose
claims are load-bearing for a decision. Do not re-verify static conceptual files on a
schedule; verify them when something contradicts them.

## Staleness

Every file has exactly one freshness verdict on a given date, computed from `review_by`
and `volatility` alone. *interval* is the review interval of the file's current
`volatility`: `static` 12 months, `slow` 6, `fast` 2.

| Verdict | When | Meaning | Action |
|---|---|---|---|
| `fresh` | today ≤ `review_by` | Within its review interval | Use |
| `unverified` | `review_by` < today ≤ `review_by` + interval | Unverified, not wrong | Use with a caveat; verify if load-bearing |
| `drifted` | today > `review_by` + interval | Probably drifted | Verify before using; downgrade `confidence` |

Date arithmetic, so that every tool gets the same answer:
- Dates are calendar days (`YYYY-MM-DD`); time of day and time zone play no part.
- Adding *n* months keeps the day of the month; if that day does not exist in the target
  month, use the month's last day: 2026-08-31 + 6 months = 2027-02-28. The same rule
  computes `review_by` from `verified`.
- `drifted` is measured from `review_by`, not from `verified`: `slow`, verified 2026-08-31,
  gives `review_by` 2027-02-28 and drifted after 2027-08-28, not 2027-08-31.

Two signals are not computed from dates:

| Signal | Meaning | Action |
|---|---|---|
| Source 404s | Provenance lost | Downgrade to `[INFERENCE]`, open a question |
| Contradicted by observation | Wrong | Fix now; check neighbours for the same error |

A stale file is not deleted. Deleting removes the record that anyone ever believed it.

## Supersession

When a file is replaced:
1. New file gets `supersedes: [old-id]`.
2. Old file gets `status: superseded` and a first line: `SUPERSEDED by <new-id> on <date>. Kept for history.`
3. `INDEX.md` points at the new one; the old one drops out of the index tables but stays
   on disk.
4. `CHANGELOG.md` records the swap and the reason.

## Contradiction resolution

Two files disagree. In order:

1. **Are they actually about the same thing?** Most apparent contradictions are scope
   differences. State the scopes and both are right.
2. **Compare `verified:` dates and evidence tags.** `[OFFICIAL]` beats `[MEASURED]` beats
   `[INFERENCE]`. Newer beats older *at the same tag level*, not across levels.
3. **If both survive:** mark the subject `[CONTESTED]` in the owning file, state both
   positions, both sources, and which you weight higher **and why**. This is a legitimate
   end state. Forced resolution is worse than honest disagreement.
4. **If it blocks a decision:** escalate to the user with the two positions and what it
   would cost to find out.

## The audit

When to run it, the checklist, and how to record it live in `../SKILL.md` § Audit checklist.

## Cost discipline

If maintaining the folder is taking longer than the work it supports, the folder is too
big. Cut reference material, merge thin files, and remember principle 2: **write only what
an agent cannot discover on its own.**
