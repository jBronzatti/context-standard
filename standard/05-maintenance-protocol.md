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

| Signal | Meaning | Action |
|---|---|---|
| Past `review_by` | Unverified, not wrong | Use with a caveat; verify if load-bearing |
| >2x past `review_by` | Probably drifted | Verify before using; downgrade `confidence` |
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

Run when a folder feels wrong, before a major project phase, or quarterly.

- [ ] Every file in the folder appears in `INDEX.md`, and vice versa.
- [ ] Every `depends_on` / `supersedes` id resolves to a real file.
- [ ] No two files state the same number independently.
- [ ] Every file has all required front-matter fields.
- [ ] Files past `review_by` are listed and triaged.
- [ ] No untagged numeric claims.
- [ ] `decisions.md` has no entry contradicted by a topic file.
- [ ] `open-questions.md` has no entry that was silently answered elsewhere.
- [ ] No file exceeds the size ceilings in `03-folder-architecture.md`.
- [ ] `AGENTS.md` still describes the folder that actually exists.

Record the audit date in `CHANGELOG.md`. An audit that finds nothing is still worth logging.

## Cost discipline

If maintaining the folder is taking longer than the work it supports, the folder is too
big. Cut reference material, merge thin files, and remember principle 2: **write only what
an agent cannot discover on its own.**
