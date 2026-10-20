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
used_by: [ctx/standard/maintenance, ctx/standard/provenance]
tags: [meta, format, schema]
---

# File format

Every `.md` file in a context folder — including `INDEX.md` and `AGENTS.md` — opens with
YAML front matter. No exceptions. `README.md` is exempt because it is for humans.

## Front-matter schema

```yaml
---
id: product-a/policy/monetization    # REQUIRED. Stable, unique, path-like, lowercase.
title: YouTube monetization policy   # REQUIRED. Human-readable.
summary: >                           # REQUIRED. One sentence. What this file answers.
  What YPP requires in 2026 and what disqualifies a channel.
read_when:                           # REQUIRED. 2-5 concrete task triggers.
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
depends_on: [product-a/policy/ai-disclosure]   # OPTIONAL. ids this file relies on.
used_by: [feature-x/pipeline/publishing]       # OPTIONAL. ids that rely on this file.
supersedes: []                                 # OPTIONAL. ids this replaces.
owner: alice                                   # OPTIONAL. Who decides when this is contested.
tags: [youtube, policy, monetization]          # REQUIRED. Lowercase, reusable.
---
```

### Field notes

**`id`** — the permanent handle. Cross-references use it, never file paths, so files can
move without breaking links. Format: `<folder>/<category>/<subject>`.

**`read_when`** — the routing table. Write it as *tasks an agent might be doing*, not as
topics. "you are choosing a video length" routes correctly; "video length" does not.

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
| `[MEASURED]` | Third-party study with disclosed methodology and sample | a published analysis of N channels |
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
- A number without a tag and a date is not usable. Write `[MEASURED, 2026-09] 340 ms p95`.

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
