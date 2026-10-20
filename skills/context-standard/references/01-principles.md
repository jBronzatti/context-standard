---
id: ctx/standard/principles
title: Principles of a living context
summary: The nine principles that the rest of the standard is derived from, and the evidence behind them.
read_when: ["you are making a judgement call the rules don't cover", "you are deciding whether something belongs in context at all", "you are designing a new context folder from scratch"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-14
review_by: 2026-12-14
sources:
  - https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
  - https://agents.md/
  - https://umesh-malik.com/blog/ai-agent-knowledge-base-architecture
  - https://www.mindstudio.ai/blog/progressive-disclosure-ai-agents-context-management
  - https://ro14nd.de/what-goes-in-agents-md/
depends_on: []
used_by: [ctx/standard/file-format, ctx/standard/folder-architecture, ctx/standard/navigation]
tags: [meta, principles]
---

# Principles

## 1. Context is a finite, degrading resource — not a bucket

[OFFICIAL] Model accuracy declines as token count rises. The mechanism is architectural:
n tokens produce n² pairwise attention relationships, and training corpora skew toward
short sequences, so models have proportionally less experience with very long ones. This
is called **context rot**.

The consequence is counterintuitive and it is the foundation of everything here:
**adding more relevant information to a prompt can make the output worse.** A context
folder is therefore not judged by how much it contains but by how little an agent has to
read to act correctly.

The operating target: *the smallest possible set of high-signal tokens that maximises the
likelihood of the desired outcome.*

## 2. Write only what an agent cannot discover on its own

[MEASURED] Auto-generated context files measurably **hurt** agent performance — one study
cited by the AGENTS.md community found success rates dropping 0.5–2% when LLM-generated
overviews were added. Restating what the model already knows, or what it can read directly
from the artefacts in front of it, is not neutral. It is negative.

So: no framework tutorials, no summaries of public documentation the agent can fetch, no
file-by-file codebase descriptions. What belongs is **the counterintuitive choices, the
hard-won invariants, the things that cost someone a week to learn.**

Test before writing any paragraph: *would a competent agent with web access get this
right without me?* If yes, delete it and leave a link.

## 3. Index first, load late (progressive disclosure)

An agent should discover context incrementally: routing file → index → the one or two
topic files that match the task. Reported token reductions of ~80% come from exactly this
— loading the knowledge file a step needs rather than the knowledge base up front.

This only works if every file **advertises itself accurately**. The `read_when` field is
not decoration; it is the routing table. A vague `read_when` makes the file invisible or,
worse, makes it get loaded every time.

## 4. One subject per file, and facts live in exactly one place

Duplication is how a living context dies. Two copies of a number drift; the agent that
reads the stale one has no way to know. When a fact is needed in two places, one file owns
it and the other links to it by `id`.

Corollary: files are small (≤ ~300 lines). A file that has grown past ~400 lines is
usually two subjects that were never separated.

## 5. Separate facts from procedures

[INFERENCE] Two file types, deliberately distinct:

- **Knowledge files** — positions, definitions, thresholds, constraints, numbers.
- **Recipes / playbooks** — ordered procedures that *reference* knowledge files by id and
  contain no domain facts of their own.

This lets a number change without re-validating a workflow, and a workflow be rewritten
without re-checking every number. Mixing them means every edit invalidates everything.

## 6. Every claim carries its epistemic status

The single most damaging failure mode in an agent-maintained knowledge base is a guess
that hardens into a fact across sessions. The tag vocabulary in `02-file-format.md`
(`[OFFICIAL]`, `[MEASURED]`, `[INFERENCE]`, `[CONTESTED]`, `[ASSUMPTION]`, `[DECISION]`,
`[OPEN]`) exists so that a future agent can tell what it is allowed to rely on.

An untagged assertion in a context file should be read as `[INFERENCE]` at best.

## 7. Knowledge flows downhill and gets promoted upward

General knowledge lives in the general folder; specific knowledge in the specific one.
`feature-x/context` may cite `product-a/context` may cite the standard. Never the reverse.

When a project discovers something true of the whole category, **promote it**: move the
fact up a level and replace the original with a link. When a general rule turns out to
have a project-specific exception, the exception stays local and links up.

## 8. Freshness is a property of the file, not of the folder

Different facts rot at different rates. A YouTube monetization threshold is `volatility:
fast`; the epistemology of evidence grading is `volatility: static`. Each file carries its
own `verified:` date and `review_by:` date, so maintenance can be targeted rather than a
doomed quarterly re-read of everything.

A file past its `review_by` is not wrong — it is **unverified**, and an agent should say so
rather than either trusting it blindly or discarding it.

## 9. The folder must be cheaper to update than to ignore

If recording a finding takes ten minutes, nobody records findings. Templates, a fixed
schema, and a short append-only changelog exist to make the correct action the fast one.
Any rule in this standard that makes updating expensive is a bug in the standard.

---

## What follows from these

- Front matter is mandatory → (1), (3), (8)
- Files are small and single-subject → (1), (4)
- `INDEX.md` updated in the same edit as the file → (3), (9)
- Claim tags are mandatory → (6)
- No duplicated facts, only `id` links → (4), (7)
- `decisions.md` and `open-questions.md` exist from day one → (6), (9)
