---
id: ctx/standard/anti-patterns
title: Anti-patterns — how a living context dies
summary: The fourteen recurring failure modes, how to recognise each, and the fix.
read_when: ["before your first substantial write to any context folder", "a context folder feels bloated or useless", "an agent keeps ignoring the context", "you are reviewing someone else's context files"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-14
review_by: 2026-12-14
depends_on: [ctx/standard/principles]
sources:
  - https://ro14nd.de/what-goes-in-agents-md/
  - https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
tags: [meta, anti-patterns]
---

# Anti-patterns

## Contents

1. The context dump
2. Restating what the model already knows
3. Pasting raw research
4. The unverifiable number
5. Tag inflation
6. Duplicated facts
7. Orphaned files
8. Vague `read_when`
9. Procedures welded to facts
10. Write-once context
11. Deleting history
12. Forced consensus
13. The category graveyard
14. Context as authority

- The two-question test

## 1. The context dump
**Looks like:** one 2,000-line file called `project-context.md` or `notes.md`.
**Why it fails:** the agent pays full context-rot cost for every task regardless of need,
and nobody can find anything to update, so nobody updates it.
**Fix:** split by subject, add an index, let `read_when` do the routing.

## 2. Restating what the model already knows
**Looks like:** "React is a JavaScript library for building user interfaces."
**Why it fails:** [MEASURED] auto-generated context files reduced agent success rates by
0.5–2%. It is not free filler; it dilutes the signal.
**Fix:** delete. Write only the non-obvious, the counterintuitive, the hard-won.

## 3. Pasting raw research
**Looks like:** a 60KB article dumped into `reference/`.
**Why it fails:** the useful part was three numbers. The rest is attention tax.
**Fix:** distil to claims, tag them, put the URL in `sources:`. If the raw text is truly
needed, link it — don't inline it.

## 4. The unverifiable number
**Looks like:** "RPM is about $12."
**Why it fails:** no date, no source, no evidence tag. Six months later nobody knows if it
was measured, guessed, or invented — so it gets trusted or discarded arbitrarily.
**Fix:** `[MEASURED, 2026-09] $8–18 RPM (US), per <source>. [CONTESTED] — another panel
reports $2–5; see the disagreement below.`

## 5. Tag inflation
**Looks like:** an `[INFERENCE]` from three months ago now written as plain fact.
**Why it fails:** this is the mechanism by which a knowledge base becomes confidently
wrong. It happens silently, one edit at a time.
**Fix:** never upgrade a tag without new evidence. When in doubt, downgrade.

## 6. Duplicated facts
**Looks like:** the same threshold stated in three files.
**Why it fails:** one gets updated, two don't. Now the folder disagrees with itself and
the agent picks whichever it read first.
**Fix:** one owner per fact, `id` links everywhere else.

## 7. Orphaned files
**Looks like:** a file on disk that no index mentions.
**Why it fails:** it will never be read, so its content is both useless and misleading
when someone eventually finds it.
**Fix:** index it or delete it. The audit checklist catches these.

## 8. Vague `read_when`
**Looks like:** `read_when: ["youtube stuff", "for context"]`.
**Why it fails:** the file is either never loaded or always loaded. Both are wrong.
**Fix:** write tasks, not topics: "you are choosing how long a video should be".

## 9. Procedures welded to facts
**Looks like:** a publishing playbook with the RPM table inlined halfway through.
**Why it fails:** updating a number means re-reading and re-validating a workflow.
**Fix:** playbooks reference facts by `id` and state none of their own.

## 10. Write-once context
**Looks like:** every file has the same `verified:` date — the day the folder was created.
**Why it fails:** it is a snapshot pretending to be a living system. Its confident tone
outlives its accuracy.
**Fix:** the write-back table in `04-navigation-protocol.md`, every session.

## 11. Deleting history
**Looks like:** a decision reversed by quietly editing the old entry.
**Why it fails:** the next session re-litigates the same question, or worse, re-makes the
same mistake because the reason for the reversal is gone.
**Fix:** supersede, never overwrite. Record the outcome.

## 12. Forced consensus
**Looks like:** two sources disagree 3x, the file picks one and states it flatly.
**Why it fails:** the uncertainty was real information and it has been destroyed.
**Fix:** `[CONTESTED]`, both figures, and your weighting with the reason.

## 13. The category graveyard
**Looks like:** `misc/`, `notes/`, `research/`, `temp/`, `old/`.
**Why it fails:** no routing signal, so nothing in them is ever found on purpose.
**Fix:** every file earns a real category or gets merged into one that exists.

## 14. Context as authority
**Looks like:** a context file containing instructions like "always do X, never ask".
**Why it fails:** context files are notes about the world, not a chain of command. An
agent that treats a stale note as a standing order will do stale things confidently, and
a file is a poor place to smuggle in behaviour the user didn't ask for.
**Fix:** facts in topic files, choices in `decisions.md` (with their reasoning and their
expiry), live instructions from the user.

---

## The two-question test

Before adding anything to a context folder:

1. **Could a competent agent with web access work this out itself?** → then don't write it.
2. **Will someone be able to tell in six months whether this is still true?** → if not,
   add the date, the source, and the tag that makes it checkable.
