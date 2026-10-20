---
id: ctx/index
title: Index of the living-context standard
summary: Every document in this repository, what it covers, and when to read it.
read_when: ["you need to find a rule about how context files work", "you are choosing which standard document to open"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-20
review_by: 2027-04-20
tags: [meta, index]
---

# INDEX — living-context standard

Read `AGENTS.md` first. This index tells you which file answers which question.

## Entry points

| File | Read it when |
|---|---|
| `AGENTS.md` | Always, first, before anything else in this folder. |
| `README.md` | You are a human and want the tour or the install steps. Agents can skip it. |
| `CHANGELOG.md` | You need to know whether a rule changed recently, or you just changed one. |

## skills/context-standard/ — the skill

| File | Read it when |
|---|---|
| `skills/context-standard/SKILL.md` | Any task about the standard. It is the short form of the rules, the audit checklist, and the index of this subtree: its last table routes you to the one file in `references/` you need, and its "Creating a context folder" section maps each file to its template. |

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
