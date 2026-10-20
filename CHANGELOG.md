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
- [DECISION] **Broadened** `[MEASURED]` to "measured with method and sample described — by
  a third party or by us" (`SKILL.md`, `02-file-format.md`, `07-research-provenance.md`).
  Two test sessions disagreed on whether an API limit found through 429 errors could be
  `[MEASURED]`. What separates a measurement from an impression is the recorded method,
  not who measured. The experiment-log template already used it this way.
- [DECISION] **Lowered** the minimum of `read_when` to one for `AGENTS.md` and `INDEX.md`
  (range 1–5; other files, category `00-index.md` included, keep 2–5), because navigation
  reads both on every task. The field stays required.
  The rule contradicted the AGENTS and INDEX templates, which ship one trigger.
- [DECISION] **Made** `depends_on` the source of truth. `used_by` is now optional; if
  present it must list exactly the ids of the files in the same context folder (the tree
  governed by one `AGENTS.md`, categories included, excluding deeper folders with their own
  `AGENTS.md`) whose `depends_on` names the file. The audit checks
  this. A hand-kept mirror drifted on the first day of testing, and listing dependents in
  other context folders would cite downhill. The example in the `02` schema had done
  exactly that, and now uses an id from the same folder.
- **Removed** `used_by` from `01-principles.md`, `02-file-format.md` and
  `reference/context-engineering-research.md`. All three were out of sync with the
  `depends_on` that point at them. Also removed from the topic-file template.
- **Changed** the `[OPEN]` linter item in `reference/knowledge-base-patterns.md`: it now
  checks `used_by` against `depends_on` only where `used_by` is present, instead of
  requiring a `used_by` for every `depends_on`.
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
