---
id: ctx/reference/context-engineering
title: Context engineering — the evidence base
summary: Distilled findings from published work on context rot, progressive disclosure, compaction, note-taking and sub-agent architectures, with sources.
read_when: ["you want the evidence behind the standard's rules", "you are arguing for or against a structural change to how context works", "you are designing agent memory or retrieval"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-14
review_by: 2027-03-14
sources:
  - https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
  - https://www.mindstudio.ai/blog/progressive-disclosure-ai-agents-context-management
  - https://umesh-malik.com/blog/ai-agent-knowledge-base-architecture
  - https://ro14nd.de/what-goes-in-agents-md/
used_by: [ctx/standard/principles, ctx/standard/navigation]
tags: [research, context-engineering]
---

# Context engineering — evidence base

## Context rot

[OFFICIAL] Anthropic's engineering guidance states that model accuracy declines as token
count rises, attributing it to transformer architecture — n tokens create n² pairwise
relationships, stretching attention thin — compounded by training corpora that skew toward
shorter sequences.

**Operating principle stated there:** find *"the smallest possible set of high-signal
tokens that maximize the likelihood of some desired outcome."*

This is the load-bearing claim under the entire standard. It inverts the naive intuition
that more context is safer.

## System prompt / instruction calibration

[OFFICIAL] Two failure extremes are named: hardcoded brittle logic, and vague guidance
that assumes shared context. The target is the "Goldilocks zone" — specific enough to
guide, flexible enough to generalise. Organise with headers or XML tags into distinct
sections.

Applied here: `AGENTS.md` files state hard invariants and routing, not step-by-step
scripts and not platitudes.

## Just-in-time retrieval

[OFFICIAL] Rather than pre-loading data, agents should hold lightweight identifiers —
file paths, URLs, ids — and load at runtime. Explicitly compared to human cognition:
retrieval on demand rather than memorisation.

Applied here: cross-references are `id`s, not inlined copies.

## Progressive disclosure

[OFFICIAL] Agents assemble understanding layer by layer, using file hierarchies, naming
conventions and metadata as navigation signals, keeping only what is needed in working
memory.

[INFERENCE] Four concrete implementation patterns are described in practitioner writing:
index-first loading, the scout pattern (a cheap pass that returns a manifest), phase-based
loading, and separating "skill" files (pure process) from reference material. The stated
threshold for when it is worth it: when full task context would otherwise exceed roughly
5–10% of the window.

[INFERENCE] One practitioner account reports an **~80% token reduction** from loading only
the knowledge file each step needs rather than the whole base up front. Single source,
self-reported, but directionally consistent with the official guidance.

## Long-horizon techniques

[OFFICIAL] Three named techniques for tasks that outrun a context window:

- **Compaction** — summarise the conversation near the limit, preserving architectural
  decisions and unresolved issues, discarding redundant tool output. Tune for recall
  first, then precision.
- **Structured note-taking** — persistent external memory (a NOTES.md, a knowledge base)
  written during the task and pulled back in later. *This is what a living context is.*
- **Sub-agent architectures** — specialised sub-agents with clean context windows return
  condensed summaries (stated as roughly 1,000–2,000 tokens) to a coordinator, separating
  detailed exploration from high-level synthesis.

## Knowledge-file vs recipe separation

[INFERENCE] A practitioner pattern ("wiki plus recipes"): dense fact files with YAML
frontmatter declaring dependencies and consumers, plus composable procedure files that
reference facts by name and contain no domain facts of their own. The frontmatter turns
the base into a bidirectional dependency graph a linter can check for dangling references,
cycles and contradictions.

Reported benefit: facts can be updated without re-validating reasoning, and vice versa.
Reported rollout: zero regressions across improvement cycles over a six-week period at
one organisation. Single source — treat the mechanism as sound and the metrics as
unverified.

## Auto-generated context hurts

[MEASURED] Research cited in the AGENTS.md community found **LLM-generated context files
reduced success rates by 0.5–2%**. Hand-written files containing "the counterintuitive
choices, the hard-won invariants" outperformed generated ones. Recommended ceiling for an
AGENTS.md: **under 150 lines**.

Explicitly called out as empirically useless: codebase overviews, standard framework
conventions already in training data, README restatements, dependency lists, file-by-file
descriptions.

## RAG vs resident context

[INFERENCE] Sparse, situational material — historical records, one-off precedents, bulky
reference documents — should be retrieved on demand rather than living permanently in
context. Dense, frequently-needed facts should be resident.

Applied here: `reference/` folders hold the sparse material; topic files hold the dense.

## Open questions

- [OPEN] No published benchmark specifically measures living-context folder designs
  against each other. All structural choices here are reasoned from the principles above,
  not measured. Resolved by: running our own comparison, or a study appearing.
- [OPEN] Optimal file size is asserted (~300 lines) rather than measured. Resolved by:
  deliberate testing, if it ever matters enough.
