---
id: <folder>/agents
title: How to navigate the <project> context
summary: Entry point and routing rules for agents working on <project>.
read_when: ["you are an agent operating in this folder"]
status: stable
confidence: high
volatility: slow
verified: YYYY-MM-DD
review_by: YYYY-MM-DD
tags: [meta, routing, entrypoint]
---

# AGENTS.md — <project>

<One paragraph: what this project is, in terms an agent with no other context can act on.>

## Governed by

This folder follows the living-context standard at `<workspace-root>/context/`.
Read `<workspace-root>/context/standard/02-file-format.md` before writing any file here.
<State any rule this folder overrides, and why. If none: "No overrides.">

## Reading protocol

1. This file.
2. `INDEX.md`.
3. The 1–3 topic files whose `read_when` matches your task.
4. `decisions.md` if you are about to make or revisit a choice.

Do not load the whole folder.

## Non-negotiables

<The 3-8 rules that must never be violated on this project. Hard constraints only —
things that cause real damage if broken. Not preferences.>

## Current state

<Where the project actually is right now, in 3-5 lines. Update this every session that
changes it. This is the most-read paragraph in the folder.>

## Where things live

| Category | What's in it |
|---|---|
| `<category>/` | <scope> |

## Upstream context

<Which parent context folders apply, and what they cover so an agent knows when to go up.>
