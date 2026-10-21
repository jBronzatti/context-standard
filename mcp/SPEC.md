---
id: ctx/mcp/spec
title: MCP server v1 — specification
summary: The exact inputs, outputs, computation rules and errors of the read-only MCP server's three tools (ctx_manifest, ctx_get, ctx_audit), the decisions behind them, and what waits for v1.1.
read_when:
  - "you are implementing or testing the MCP server"
  - "you are changing what a tool returns or how it computes freshness, ids or findings"
  - "you are adding or editing a fixture under mcp/fixtures/"
do_not_read_when:
  - "you are applying the standard by hand — see skills/context-standard/SKILL.md"
status: draft
confidence: medium
volatility: fast
verified: 2026-10-21
review_by: 2026-12-21
sources:
  - https://modelcontextprotocol.io/specification/2026-07-28/server/tools
  - https://modelcontextprotocol.io/specification/2026-07-28/schema
depends_on: [ctx/standard/file-format, ctx/standard/folder-architecture, ctx/standard/maintenance, ctx/reference/market-benchmark]
tags: [mcp, spec, tooling]
---

# MCP server v1 — specification

## Contents

- Decisions
- Deferred to v1.1
- Common rules
  - Folders and files
  - Reading a file
  - Front matter
  - Freshness
  - Id resolution and dependents
  - Index entries
  - Results and errors
- ctx_manifest
- ctx_get
- ctx_audit
- Fixtures
- Open questions

Every rule below is a [DECISION, 2026-10-21] unless tagged otherwise. Where this file and
the standard disagree, the standard wins and this file is a bug.

## Decisions

- [DECISION, 2026-10-21] A local MCP server over stdio, written in TypeScript. Not hosted,
  not multi-user.
- [DECISION, 2026-10-21] Read-only in v1: no tool writes, creates, moves or deletes a file.
  Changes are proposed by the model, or later returned as patches, and applied through the
  host's normal edit and review path. Resolves the `[OPEN]` "read-only or write tools" in
  `ctx/reference/market-benchmark`.
- [DECISION, 2026-10-21] Three tools: `ctx_manifest`, `ctx_get`, `ctx_audit`. Every tool
  declares the annotations `readOnlyHint: true` and `openWorldHint: false`.
- [DECISION, 2026-10-21] `ctx_audit` runs only the mechanical items of the audit checklist
  in `SKILL.md` (items 1, 2, 4, 5, 6, 9, 11). It returns the other four (3, 7, 8, 10) as
  text for the model to judge. Items are numbered in the order `SKILL.md` lists them.
- [DECISION, 2026-10-21] Same files, same inputs and same `as_of` give deep-equal output,
  with every array in the order this file specifies for it. Two inputs come from outside
  the files: `as_of`, which defaults to the server machine's local date, and the server's
  working directory, which picks the folder when `folder` is omitted.
- [DECISION, 2026-10-21] The Markdown files are the only source of truth. No cache, index,
  database or embedding survives between calls.

## Deferred to v1.1

| Item | What it will do |
|---|---|
| `ctx_mark_verified` | Return a patch that sets `verified` and recomputes `review_by` from `volatility` |
| `ctx_scaffold` | Return the five root files (and `CLAUDE.md`) from the templates as a patch |
| prompt `update-context` | Walk the model through the write-back table of `SKILL.md` |
| resource `ctx://<id>` | Expose a file by id for hosts that attach resources |

Not planned for any version: vector or graph memory, extraction of memories from
conversations, library documentation, server-side task-to-file matching, and loading
`AGENTS.md` or `CLAUDE.md` on the host's behalf. Reasons: `ctx/reference/market-benchmark`.

## Common rules

### Folders and files

- **Exact names.** Every name and path is compared as an exact, case-sensitive string
  against directory listings, never through a file-system lookup, so `agents.md` is not
  `AGENTS.md` and the result is the same on Windows, macOS and Linux.
- **Context folder.** A directory that contains a file named exactly `AGENTS.md`. Its tree
  is every descendant except the subtree of any deeper directory that has its own
  `AGENTS.md` (a *nested* context folder), the subtree of any directory whose name starts
  with `.`, and the subtree of any directory named `node_modules`. Symbolic links, to files
  or directories, are ignored.
- **Files of a folder.** Every regular file in its tree whose name ends in `.md`
  (case-sensitive). Other files are never read.
- **Paths.** Every path in an output is relative to the folder's root, uses `/`, and has
  no leading `./`. A path into an ancestor folder starts with `../`.
- **Sort order.** Paths, ids and strings are sorted by UTF-16 code unit, ascending, which
  is what JavaScript's default `sort()` does.
- **Root.** The absolute path of the folder as `path.resolve` gives it: symbolic links not
  resolved, no trailing separator.
- **Default folder.** When `folder` is omitted, the nearest directory at or above the
  server's working directory that contains `AGENTS.md`. The search uses the same stop rule
  as id resolution: it ends after the first directory that contains `.git`.
- **Exempt names.** `README.md` and `CLAUDE.md` are exempt from front matter, wherever
  they are in the tree (`ctx/standard/file-format`). `CLAUDE.local.md` is not: the
  standard does not name it.
- **Root files.** `AGENTS.md`, `INDEX.md` and `CHANGELOG.md` directly in the folder root.
  Files with those names deeper in the tree are ordinary files.
- **Category.** A direct subdirectory of the root that is in the tree.

### Reading a file

1. Decode as UTF-8; invalid bytes become U+FFFD. Drop a leading byte-order mark.
2. Replace every `\r\n`, then every remaining `\r`, with `\n`.
3. **Lines.** Split on `\n`. If the text ends with `\n`, the empty string after it is not
   a line. An empty file has zero lines. Line numbers start at one.

### Front matter

- A file *has front matter* if its first line is exactly `---` and a later line is exactly
  `---`. The lines between them are the front matter; everything after the closing line
  is the *body*. Without front matter, the whole text is the body.
- The front matter is parsed as YAML 1.2 with the core schema, so an unquoted
  `2026-10-21` is the string `"2026-10-21"`, never a date object.
- It is *valid* if it parses, has no duplicate keys, and its top level is a mapping. Empty
  front matter parses to `null`, so it is not valid. A file with valid front matter *is in
  the schema*.
- A field is *present* when its key exists, whatever its value: `tags:` with nothing after
  it is present, with the value `null`, and is invalid rather than missing.
- **Key line.** The line number of a front-matter key is the first front-matter line that
  starts with `<key>:`. A key that is not found that way has line `null`.
- **Required fields:** `id`, `title`, `summary`, `read_when`, `status`, `confidence`,
  `volatility`, `verified`, `review_by`, `tags`. Unknown fields are allowed.
- **Valid values:**

| Field | Valid when it is |
|---|---|
| `id` | a string matching `^[a-z0-9][a-z0-9._-]*(/[a-z0-9][a-z0-9._-]*)*$` |
| `title`, `summary` | a string that is not empty after trimming |
| `read_when` | a list of 2–5 strings, none empty after trimming; 1–5 in the root `AGENTS.md` and `INDEX.md` |
| `do_not_read_when`, `depends_on`, `supersedes`, `used_by`, `sources`, `tags` | a list of strings (may be empty) |
| `status` | one of `stable`, `draft`, `contested`, `superseded` |
| `confidence` | one of `high`, `medium`, `low` |
| `volatility` | one of `static`, `slow`, `fast` |
| `verified`, `review_by` | a string `YYYY-MM-DD` that names a real calendar date |

- A field's *usable value* is its value if valid, else nothing: tools never guess from an
  invalid value.
- Repeated ids in a list are kept: each occurrence is resolved and reported on its own.

### Freshness

Exactly the rule in `ctx/standard/maintenance` § Staleness:

| Verdict | When |
|---|---|
| `fresh` | `as_of` ≤ `review_by` |
| `unverified` | `review_by` < `as_of` ≤ `drift_date` |
| `drifted` | `as_of` > `drift_date` |
| `unknown` | `review_by` or `volatility` has no usable value |

- `drift_date` = `review_by` + the interval of `volatility`: `static` 12 months, `slow` 6,
  `fast` 2.
- Adding months keeps the day of the month; if that day does not exist in the target
  month, the month's last day is used. 2026-07-31 + 2 months = 2026-09-30, not 2026-10-01.
- Dates compare as strings; `YYYY-MM-DD` sorts chronologically.
- `verified` plays no part in the verdict.

### Id resolution and dependents

- The *ids of a folder*: the usable `id` of each of its files.
- **Resolving an id from folder F:** look in F's files; if no file there has it, look in
  the nearest ancestor context folder, then the next, and so on. The walk up stops after
  the first directory that contains a `.git` entry (file or directory), or at the
  filesystem root; if F itself contains `.git`, it has no ancestors. An ancestor context
  folder's tree excludes its nested context folders, F included.
- An id held by one file in the first folder that has it *resolves* to that file. Held by
  several files there, it is *ambiguous*: `ctx_get` reports `ID_AMBIGUOUS`, `ctx_audit`
  reports `duplicate-id`, and `depends_on` / `supersedes` treat it as resolved.
- **Dependents of a file X with id I:** the files in the same context folder as X whose
  usable `depends_on` contains I. Files in other context folders are never dependents
  (`ctx/standard/file-format`, `used_by`), even when `ctx_get` is called from one of them:
  a folder cannot see the folders below it, and listing them would cite downhill.

### Index entries

- *Index files:* the root `INDEX.md`, and every file named `00-index.md` in the tree.
- An *entry* is found on any body line of an index file outside a fenced code block or
  HTML comment (skipped as in `ctx/mcp/audit-checks` § Body scan, step 1). It is either:
  - the content of an inline code span (matched by ``` `[^`\n]*` ```) that matches
    ``^[^\s<>`]+\.md$``; or
  - a Markdown link destination, matched by `\]\(([^)\s]+)`, after cutting it at the
    first `#`, that ends in `.md` and holds no `:`.

  One line can hold several entries. Repeated entries are each checked.
- **Resolving an entry:** in `INDEX.md`, relative to the root. In `<dir>/00-index.md`,
  relative to `<dir>` if a file exists there, else relative to the root. `./` and `..`
  segments are normalised; an entry that leaves the root is ignored. An entry that lands in
  a nested context folder, or in a directory excluded from the tree, is ignored.
- A file is *indexed* if some entry resolves to it.
- [DECISION, 2026-10-21] A file whose usable `status` is `superseded` need not be indexed:
  `ctx/standard/maintenance` § Supersession drops it from the index tables, which
  contradicts the checklist's "every file appears in `INDEX.md`". This spec follows the
  supersession rule.

### Results and errors

- Success: `structuredContent` is the output object, and `content` holds one text block
  with the same object serialised as JSON. Each tool declares its `outputSchema`.
- Failure: `isError: true`, and `content` holds one text block with the JSON
  `{"code": "<CODE>", "message": "<human text>"}`. Tests compare `code` only.
- A file that breaks a rule is never an error. It is a finding in `ctx_audit`, and shows
  up as `null` or `unknown` values in `ctx_manifest` and `ctx_get`.

| Code | When |
|---|---|
| `INVALID_INPUT` | An input has the wrong type, `id` is missing or empty, or an unknown input is passed |
| `INVALID_AS_OF` | `as_of` is not a real calendar date in `YYYY-MM-DD` form |
| `FOLDER_NOT_FOUND` | `folder` does not exist or is not a directory |
| `NOT_A_CONTEXT_FOLDER` | `folder` has no `AGENTS.md`, or `folder` is omitted and no directory at or above the working directory has one |
| `ID_NOT_FOUND` | `ctx_get`: the id does not resolve from `folder` |
| `ID_AMBIGUOUS` | `ctx_get`: the id is held by several files in the first folder that has it |

`folder` may be absolute or relative to the server's working directory. Checks run in
the order of the table, so a call with a bad `as_of` and a missing folder reports
`INVALID_AS_OF`. The tool checks its own inputs and returns `INVALID_INPUT` as a tool
result; the input schema it declares is not left to the SDK to enforce, because an SDK
rejection would carry the SDK's message instead of a code.

## ctx_manifest

Everything a model needs to pick the 1–3 files for a task, without opening any.

**Input**

```ts
{
  folder?: string;   // default: see "Default folder"
  as_of?: string;    // YYYY-MM-DD; default: today, server local time
}
```

**Output**

```ts
type Freshness = "fresh" | "unverified" | "drifted" | "unknown";

{
  root: string;            // absolute path of the folder, native separators
  as_of: string;
  agents: "AGENTS.md";
  index: "INDEX.md" | null; // null when the root has no INDEX.md
  files: Array<{
    path: string;
    id: string | null;
    title: string | null;
    summary: string | null;
    read_when: string[];
    do_not_read_when: string[];
    status: string | null;
    confidence: string | null;
    volatility: string | null;
    verified: string | null;
    review_by: string | null;
    freshness: Freshness;
  }>;
}
```

- `files` lists every file of the folder except the root `AGENTS.md`, the root `INDEX.md`,
  and exempt names, sorted by `path`. Files without valid front matter are listed too.
- Each field holds its usable value, or `null` (`[]` for the two lists) when it has none.
  Strings, and the strings inside the two lists, are trimmed. So a manifest never shows a
  value that fails validation; a duplicate id is still shown.

## ctx_get

One file by id, with its links resolved.

**Input**

```ts
{
  id: string;
  folder?: string;
  as_of?: string;
}
```

**Output**

```ts
type Ref = { id: string | null; path: string | null };

{
  root: string;
  as_of: string;
  id: string;
  path: string;
  front_matter: Record<string, unknown>;  // the parsed mapping, as is
  body: string;                           // see "Front matter"
  freshness: Freshness;
  depends_on: Ref[];   // usable depends_on, in file order; path null if unresolved
  supersedes: Ref[];   // same, for supersedes
  dependents: Ref[];   // computed; sorted by path
}
```

- For `depends_on` and `supersedes`, `id` is the listed id, and `path` is the file it
  resolves to (the first by path, if ambiguous), or `null`.
- For `dependents`, `path` is always set, and `id` is the dependent's usable id or `null`.
- `body` is exact: the text after the closing `---` line's `\n`, after the normalisation in
  "Reading a file". It usually starts with `\n`.

## ctx_audit

**Input**

```ts
{
  folder?: string;
  as_of?: string;
}
```

**Output**

```ts
type Rule =
  | "index-missing" | "not-indexed" | "index-entry-missing"             // item 1
  | "unresolved-id" | "duplicate-id"                                   // item 2
  | "no-front-matter" | "invalid-front-matter"
  | "missing-field" | "invalid-field"                                  // item 4
  | "unverified" | "drifted"                                           // item 5
  | "untagged-number"                                                  // item 6
  | "size-ceiling"                                                     // item 9
  | "used-by-extra" | "used-by-missing";                               // item 11

{
  root: string;
  as_of: string;
  files_checked: number;   // all files of the folder, exempt names included
  findings: Array<{
    item: 1 | 2 | 4 | 5 | 6 | 9 | 11;
    rule: Rule;
    file: string;          // a path; for a category, the directory path ending in "/"
    line: number | null;
    detail: string;
  }>;
  manual: Array<{ item: 3 | 7 | 8 | 10; text: string }>;
}
```

- `findings` is sorted by `file`, then `line` (`null` first), then `rule`, then `detail`.
- `manual` is always these four, in this order, with this text:

| item | text |
|---|---|
| 3 | `No two files state the same number independently.` |
| 7 | `` `decisions.md` has no entry contradicted by a topic file. `` |
| 8 | `` `open-questions.md` has no entry that was silently answered elsewhere. `` |
| 10 | `` `AGENTS.md` still describes the folder that actually exists. `` |

The checks behind each rule, the exact `detail` strings and the body scan for untagged
numbers are in `ctx/mcp/audit-checks` (`mcp/audit-checks.md`).

## Fixtures

`mcp/fixtures/` holds the test data: each fixture is a context folder with an
`expected.json` of exact outputs. Its format, how a test uses it, and what each fixture
pins: `ctx/mcp/fixtures` (`mcp/fixtures.md`).

## Open questions

- [OPEN, 2026-10-21] Run on this repository's root, `ctx_audit` would report findings the
  repository accepts today: the skill's `references/` as not indexed; `SKILL.md` as
  missing ten fields (`AGENTS.md` exempts it, the standard does not); the `templates/` as
  having invalid or no front matter (placeholders such as `YYYY-MM-DD`,
  `CLAUDE.template.md`); and untagged numbers in this file's own rules. Resolved by:
  deciding whether the skill folder and `mcp/` follow the standard or are excluded from
  the root context folder, and writing that into the standard.
- [OPEN, 2026-10-21] `ctx/standard/maintenance` drops superseded files from the index, but
  the checklist says every file appears in `INDEX.md`. This spec follows the first (see
  "Index entries"). Resolved by: fixing the checklist wording in `SKILL.md`.
- [OPEN, 2026-10-21] Should the audit also check that `review_by` equals `verified` plus
  the interval, that the five root files exist, and that `sources` is present when a
  claim is `[OFFICIAL]`? All are mechanical, but none is a checklist item today.
  Resolved by: adding them to the checklist first, then to `ctx_audit`.
- [OPEN, 2026-10-21] Should numbers in tables be scanned? Resolved by: a real folder where
  an untagged figure in a table went stale unnoticed.
