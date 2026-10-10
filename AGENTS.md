---
id: ctx/agents
title: How to navigate this context folder
summary: Entry point and routing rules for any AI agent working in or with this folder.
read_when: ["you are an agent operating in this folder", "you need to find the right context file", "you are about to write or edit a context file anywhere"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-24
review_by: 2026-12-14
tags: [meta, routing, entrypoint]
---

# AGENTS.md — read this first

This folder (`context/`) is **the standard**. It does not contain project knowledge.
It defines *how* every other context folder in this workspace is written, organised,
navigated and maintained — including itself.

If you are here because you landed in a project folder and followed a link: you want
`standard/` (the rules) or `templates/` (the files to copy). Then go back.

## The 30-second version

1. **Never load a whole context folder.** Read `INDEX.md`, decide which 1–3 files your
   current task actually needs, read only those.
2. **Every file declares its own routing** in YAML front matter (`read_when`, `status`,
   `confidence`, `volatility`, `verified`). Trust that metadata to decide what to open.
3. **Every claim is tagged** with its epistemic status — `[OFFICIAL]`, `[MEASURED]`,
   `[INFERENCE]`, `[CONTESTED]`, `[ASSUMPTION]`, `[DECISION]`, `[OPEN]`. Never silently
   promote an inference to a fact.
4. **The nearest `AGENTS.md` wins.** A deeper folder's rules override a shallower one's.
   The user's live instruction overrides everything written here.
5. **If you learn something durable, write it down before you finish the turn.** A living
   context that nobody updates is a dead context in about three weeks.

## Reading protocol (progressive disclosure)

```
task arrives
  └─ read the nearest AGENTS.md          (≈150 lines, always)
       └─ read INDEX.md of that folder    (≈100 lines, always)
            └─ match task → read_when triggers
                 └─ read 1–3 topic files  (≈200–400 lines each)
                      └─ follow depends_on links ONLY if still blocked
```

Stop as soon as you can act. Opening a fourth file is usually a sign you are researching
rather than working — which is fine, but say so.

**Why it is done this way:** model accuracy degrades as the context window fills
("context rot"), because n tokens create n² pairwise attention relationships and training
data skews short. The objective is the *smallest set of high-signal tokens* that gets the
job done, not the largest set of possibly-relevant ones. See `standard/01-principles.md`.

## Writing protocol

Before you create or edit any context file anywhere in this workspace:

- `standard/02-file-format.md` — front matter fields and the claim-tagging vocabulary.
- `standard/03-folder-architecture.md` — where the file goes and what it is allowed to contain.
- `standard/05-maintenance-protocol.md` — how to update without corrupting what is there.
- `standard/06-anti-patterns.md` — the failure modes. Read this one at least once, fully.
- `templates/` — copy, do not improvise.

Hard rules, in short:
- One subject per file. A fact about X lives in X's file and nowhere else.
- Target ≤ 300 lines per file. Past ~400, split it and add both halves to the index.
- Update the folder's `INDEX.md` **in the same edit** that adds or removes a file.
- Update `verified:` when you re-check a claim. Not when you merely re-read the file.
- Never delete a superseded decision — move it to the decision log with an outcome.

## The folder map of a workspace (example)

```
context/                       ← YOU ARE HERE. The standard. How context works.
product-a/context/             ← Knowledge true of all of product A. Feature-agnostic.
product-a/feature-x/context/   ← One feature of product A. Feature-specific.
```

Knowledge flows **downhill only**. `product-a/context` may cite `context`.
`feature-x/context` may cite both. Nothing ever cites downward — if feature X learns
something true of all of product A, that fact gets *promoted* to `product-a/context`
and the feature-x file links to it instead of restating it.

## What this folder is NOT

- Not a place for project facts, API keys, drafts, or anything about a specific project.
- Not a dumping ground for raw research. Raw research gets distilled into a topic file;
  the source URL goes in `sources:`, not the whole article.
- Not documentation for humans. `README.md` is the human door; everything else is
  written to be read by a model at 3am with no other context.
