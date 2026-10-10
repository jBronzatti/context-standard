---
id: ctx/standard/index
title: The living-context standard — rules map
summary: What each of the seven rule documents covers and the order to read them in.
read_when: ["you want an overview of the standard", "you are unsure which rule document answers your question"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-14
review_by: 2026-12-14
tags: [meta, index]
---

# The standard, in reading order

| # | File | One line |
|---|---|---|
| 01 | `01-principles.md` | Why a context folder is shaped this way. The reasoning you need to make judgement calls the rules don't cover. |
| 02 | `02-file-format.md` | The front-matter schema and the claim-tag vocabulary. Mechanical. Read every time you write. |
| 03 | `03-folder-architecture.md` | Folder layout, required files, naming, where a given fact belongs. |
| 04 | `04-navigation-protocol.md` | How an agent loads context without drowning in it. |
| 05 | `05-maintenance-protocol.md` | Keeping it true over time: verification, staleness, supersession, contradictions. |
| 06 | `06-anti-patterns.md` | The ways this goes wrong. Read once in full. |
| 07 | `07-research-provenance.md` | Sources, evidence grading, and how to record research honestly. |

**Minimum viable read** for an agent that just needs to write one file:
`02-file-format.md` + the relevant template. Everything else is for when you are
designing, restructuring, or arbitrating.
