---
id: ctx/standard/file-format
title: Context file format — front matter and claim tags
summary: The mandatory YAML schema for every context file and the vocabulary for tagging claims by evidence quality.
read_when: ["you are creating any context file", "you are editing any context file", "you need the meaning of a front-matter field or a claim tag"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-14
review_by: 2026-12-14
depends_on: [ctx/standard/principles]
tags: [meta, format, schema]
---

# File format

## Contents

- Front-matter schema
  - Field notes
- Claim tags
- Body structure
- Style

Every `.md` file in a context folder — including `INDEX.md` and `AGENTS.md` — opens with
YAML front matter. Two exceptions: `README.md`, because it is for humans, and `CLAUDE.md`,
because it belongs to Claude Code, which reads it as instructions (see
`ctx/standard/folder-architecture`). Both are still listed in `INDEX.md`.

## Front-matter schema

```yaml
---
id: product-a/policy/monetization    # REQUIRED. Stable, unique, path-like, lowercase.
title: YouTube monetization policy   # REQUIRED. Human-readable.
summary: >                           # REQUIRED. One sentence. What this file answers.
  What YPP requires in 2026 and what disqualifies a channel.
read_when:                           # REQUIRED. 2-5 task triggers (1-5 in AGENTS.md / INDEX.md).
  - "you are about to publish a video"
  - "you are assessing demonetization risk"
do_not_read_when:                    # OPTIONAL. Prevents wrong loads.
  - "you only need ranking or packaging advice"
status: stable                       # REQUIRED. stable | draft | contested | superseded
confidence: high                     # REQUIRED. high | medium | low
volatility: fast                     # REQUIRED. static | slow | fast
verified: 2026-09-14                 # REQUIRED. Last date a human/agent re-checked it.
review_by: 2026-11-14                # REQUIRED. Derived from volatility (see below).
sources:                             # REQUIRED if any claim came from outside.
  - https://support.google.com/youtube/answer/1311392
depends_on: [product-a/policy/ai-disclosure]   # OPTIONAL. ids this file relies on. Source of truth.
used_by: [product-a/pipeline/publishing]       # OPTIONAL. Derived from depends_on; see below.
supersedes: []                                 # OPTIONAL. ids this replaces.
owner: alice                                   # OPTIONAL. Who decides when this is contested.
exclude: ["fixtures/**"]                       # OPTIONAL. AGENTS.md only. Files outside the folder.
tags: [youtube, policy, monetization]          # REQUIRED. Lowercase, reusable.
---
```

### Field notes

**`id`** — the permanent handle. Cross-references use it, never file paths, so files can
move without breaking links. Format: `<folder>/<category>/<subject>`.

**`read_when`** — the routing table. Write it as *tasks an agent might be doing*, not as
topics. "you are choosing a video length" routes correctly; "video length" does not.
Exception: `AGENTS.md` and `INDEX.md` need only one trigger (up to five). The navigation
algorithm reads both on every task, so their `read_when` routes nothing; the field stays
required so every file has the same schema. Category `00-index.md` files are not read on
every task and keep the 2–5 range.

**`depends_on` / `used_by`** — `depends_on` is the source of truth: the ids this file
relies on. `used_by` is its mirror and is optional. If present, it lists **exactly** the
ids of the files in the same context folder whose `depends_on` names this id — no more,
no fewer. A *context folder* is the tree governed by one `AGENTS.md`, its categories
included, less the files its `exclude` names; a deeper folder with its own `AGENTS.md` is
a separate context folder. Dependents in other context folders are never listed: naming a
downstream file would cite downhill, and an audit run inside this folder cannot see them.
A hand-kept mirror drifts, so prefer leaving `used_by` out.

**`exclude`** — optional, and read only from a folder's `AGENTS.md`: a list of glob
patterns, relative to that folder, naming files that sit in its tree but are not part of
the context folder, such as tooling, test fixtures, or files that follow another tool's
schema. Exclude what is not context; never exclude a file to silence an audit finding.

- An excluded file needs no front matter and no index entry, and is not audited.
- It can still be reached. `INDEX.md` may point at it. Its `id`, if valid, resolves after
  the folder's own files and before any parent folder's, so `depends_on` may name it.
- Only files of the folder's own tree can be excluded. A deeper folder with its own
  `AGENTS.md` is a separate context folder whatever the patterns say, so excluding never
  lets a folder cite downhill. `AGENTS.md` itself is never excluded.
- Patterns match file paths, not directories: `fixtures/**`, not `fixtures`. Segments are
  separated by `/`, and none is empty, `.` or `..`, so a pattern never starts or ends
  with `/`. `*` matches any run of characters inside one segment, a leading `.` included;
  `?` matches one character inside one segment; a segment that is exactly `**` matches
  any number of whole segments, zero included. Every other character matches itself, case
  included.

Example: `exclude: ["fixtures/**", "*.draft.md"]`.

**`volatility` → `review_by`** — set the review interval mechanically:

| volatility | meaning | review interval |
|---|---|---|
| `static` | conceptual, unlikely to change | 12 months |
| `slow` | changes with the field, not the month | 6 months |
| `fast` | platform rules, prices, tool versions | 2 months |

**`status`**
- `stable` — believed correct and verified.
- `draft` — being written; do not build on it yet.
- `contested` — sources disagree; the file must say how and why. Never resolve a
  contested file by picking a side silently.
- `superseded` — kept for history. Must name its replacement in `supersedes`/body.

**`confidence`** is about the *content*; `status` is about the *file*. A `stable` file can
be `confidence: low` if the underlying evidence is genuinely thin — say so rather than
pretending.

## Claim tags

Inline, at the start of the sentence or bullet that makes the claim.

| Tag | Means | Example source |
|---|---|---|
| `[OFFICIAL]` | Stated by the authority itself | YouTube Help Center, a vendor's own pricing page |
| `[MEASURED]` | Measured with method and sample described — by a third party or by us | a published analysis of N channels; our own log of 1,200 requests |
| `[INFERENCE]` | Reasoned conclusion, community consensus, no authority behind it | "creators report…" |
| `[CONTESTED]` | Credible sources directly disagree — record both sides | two RPM figures 3x apart |
| `[ASSUMPTION]` | We chose to assume this to move forward; untested | "assume $6 RPM for modelling" |
| `[DECISION]` | We decided this. Not a fact about the world. | "we release weekly, not daily" |
| `[OPEN]` | Known unknown, deliberately recorded | "unknown whether X affects Y" |

Rules:
- Never upgrade a tag without new evidence. Promoting `[INFERENCE]` to `[OFFICIAL]`
  because it has been in the file a while is the single worst thing you can do here.
- `[CONTESTED]` claims must state **both** figures and **why** you weight one higher.
- `[ASSUMPTION]` claims must carry the condition that would falsify them.
- Our own observation is `[MEASURED]` only when the claim records method, sample and date:
  `[MEASURED, 2026-10] 429s start at 100 req/min — 3 runs × 500 requests, client log
  query rate-limit-probe`.
  A few errors noticed in passing are `[INFERENCE]`.
- A number without a tag and a date is not usable. Write
  `[MEASURED, 2026-09] 340 ms p95 — 10k requests over 24 h, APM`.

## Body structure

```markdown
# Title

## TL;DR
Three to six bullets. The answer, if the reader reads nothing else.

## <substantive sections>
Tagged claims. Tables for anything with more than three comparable items.

## Open questions
`[OPEN]` items, each with what would resolve it.

## Sources
Only if not fully captured in front matter, or when per-claim attribution matters.
```

## Style

- Tables over prose for comparisons. Prose over tables for reasoning.
- Absolute dates (`2026-09-14`), never "recently", "currently", "last month".
- Name the thing: "PostgreSQL 16", not "the database".
- No hedging that carries no information ("it depends", "results may vary"). If it
  depends, say *on what*.
- Write for an agent with no memory of this conversation and no access to the user.
