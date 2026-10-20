---
id: ctx/changelog
title: Changelog — living-context standard
summary: Append-only record of changes to the standard and why they were made.
read_when: ["you need to know whether a rule changed recently", "you just changed a rule"]
status: stable
confidence: high
volatility: fast
verified: 2026-09-14
review_by: 2026-11-14
tags: [meta, changelog]
---

# CHANGELOG

Append at the top. Log reversals, corrections, deletions, promotions and audits.
Do not log typos.

## 2026-10-20
- **Moved** `standard/01`–`07` → `skills/context-standard/references/` and `templates/` →
  `skills/context-standard/templates/`, so the standard installs as an Agent Skill by
  copying one folder. File names and `id:` values are unchanged.
- **Added** `skills/context-standard/SKILL.md` — the short form of the rules and the
  router to every reference and template.
- **Removed** `standard/00-index.md` — its per-file summaries and minimum viable read now
  live in `SKILL.md`'s routing table, which would otherwise be a second routing table for
  the same files. The 01→07 reading order is gone; `SKILL.md` routes by task.
- **Moved** the audit (when to run it, the checklist, how to record it) from
  `references/05-maintenance-protocol.md` to `SKILL.md`; `05` links to it.
- **Changed** principle 7 in `01-principles.md`: a product folder now cites "the standard"
  instead of a `context/` folder, since the standard is no longer a workspace folder.
- **Rewrote** `README.md` around the skill: what it is, install on macOS/Linux and Windows
  from `https://github.com/jBronzatti/context-standard`, and usage examples.
- **Replaced** the 30-second version and the workspace map in `AGENTS.md` with a router to
  `SKILL.md`. `INDEX.md` now lists `SKILL.md` as the index of the skill subtree.
- **Changed** `templates/AGENTS.template.md` to cite the skill, with
  `<skill-dir>/SKILL.md` as the fallback for agents that do not load skills.
- **Decided** `SKILL.md` is exempt from the front-matter schema, like `README.md`: its front
  matter belongs to Claude Code, which ignores unknown fields.
- **Decided** `reference/` stays outside the skill: no rule links to it, and it serves
  people changing the standard, not agents applying it.
- **Replaced** the personal name in the `owner:` example of `02-file-format.md`.

## 2026-10-10
- **Replaced** the workspace-specific folder map in `AGENTS.md` and `INDEX.md`, and the
  path examples in `standard/01-principles.md` and `standard/02-file-format.md`, with a
  generic layout (`context/` → `product-a/context/` → `product-a/feature-x/context/`)
  for publication. `templates/AGENTS.template.md` now uses a `<workspace-root>` placeholder.
- **Replaced** project-specific examples in `standard/02-file-format.md` and
  `standard/05-maintenance-protocol.md` with neutral ones.
- **Redacted** workspace-specific names from the 2026-09-24 entry.

## 2026-09-24
- **Corrected** workspace paths in `AGENTS.md`, `INDEX.md` and `templates/AGENTS.template.md`.
- **Added** a downstream folder to the workspace map and index.

## 2026-09-14
- **Created** the standard. Seven rule documents, six templates, four reference files.
- **Adopted** the AGENTS.md nearest-file-wins precedence rule from the external convention.
- **Decided** not to use `llms.txt` locally — `AGENTS.md` + `INDEX.md` carry strictly more
  routing information for filesystem agents. Revisit if this context is ever published to
  a URL. See `reference/llms-txt-convention.md`.
- **Decided** the front-matter schema: `id`, `title`, `summary`, `read_when`, `status`,
  `confidence`, `volatility`, `verified`, `review_by`, `sources`, `depends_on`, `used_by`,
  `tags`. `volatility` drives `review_by` mechanically (12/6/2 months).
- **Decided** the claim-tag vocabulary: `[OFFICIAL]`, `[MEASURED]`, `[INFERENCE]`,
  `[CONTESTED]`, `[ASSUMPTION]`, `[DECISION]`, `[OPEN]`. Rationale: the dominant failure
  mode of an agent-maintained knowledge base is a guess hardening into a fact.
- **Open** — no linter yet for `depends_on`/`used_by`/`id` resolution. Write one if any
  folder passes ~30 files.
