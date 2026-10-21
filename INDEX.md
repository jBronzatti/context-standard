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
| `CLAUDE.md` | Never; it only imports `AGENTS.md` for Claude Code. |
| `README.md` | You are a human and want the tour or the install steps. Agents can skip it. |
| `CHANGELOG.md` | You need to know whether a rule changed recently, or you just changed one. |
| `decisions.md` | You are about to change how this repository is organised or tooled, or need to know why it is set up this way. |
| `open-questions.md` | You are deciding how to package or distribute the skill, or how to run the evaluations automatically. |

`skills/`, `mcp/` and `evals/` are excluded from this context folder (`AGENTS.md`,
`exclude:`). The rows below that point into them are for navigation only.

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
| `reference/market-benchmark.md` | You are arguing that a rule is redundant with an existing tool, deciding whether to copy a feature from another context or memory tool, weighing an MCP feature against other tools (the adopted design is `mcp/SPEC.md`), or writing positioning text. |

## evals/ — tests of the skill

| File | Read it when |
|---|---|
| `evals/README.md` | You are running or adding an evaluation of the skill. It lists the four evaluations, their format, and the defects planted in the fixtures under `evals/files/`. |

## mcp/ — the MCP server specification

| File | Read it when |
|---|---|
| `mcp/SPEC.md` | You are implementing or testing the MCP server: decisions, what waits for v1.1, the common rules (files, front matter, freshness, ids, index entries, errors), and each tool's input and output. |
| `mcp/fixtures.md` | You are writing the server's tests, or adding or changing a fixture: the `expected.json` format, how a test runs it, and what each of the ten fixtures pins. |
| `mcp/audit-checks.md` | You are implementing `ctx_audit` or explaining one of its findings: what each rule checks, its exact `detail`, and the scan for untagged numbers. |

## Downstream folders governed by this standard (example)

| Folder | Scope |
|---|---|
| `product-a/context/` | Knowledge true of all of product A: platform rules, policy, shared stack. Feature-agnostic. |
| `product-a/feature-x/context/` | One feature of product A: feature-specific strategy, pipeline, experiments. |
