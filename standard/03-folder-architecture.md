---
id: ctx/standard/folder-architecture
title: Folder architecture for a context folder
summary: Required files, category design, naming rules, and how to decide where a given fact belongs.
read_when: ["you are creating a new context folder", "you are adding a new category or file", "you are unsure which file a fact belongs in", "a folder feels disorganised"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-14
review_by: 2026-12-14
depends_on: [ctx/standard/principles, ctx/standard/file-format]
tags: [meta, architecture]
---

# Folder architecture

## The canonical shape

```
<project>/context/
  AGENTS.md            REQUIRED  Routing rules for this folder. Read first, always.
  INDEX.md             REQUIRED  Every file, with "read this when…" triggers.
  CHANGELOG.md         REQUIRED  Append-only. What changed, when, why.
  decisions.md         REQUIRED  Decision log. Exists from day one, even empty.
  open-questions.md    REQUIRED  Known unknowns and what would resolve each.
  <category>/          One folder per durable area of concern.
    00-index.md        Category index if the category has >4 files.
    <topic>.md         One subject each.
  reference/           OPTIONAL  Distilled external research, heavy detail, raw-ish.
  assets/              OPTIONAL  Non-markdown: schemas, examples, snippets, data.
```

Five required root files. That is the floor, not a suggestion — `decisions.md` and
`open-questions.md` created empty on day one are what stop a folder from becoming a pile
of facts with no memory of *why*.

## Choosing categories

A category is a **durable area of concern**, not a topic that happened to come up. The
test: *will this category still make sense in six months, and will new files land in it?*

Good categories are usually shaped by **who asks the question**:
- things you must obey → `policy/`
- things you are trying to achieve → `strategy/`
- things you build with → `pipeline/` or `stack/`
- things you learned by doing → `experiments/`
- things you decided → `decisions.md`

Bad categories: `misc/`, `notes/`, `research/` (research belongs distilled into a topic
file with its URL in `sources:`), anything named after a date or a session.

Aim for **3–7 categories**. Fewer and the index is useless; more and routing gets ambiguous.

## Where does this fact go?

Ask, in order:

1. **Does a file already own this subject?** Then it goes there. Not in a new file.
2. **Is it a fact about the world, or a choice we made?** Choices → `decisions.md`.
3. **Is it general or specific?** General → the parent context folder (see promotion, below).
4. **Is it a procedure?** → a playbook/recipe file, which references facts by `id` and
   states none of its own.
5. **Is it something we don't know?** → `open-questions.md`, with the resolution path.
6. **None of the above** → new topic file, and add it to `INDEX.md` in the same edit.

If a fact plausibly belongs in two files, it belongs in **one** and the other links to it.
Pick the file whose subject the fact is *about*, not the file that will read it most.

## Naming

- Files: lowercase, hyphenated, noun-phrase. `mid-roll-ads.md`, not `MidRollAds.md` or
  `how-mid-roll-ads-work-and-why-they-matter.md`.
- Prefix with `NN-` only where reading order genuinely matters (like `standard/` here).
- Categories: lowercase, singular or plural consistently, one word where possible.
- Never encode dates or versions in filenames. Those live in front matter.

## Size limits

| Unit | Target | Hard ceiling | What to do at the ceiling |
|---|---|---|---|
| Any single file | ≤ 300 lines | 400 lines | Split by sub-subject; both halves get index entries |
| `AGENTS.md` | ≤ 150 lines | 200 lines | Move detail into a topic file and link |
| `INDEX.md` | ≤ 120 lines | 200 lines | Introduce category indexes |
| Files per category | ≤ 8 | 12 | Split the category or add `00-index.md` |
| Total folder | ≤ 40 files | — | If you're past this, some of it is reference, not context |

These are not aesthetic. An agent that must read a 900-line file to find one number is
paying the full context-rot cost for a single fact.

## Promotion and demotion

**Promote** when a project-level file states something true of the whole category:
1. Move the claim (with its tags, sources and `verified:` date) to the parent folder's file.
2. Replace the original with a one-line link to the parent `id`.
3. Note the move in both `CHANGELOG.md` files.

**Demote** when a general rule turns out to hold only here: keep the exception local,
tagged, and linked upward to the general rule it deviates from — and say *why* it deviates.

Never copy. A promoted fact that is also left behind is two facts that will disagree.

## `AGENTS.md` nesting

The nearest `AGENTS.md` up the directory tree wins. A deeper one may **add** or **override**
rules from a shallower one, but must say explicitly which rule it is overriding and why.
Silent divergence between levels is how a standard stops being a standard.

The user's live instruction overrides every written rule, always.
