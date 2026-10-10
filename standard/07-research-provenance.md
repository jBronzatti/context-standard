---
id: ctx/standard/provenance
title: Research provenance and evidence grading
summary: How to record external research honestly — source quality tiers, the grading procedure, and how to handle SEO-farmed and vendor-biased material.
read_when: ["you are recording findings from web research", "you are citing a source", "you need to decide how much to trust a figure", "sources disagree"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-14
review_by: 2026-12-14
depends_on: [ctx/standard/file-format]
tags: [meta, research, sources]
---

# Research provenance

## Source quality tiers

| Tier | What | Default tag | Notes |
|---|---|---|---|
| **A** | The authority itself: platform help docs, official blogs, vendor pricing pages, API reference, primary legal text | `[OFFICIAL]` | Still date it — official pages change silently |
| **B** | Named study with disclosed methodology and sample size; a company's own published data | `[MEASURED]` | Record N and method, not just the conclusion |
| **C** | Practitioner reporting with specifics: named creators, screenshots, dashboards, reproducible detail | `[INFERENCE]` | Better than it sounds; often the only source |
| **D** | Aggregators, "best X of 2026" listicles, tool-vendor blogs, SEO content farms | `[INFERENCE]`, low confidence | See the warning below |
| **E** | Uncited numbers, AI-generated summaries of unknown provenance, forum hearsay | Do not record as fact | Record as `[OPEN]` if the question matters |

## The tier-D warning

A large share of content on commercially valuable queries — pricing, RPM, "best tools",
platform rules — is **SEO content produced by vendors who profit from the conclusion**.
These pages cite nothing, agree with each other because they copied each other, and are
the single largest source of confident wrong numbers in an agent-built knowledge base.

Detection:
- No methodology, no sample, no date on any figure.
- The site sells a product in the category it is ranking.
- The number appears identically on six sites with no original source.
- The range is suspiciously round and suspiciously wide ($8–18).
- Two pages on the same site contradict each other.

Handling: record it as `[INFERENCE]`, `confidence: low`, name the bias explicitly
("vendor has an incentive to overstate"), and prefer any tier-A/B figure that disagrees
even if it is less convenient.

## Recording procedure

For every finding worth keeping:

1. **Claim** — one sentence, specific, with units.
2. **Tag** — from the vocabulary in `02-file-format.md`.
3. **Date** — when the source said it, not when you read it, if they differ.
4. **Source** — full URL in `sources:`; inline if per-claim attribution matters.
5. **Method** — for `[MEASURED]`, the sample and how it was gathered.
6. **Bias note** — for tier C/D, who benefits from this being believed.

What you do **not** record: the article's prose, its framing, its recommendations that
don't apply to us, or six sources that all say the same tier-D thing.

## Reconciling disagreement

1. Rank the sources by tier. A beats B beats C beats D.
2. Check whether they are measuring the same thing (same market, same period, same
   definition). Most "disagreements" are scope mismatches — say so and keep both.
3. If genuinely contradictory at the same tier: `[CONTESTED]`, record both, state which
   you weight higher and the reason.
4. Sanity-check arithmetic. A back-computed figure that lands on exactly the same round
   range across many different categories is a hardcoded constant, not a measurement —
   discard the derived number, keep the raw inputs.

## Derived figures

When you compute something (cost per video, break-even views, revenue per hour of
production), show the inputs and the arithmetic in the file. A derived number with hidden
inputs cannot be updated when one input changes — it can only be distrusted.

Tag derived figures `[INFERENCE]` and list which `[ASSUMPTION]`s they rest on.

## Re-verification

`volatility: fast` sources (pricing, platform policy, model versions) need the URL stored
so a future agent can re-check in one fetch. Store the **deep link** to the specific page,
not the vendor's homepage. If a page is behind a paywall or login, say so — otherwise the
next agent wastes a turn discovering it.
