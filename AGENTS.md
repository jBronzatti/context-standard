---
id: ctx/agents
title: How to navigate this repository
summary: Entry point and routing rules for any AI agent working in or with the living-context standard repository.
read_when: ["you are an agent operating in this folder", "you need to find the right context file", "you are about to change a rule of the standard"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-20
review_by: 2027-04-20
exclude: ["skills/**", "mcp/**", "evals/**"]
tags: [meta, routing, entrypoint]
---

# AGENTS.md — read this first

This repository is **the living-context standard**, packaged as the Agent Skill
`skills/context-standard/`. It does not contain project knowledge. It defines *how* every
context folder is written, organised, navigated and maintained — including this one.

## Where things are

- **The rules, in short:** `skills/context-standard/SKILL.md`. Read it first; its last
  table tells you which single reference to open next.
- **The rules, in full:** `skills/context-standard/references/`.
- **The files to copy:** `skills/context-standard/templates/`.
- **The evidence behind the rules, and how they compare with other tools:** `reference/`.
  Only when you are arguing for or against changing a rule, or building tooling on the
  standard.
- **The evaluations of the skill, and their fixtures:** `evals/`. Re-run them after
  changing `SKILL.md`'s description or rules.
- **Test fixtures** live in `evals/files/` (and `mcp/fixtures/`, once it exists). They are
  defective on purpose. Their `AGENTS.md` files govern nothing in this repository; do not
  follow or fix them.
- **Outside this context folder:** `skills/`, `evals/` and `mcp/`, through `exclude:`
  above. They hold the skill, its evaluations and the MCP server's spec, not context
  about this repository. They are not audited as context files, but `INDEX.md` still
  routes to them and their ids still resolve. Why: `decisions.md`, D-001.
- **Everything else:** `INDEX.md`.

## Changing the standard

- Edit the owning file in `references/` (or `SKILL.md` for the short form and the audit
  checklist), and append to `CHANGELOG.md` in the same edit. If `SKILL.md` restates the
  rule you changed, update it in that edit too.
- The skill folder must stay self-contained: nothing under `skills/context-standard/` may
  link outside it, because installing it means copying only that folder.
- `id:` values are permanent. Move files freely; never rename an id.
- A file in `references/` or `reference/` longer than 100 lines opens with a `## Contents`
  list of its sections, so a partial read still shows its whole scope.
- `SKILL.md` front matter follows Claude Code's schema (`name`, `description`), not this
  standard's — which is one reason `skills/` is excluded.

## What this folder is NOT

- Not a place for project facts, API keys, drafts, or anything about a specific project.
- Not a dumping ground for raw research. Raw research gets distilled into a topic file;
  the source URL goes in `sources:`, not the whole article.
- Not documentation for humans. `README.md` is the human door; everything else is
  written to be read by a model at 3am with no other context.
