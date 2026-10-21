---
id: ctx/open-questions
title: Open questions — living-context standard repository
summary: Known unknowns about how this repository packages, tests and distributes the standard, each with what would resolve it.
read_when: ["you are deciding how to package or distribute the skill", "you are choosing how to run the evaluations automatically"]
status: stable
confidence: medium
volatility: fast
verified: 2026-10-21
review_by: 2026-12-21
tags: [meta, open-questions]
---

# Open questions

Each entry says what would resolve it. When one is answered, record the answer in the
file that owns it, log it in `CHANGELOG.md`, and mark the entry `Resolved <date>:` with a
pointer, as `ctx/reference/market-benchmark` does.

- [OPEN, 2026-10-21] Should the standard also ship as a Claude Code plugin that bundles
  `skills/context-standard/` and the read-only MCP server? [DECISION, 2026-10-21]
  Deferred until the MCP server v1 exists. [ASSUMPTION, 2026-10-21] A plugin would let
  Claude Code's plugin evaluation command run the trigger and no-trigger tests in
  `evals/` in CI, which a bare skill folder cannot; recalled from an earlier session, not
  checked against Claude Code's documentation. Falsified if that documentation shows the
  command missing or running on a bare skill folder. Resolved by: checking that, then
  packaging a trial plugin once the server works and running `evals/` through it.
