---
id: ctx/index
title: Index of the living-context standard
summary: Every file in context/, what it covers, and when to read it.
read_when: ["you need to find a rule about how context files work", "you are choosing which standard document to open"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-24
review_by: 2026-12-14
tags: [meta, index]
---

# INDEX — context/

Read `AGENTS.md` first. This index tells you which file answers which question.

## Entry points

| File | Read it when |
|---|---|
| `AGENTS.md` | Always, first, before anything else in this folder. |
| `README.md` | You are a human and want the tour. Agents can skip it. |
| `CHANGELOG.md` | You need to know whether a rule changed recently, or you just changed one. |

## standard/ — the rules

| File | Read it when |
|---|---|
| `standard/00-index.md` | You want the rules map with a bit more detail than this table. |
| `standard/01-principles.md` | You are deciding *whether* something belongs in context at all, or you want the reasoning behind the rules. Covers context rot, high-signal tokens, just-in-time retrieval, downhill-only knowledge flow. |
| `standard/02-file-format.md` | You are writing or editing any context file. Defines the front-matter schema and the claim-tag vocabulary. **The most-used file here.** |
| `standard/03-folder-architecture.md` | You are creating a new context folder, adding a new category, or unsure where a file belongs. |
| `standard/04-navigation-protocol.md` | You are an agent about to load context, or you are designing how an agent should traverse a folder. Covers index-first loading, the scout pattern, phase-scoped loading, sub-agent delegation. |
| `standard/05-maintenance-protocol.md` | You are updating a file, or a `review_by` date has passed, or two files contradict each other. Covers staleness, verification, supersession, contradiction resolution. |
| `standard/06-anti-patterns.md` | Before your first substantial write, and any time a context folder feels bloated or useless. The failure catalogue. |
| `standard/07-research-provenance.md` | You are recording external research, citing a source, or grading a claim's reliability. |

## templates/ — copy these

| File | Use for |
|---|---|
| `templates/AGENTS.template.md` | The router file at the root of any new context folder. |
| `templates/INDEX.template.md` | The index of any context folder. |
| `templates/topic-file.template.md` | Any ordinary knowledge file. |
| `templates/decision-record.template.md` | One entry in a `decisions.md` log. |
| `templates/experiment-log.template.md` | One entry in an `experiments.md` log — hypothesis, setup, result, verdict. |
| `templates/CHANGELOG.template.md` | The changelog of any context folder. |

## reference/ — why the rules are what they are

| File | Read it when |
|---|---|
| `reference/context-engineering-research.md` | You want the evidence base: context rot, progressive disclosure, compaction, structured note-taking, sub-agent architectures. |
| `reference/agents-md-convention.md` | You are integrating with external tooling, or want the AGENTS.md convention as others define it. |
| `reference/llms-txt-convention.md` | You are exposing context to external agents or publishing docs for machine consumption. |
| `reference/knowledge-base-patterns.md` | You are designing a larger structure: wiki-plus-recipes separation, dependency graphs, RAG-vs-context boundaries. |

## Downstream folders governed by this standard (example)

| Folder | Scope |
|---|---|
| `product-a/context/` | Knowledge true of all of product A: platform rules, policy, shared stack. Feature-agnostic. |
| `product-a/feature-x/context/` | One feature of product A: feature-specific strategy, pipeline, experiments. |
