---
id: product-a/agents
title: How to navigate the product-a context
summary: Entry point and routing rules for agents working on product-a.
read_when: ["you are an agent operating in this folder"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-01
review_by: 2027-04-01
tags: [meta, routing, entrypoint]
---

# AGENTS.md — product-a

product-a is a web client for the Acme payments API. It sends payment requests on behalf
of shop owners and shows them the results.

## Governed by

This folder follows the living-context standard (the `context-standard` skill). If the
skill is not loaded, read `<skill-dir>/SKILL.md` first — `<skill-dir>` is the installed
`context-standard` folder, e.g. `~/.claude/skills/context-standard`.
No overrides.

## Reading protocol

1. This file.
2. `INDEX.md`.
3. The 1–3 topic files whose `read_when` matches your task.
4. `decisions.md` if you are about to make or revisit a choice.

Do not load the whole folder.

## Non-negotiables

- Never retry a payment request that the Acme API rejected as invalid; only rate-limit
  errors may be retried.
- Never store a card number; the Acme API returns a token for that.
- Never log a shop owner's API key.

## Current state

Client in private beta with a handful of shops.

## Where things live

| Category | What's in it |
|---|---|
| `stack/` | The external services product-a calls, and their limits |

## Upstream context

None. product-a has no parent context folder.
