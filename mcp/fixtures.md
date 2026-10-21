---
id: ctx/mcp/fixtures
title: MCP server v1 — test fixtures
summary: The format of mcp/fixtures/*/expected.json, how a test runs a fixture, and which rule of the spec each fixture pins.
read_when:
  - "you are writing the tests of the MCP server"
  - "you are adding, changing or regenerating a fixture under mcp/fixtures/"
status: draft
confidence: medium
volatility: fast
verified: 2026-10-21
review_by: 2026-12-21
depends_on: [ctx/mcp/spec, ctx/mcp/audit-checks]
tags: [mcp, fixtures, testing]
---

# MCP server v1 — test fixtures

`mcp/fixtures/<name>/` holds one context folder at `context/` (sometimes with a nested
one) and `expected.json`:

```ts
{
  description: string;
  calls: Array<{
    tool: "ctx_manifest" | "ctx_get" | "ctx_audit";
    input: Record<string, unknown>;   // folder is relative to the fixture directory
    output?: object;                  // the exact output without root
    error?: string;                   // the expected error code instead
  }>;
}
```

A test resolves `input.folder` against the fixture directory and calls the tool. On
success it checks that `root` is that absolute path, removes `root`, and deep-compares
the rest with `output`. On failure it compares the error code. Every call passes `as_of`
explicitly, so no output depends on the date the tests run.

Fixture ids start with `fx/<fixture>/`. The upward walk from a fixture reaches this
repository's root, whose ids start with `ctx/`, so the two never collide.

| Fixture | What it pins |
|---|---|
| `valid` | A clean folder: zero findings; tagged numbers, masked numbers, tables, exempt `README.md` and `CLAUDE.md`, a superseded file left out of the index, a nested folder that is excluded, `used_by` that ignores dependents in the nested folder, an id resolved upward, `ctx_get` from both folders, and five error codes |
| `index-missing` | Item 1: no `INDEX.md`, so one `index-missing` finding and no `not-indexed` ones |
| `index-sync` | Item 1: a file not indexed, entries with no file in `INDEX.md` and in a `00-index.md`, and a `00-index.md` entry resolved relative to its own directory |
| `unresolved-id` | Item 2: unresolved `depends_on` and `supersedes` ids |
| `duplicate-id` | Item 2: two files with one id; `ctx_get` reports `ID_AMBIGUOUS`; a `depends_on` on the shared id counts as resolved |
| `front-matter` | Item 4: no front matter, front matter that is not YAML, empty, or a scalar; missing fields; invalid values, a list field holding a string, `read_when` out of range; and a `CLAUDE.md` with extra content that stays exempt |
| `stale` | Item 5: both verdicts, both boundaries, and the end-of-month clamp; a second manifest on 2026-10-01 tells the clamp (2026-09-30) from date overflow (2026-10-01) |
| `untagged-number` | Item 6: units, tags after the number, nested list items, blockquotes, fences |
| `size-ceiling` | Item 9: a topic file of 401 lines, a root `AGENTS.md` of 201 lines, and a category of 13 files |
| `used-by` | Item 11: an extra and a missing `used_by` id |

[DECISION, 2026-10-21] When the spec changes, change the affected `expected.json` files
in the same edit.
