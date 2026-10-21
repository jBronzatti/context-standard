---
id: ctx/decisions
title: Decisions — living-context standard repository
summary: Choices about how this repository is organised and tooled, with the alternatives rejected.
read_when: ["you are about to change how this repository is organised or tooled", "you need to know why the repository is set up the way it is"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-21
review_by: 2027-04-21
tags: [meta, decisions]
---

# Decisions

Entries follow `skills/context-standard/templates/decision-record.template.md`, newest at
the top. Changes to the standard's rules are logged in `CHANGELOG.md`, not here.

## D-001 — Exclude `skills/`, `mcp/` and `evals/` from the root context folder

- **Date:** 2026-10-21
- **Status:** active
- **Decided by:** the repository owner
- **Scope:** the root context folder of this repository. Not the context folders inside
  the fixtures, which keep their own rules.

**Context.** These folders are not context files about this repository. `skills/` is the
product: an installable skill whose `SKILL.md` follows Claude Code's schema, whose
templates hold placeholder front matter, and whose `references/` route through
`SKILL.md`'s own table. `evals/` holds evaluations and their fixtures; `mcp/` holds the
server's spec and fixtures, most of them defective on purpose.

**Decision.** [DECISION] The root `AGENTS.md` lists `skills/**`, `mcp/**` and `evals/**`
in `exclude:`. `INDEX.md` keeps routing to them, and their ids still resolve.

**Alternatives rejected.**
| Option | Why not |
|---|---|
| Bring the skill folder into line with the standard | `SKILL.md` must keep Claude Code's front matter, and the templates must keep their placeholders |
| Exclude only `SKILL.md` and `skills/**/templates/**` | `references/` would join the root folder and need rows in the root `INDEX.md`, duplicating `SKILL.md`'s routing table, which must stay inside the skill |
| Accept the findings on every audit | Findings that are always accepted hide the ones that matter |

**Cost.** [DECISION] An audit of the root no longer checks the skill's `references/`, so
their `review_by` dates go unflagged there. They must be re-verified by hand when they
pass: all seven on 2026-12-14.

**Rests on.** `ctx/standard/file-format` (the `exclude` field).

**Revisit when.** A file under one of these folders starts holding facts an agent needs
for work in this repository, or a reference passes `review_by` unnoticed.

**Outcome.**
