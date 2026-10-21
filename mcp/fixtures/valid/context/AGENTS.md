---
id: fx/valid/agents
title: How to navigate the valid fixture
summary: Entry point for the valid fixture.
read_when: ["you are an agent operating in this folder"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-01
review_by: 2027-04-01
exclude: ["vendor/**", "*.draft.md"]
tags: [meta, routing, entrypoint]
---

# AGENTS.md — valid fixture

A test fixture for the MCP server. It is clean on purpose; do not change it. `vendor/` and
`*.draft.md` files are excluded: they are not part of this folder.

## Reading protocol

1. This file.
2. `INDEX.md`.
3. The topic files whose `read_when` matches your task.
