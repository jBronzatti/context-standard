---
id: ctx/mcp/audit-checks
title: MCP server v1 — the checks behind ctx_audit
summary: For each mechanical audit-checklist item, which files ctx_audit checks, when it reports a finding, and the exact line and detail it reports; plus the body scan that finds untagged numbers.
read_when:
  - "you are implementing or testing ctx_audit"
  - "you need to know why ctx_audit reported a finding, or did not"
  - "you are changing a detail string or the untagged-number scan"
do_not_read_when:
  - "you need ctx_audit's input and output types — see ctx/mcp/spec"
status: draft
confidence: medium
volatility: fast
verified: 2026-10-21
review_by: 2026-12-21
depends_on: [ctx/mcp/spec, ctx/standard/file-format, ctx/standard/maintenance]
tags: [mcp, spec, audit]
---

# MCP server v1 — the checks behind ctx_audit

Terms such as *files of a folder*, *usable value*, *key line*, *exempt names* and *index
entry* are defined in `ctx/mcp/spec` § Common rules. Every rule here is a
[DECISION, 2026-10-21].

## Contents

- Mechanical checks
- Body scan for untagged numbers

## Mechanical checks

In each `detail`, `{x}` is replaced by a value and everything else is literal. `{found}`
is the YAML value serialised with `JSON.stringify`. `{entry}` is the entry as written in
the index file, before normalisation. `file` is a path; for `index-missing` it is
`INDEX.md`, and for a category it is the directory path ending in `/`.

| Rule | Checked on | Finding when | `line` | `detail` |
|---|---|---|---|---|
| `index-missing` | the folder | the root has no `INDEX.md`; then `not-indexed` is skipped | `null` | `INDEX.md does not exist` |
| `not-indexed` | every file except the root `INDEX.md` and files whose usable `status` is `superseded` | it is not indexed | `null` | `not listed in INDEX.md or any 00-index.md` |
| `index-entry-missing` | every entry of every index file | it resolves to no file | the entry's line | `lists {entry}, which does not exist` |
| `unresolved-id` | every id in a usable `depends_on` or `supersedes` | it does not resolve from this folder | the key line | `{field} names {id}, which no file has` |
| `duplicate-id` | every file with a usable `id` | another file of the folder has the same id | the key line of `id` | `id {id} is also used by {paths}` (the others, sorted, joined by `, `) |
| `no-front-matter` | every non-exempt file | it has no front matter | `null` | `no front matter` |
| `invalid-front-matter` | every non-exempt file | its front matter is not valid | `1` | `front matter is not a YAML mapping` |
| `missing-field` | every file in the schema | a required field is absent | `null` | `missing required field {field}` |
| `invalid-field` | every file in the schema | a present field has an invalid value | the key line | `{field} must be {rule}; found {found}` |
| `unverified` | every file in the schema | its verdict is `unverified` | the key line of `review_by` | `review_by {review_by} has passed; drifted after {drift_date}` |
| `drifted` | every file in the schema | its verdict is `drifted` | the key line of `review_by` | `review_by {review_by} plus {n} months ({volatility}); drifted after {drift_date}` |
| `untagged-number` | every file except the root `AGENTS.md`, `INDEX.md`, `CHANGELOG.md`, any `00-index.md` and exempt names | a unit holds a number with no claim tag before it | the line of that number | `number {number} has no claim tag before it` |
| `size-ceiling` | every file | it has more lines than its ceiling: root `AGENTS.md` and root `INDEX.md` 200, any other file 400 | `null` | `{lines} lines; ceiling {ceiling}` |
| `size-ceiling` | every category | its tree holds more than 12 files | `null` | `{count} files; ceiling 12` |
| `used-by-extra` | every id in a usable `used_by` | it is not the id of a dependent | the key line of `used_by` | `used_by lists {id}, which does not depend on this file` |
| `used-by-missing` | every dependent with a usable id, when `used_by` is usable | `used_by` does not list it | the key line of `used_by` | `used_by omits {id}, which depends on this file` |

`{rule}` in `invalid-field`:

| Field | `{rule}` |
|---|---|
| `id` | `a lowercase path-like id` |
| `title`, `summary` | `a non-empty string` |
| `read_when` | `a list of 2-5 non-empty strings` (`1-5` in the root `AGENTS.md` and `INDEX.md`) |
| `status` | `one of stable, draft, contested, superseded` |
| `confidence` | `one of high, medium, low` |
| `volatility` | `one of static, slow, fast` |
| `verified`, `review_by` | `a date in YYYY-MM-DD form` |
| the six list fields | `a list of strings` |

One file can produce several findings. A file with no usable `id` is never reported by
`duplicate-id` or `used-by-missing`.

[DECISION, 2026-10-21] The number scan skips the root `AGENTS.md`, `INDEX.md`,
`CHANGELOG.md` and every `00-index.md`. They route and log rather than state facts, and
their templates carry numbers that are rules ("the 1–3 topic files") or counts of changes.

## Body scan for untagged numbers

[DECISION, 2026-10-21] The checklist says "no untagged numeric claims". A tool cannot know
what a claim is, so it approximates: in each *unit* (a paragraph or one list item),
every number must come after a claim tag in the same unit. This misses a claim whose
tag belongs to another number, and flags a number that is not a claim. Tables are not
scanned in v1.

1. **Skip** the front matter. In the body, skip every line from one whose text, after
   leading spaces, starts with three backticks or three tildes, through the next line that
   starts, after leading spaces, with the same three characters (a fence that never closes
   skips to the end). Skip every line from one containing `<!--` through the first line,
   at or after it, that contains `-->` after that `<!--` (a comment that never closes
   skips to the end). A skipped line ends the current unit.
2. **Blockquotes.** Repeatedly strip leading whitespace followed by `>` and one optional
   space, before the next step.
3. **Classify each remaining line** by the first rule that matches, in this order:
   - blank: ends the current unit;
   - heading (`^ {0,3}#{1,6}( |$)`), table row (`^\s*\|`), or thematic break
     (`^ {0,3}([-*_])( *\1){2,} *$`): ends the current unit and is not scanned;
   - list item (`^\s*([-*+]|\d+[.)])\s+`): ends the current unit and starts a new one with
     the text after the marker;
   - anything else: continues the current unit, or starts one if none is open.
4. **Mask**, in this order, replacing each match with spaces of the same length:
   inline code spans (`` `[^`\n]*` ``); link destinations (`\]\([^)\s]*\)`); URLs
   (`[a-z][a-z0-9+.-]*://\S+`); dates (`(?<!\d)\d{4}-\d{2}(-\d{2})?(?!\d)`).
5. **Tags:** `\[(OFFICIAL|MEASURED|INFERENCE|CONTESTED|ASSUMPTION|DECISION|OPEN)(,[^\]\n]*)?\]`.
   Record where each starts, then mask it.
6. **Numbers:** `(?<![A-Za-z0-9_./#§-])\d+([.,]\d+)*`. A number is *untagged* if no tag
   starts before it in the same unit (text of the unit's lines joined by `\n`).
7. One finding per unit, for its first untagged number.

Examples, all in one unit each:

| Text | Finding |
|---|---|
| `[MEASURED, 2026-09] 340 ms p95 — 10k requests` | none |
| `Responses are cached for 300 seconds.` | `300` |
| `Up to 5 retries. [INFERENCE] Usually 2.` | `5` |
| `See ctx/standard/principles §3, decision D-001, v2.1, PR #1.` | none |
| `Verified on 2026-10-21 against https://example.com/v1/limits.` | none |
| `` Set `timeout_ms: 300`. `` | none |
