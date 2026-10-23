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

## 2026-10-21
- [DECISION] **Aligned** the MCP spec with the decisions below it in this log, which
  resolve its four `[OPEN]` items (exclusion, superseded entries, extra checks, table
  scan); a dangling index entry under an excluded path is still reported:
  - `exclude`: excluded files are not files of the folder, so `ctx_manifest`, `ctx_audit`
    and `files_checked` leave them out, and an index entry that matches a pattern is
    ignored. Their ids still resolve, after the folder's files and before an ancestor's,
    and `ctx_get` can return one. `mcp/SPEC.md` § Folders and files defines the pattern
    matching.
  - Item 1: a superseded file must not be indexed. The new rule
    `index-entry-superseded` reports an entry that resolves to one.
  - Items 12 and 13 run in v1, as `review-by-mismatch` and `root-file-missing`. The
    `sources`-with-`[OFFICIAL]` rule and the table scan are deferred to v1.1.
  - The plugin question points to `ctx/open-questions`.
  - The `manual` texts moved to `mcp/audit-checks.md`, so `SPEC.md` stays under 400
    lines.
- **Updated** the fixtures. Every fixture folder except `index-missing` now has the five
  root files, so each reports only its planted defects; `index-missing` now also pins
  item 13. `valid` pins `exclude`, `index-sync` an indexed superseded file, and
  `front-matter` an invalid `exclude`. A new `review-by` fixture pins item 12. Every
  `expected.json` was regenerated, and `mcp/fixtures.md` lists eleven fixtures.
- [DECISION] **Added** `mcp/SPEC.md` (`ctx/mcp/spec`), `mcp/audit-checks.md`
  (`ctx/mcp/audit-checks`) and `mcp/fixtures.md` (`ctx/mcp/fixtures`): the specification of a local, read-only MCP server over stdio,
  in TypeScript, with three tools, `ctx_manifest`, `ctx_get` and `ctx_audit` (only the
  seven mechanical checklist items). `ctx_mark_verified`, `ctx_scaffold`, the
  `update-context` prompt and `ctx://` resources wait for v1.1. This resolves the
  read-only-or-write `[OPEN]` in `reference/market-benchmark.md`. The audit checks and
  the fixture guide live in their own files so that none passes the 400-line ceiling.
- [DECISION] **Chose**, for `ctx_audit`, that a superseded file need not be indexed:
  `05-maintenance-protocol.md` § Supersession drops it from the index tables, while the
  checklist says every file appears in `INDEX.md`. Recorded as `[OPEN]` in `mcp/SPEC.md`
  until the checklist wording is fixed.
- **Added** `mcp/fixtures/`: ten small context folders, one valid and at least one for
  each mechanical checklist item (item 1 has two, index sync and a missing `INDEX.md`;
  item 2 has two, unresolved and duplicate ids). Each has an
  `expected.json` with the exact outputs of `ctx_manifest`, `ctx_audit` and, where
  relevant, `ctx_get`, at a fixed `as_of`. No implementation code. Listed in `INDEX.md`,
  `AGENTS.md` and `README.md`.
- [DECISION] **Added** the optional `exclude:` field to `AGENTS.md` front matter
  (`02-file-format.md`, its schema block, `SKILL.md`): a list of globs, relative to the
  folder, naming files in its own tree that are not part of the context folder. They need
  no front matter or index entry and are not audited, but stay reachable: `INDEX.md` may
  route to them, and their ids resolve after the folder's own files and before any parent
  folder's. A nested context folder can never be excluded into reach, so excluding never
  lets a folder cite downhill. `02` defines the glob syntax. The context folder
  definition, in `02` and in the checklist's `used_by` item, now subtracts them.
- [DECISION] **Excluded** `skills/`, `mcp/` and `evals/` from this repository's root
  context folder through the new field (`decisions.md`, D-001, which records the cost:
  the skill's references are no longer checked by a root audit).
- [DECISION] **Reworded** checklist item 1 to "every file that is not superseded appears
  in `INDEX.md`, and vice versa; an entry may also point at an excluded file", matching
  `05-maintenance-protocol.md` § Supersession, which drops a superseded file from the
  index. Was "every file in the folder", which contradicted it. `03-folder-architecture.md`
  and the INDEX template's summary say the same now.
- [DECISION] **Added** two items to the end of the audit checklist (`SKILL.md`): every
  `review_by` equals `verified` plus its volatility's interval, and the five root files
  exist. Appended, so items 1–11 keep their numbers. `reference/market-benchmark.md`'s
  count of mechanical items gets a dated note.
- **Added** `decisions.md` and `open-questions.md` at this repository's root, which
  lacked two of its five root files, and listed them in `INDEX.md` and `README.md`. The
  first open question is whether to ship a plugin bundling the skill and the MCP server,
  deferred until the server exists.
- **Audited** the root context folder's Markdown files (12 after exclusion) against the
  mechanical checklist items 1, 2, 4, 5, 6, 9, 11, 12 and 13, as of 2026-10-21, with a
  scratch model of the planned MCP audit. Found four numbers in `reference/` with no claim
  tag (item 6). Three now carry a dated tag; the fourth, the ~80% figure in
  `knowledge-base-patterns.md`, duplicated the one in `context-engineering-research.md`
  (item 3) and became a link to it. No tag was upgraded. Nothing else was found. Items
  3, 7, 8 and 10 were not reviewed beyond that.
- [DECISION] **Fixed** the date of every evaluation with a new `as_of` field (2026-10-21);
  the runner tells the session to assume that date (`evals/README.md`). Was: fixture
  dates that expire and had to be moved forward by hand.
- [DECISION] **Defined** `drifted` exactly (`05-maintenance-protocol.md` § Staleness,
  `SKILL.md`): today > `review_by` + one review interval of the file's current
  `volatility`. Was ">2x past `review_by`", which did not say twice what. Every file now
  has one verdict, `fresh`, `unverified` or `drifted`, and § Staleness fixes the date
  arithmetic (calendar days; a missing day of the month clamps to its last day) so tools
  agree. `04-navigation-protocol.md` and `01-principles.md` §8 now say a drifted file is
  verified before use, and the audit checklist asks for files past `review_by` to be
  listed as unverified or drifted.
- [DECISION] **Added** `templates/CLAUDE.template.md`, a one-line `CLAUDE.md` containing
  `@AGENTS.md`, as a root file required when Claude Code works in the folder
  (`03-folder-architecture.md`, `SKILL.md`). By default Claude Code skips `AGENTS.md` when
  a `CLAUDE.md`, `.claude/CLAUDE.md` or `CLAUDE.local.md` exists in or above its working
  directory, and the setting that changes this is ignored in a project's own settings
  files. An existing `CLAUDE.md` gets the line added instead. The INDEX template lists it.
- [DECISION] **Exempted** every `CLAUDE.md` from front matter, like `README.md`
  (`02-file-format.md`, `SKILL.md`): it belongs to Claude Code, which reads it as
  instructions, including when it already had content before the import was added. It is
  still listed in `INDEX.md`.
- **Added** a root `CLAUDE.md` with `@AGENTS.md` to this repository, which follows its own
  rule, and listed it in `INDEX.md`.
- [DECISION] **Added** a `## Contents` list at the top of every reference longer than 100
  lines: `01`, `02`, `03`, `05` and `06` in the skill, and
  `reference/context-engineering-research.md` and `reference/market-benchmark.md`, per the
  Agent Skills best practices. `AGENTS.md` states this as a rule for this repository.
- [DECISION] **Added** `evals/`: the four manual test sessions of 2026-10-20 (pull request
  #1) as versioned evaluations, three that must trigger the skill and one that must not,
  with fixtures. Format: Anthropic's Agent Skills best practices, plus `should_trigger`
  from the agentskills.io trigger-query format. Kept outside the skill so installing it
  does not copy fixtures. Listed in `INDEX.md`, `AGENTS.md` and `README.md`; `AGENTS.md`
  warns that fixture `AGENTS.md` files govern nothing.
- **Marked** recommendations 2, 3 and 7 of `reference/market-benchmark.md` as adopted, and
  its `drifted` assumption as resolved.
- **Added** `reference/market-benchmark.md` (`ctx/reference/market-benchmark`) — the
  standard compared with 16 tools: Agent Skills, AGENTS.md, Claude Code memory, Cursor,
  Copilot, llms.txt, Cline Memory Bank, Kiro, Spec Kit, five MCP servers (official memory,
  basic-memory, mem0/OpenMemory, Graphiti, Context7), Letta memory blocks and the
  Anthropic memory tool; plus the tools a proposed MCP server should and should not
  offer. Listed in `INDEX.md`. No rule changed; its recommendations are not adopted yet.
- **Corrected** `reference/agents-md-convention.md`: agents.md no longer states "60+
  tools"; it now cites Linux Foundation stewardship and "over 60k open-source projects".
  Added that Claude Code, by default, skips `AGENTS.md` when a `CLAUDE.md`,
  `.claude/CLAUDE.md` or `CLAUDE.local.md` exists. `verified:` is not bumped, because the
  ro14nd claims were not re-checked.
- **Changed** `volatility` of `reference/agents-md-convention.md` from `slow` to `fast`
  (`review_by` 2027-03-14 → 2026-11-14): it now holds tool-loading behaviour that changes
  between versions.
- **Widened** the `reference/` line in `AGENTS.md` to cover comparisons with other tools
  and building tooling on the standard.

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
