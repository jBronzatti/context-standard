---
id: ctx/reference/market-benchmark
title: Market benchmark — context-standard against current tools
summary: How the standard compares, on 2026-10-21, with file conventions, memory banks and MCP memory servers on routing, freshness, provenance, maintenance, tokens, validation, portability and adoption — and what that means for a proposed MCP server built on it.
read_when:
  - "you are designing a proposed MCP server that operates on context folders"
  - "you are deciding whether to copy a feature from another context or memory tool"
  - "you are writing positioning or comparison text for the README"
  - "you are arguing that a rule of the standard is redundant with an existing tool"
do_not_read_when:
  - "you only need the AGENTS.md or llms.txt format itself — see ctx/reference/agents-md or ctx/reference/llms-txt"
status: stable
confidence: medium
volatility: fast
verified: 2026-10-21
review_by: 2026-12-21
sources:
  - https://agentskills.io/specification
  - https://agentskills.io/
  - https://github.com/agentskills/agentskills/tree/main/skills-ref
  - https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices
  - https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
  - https://code.claude.com/docs/en/memory
  - https://cursor.com/docs/context/rules
  - https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions
  - https://agents.md/
  - https://docs.cline.bot/prompting/cline-memory-bank
  - https://kiro.dev/docs/steering/
  - https://kiro.dev/docs/specs/
  - https://github.com/github/spec-kit
  - https://github.com/github/spec-kit/blob/main/spec-driven.md
  - https://github.github.io/spec-kit/integrations/index.html
  - https://github.com/modelcontextprotocol/servers/tree/main/src/memory
  - https://github.com/basicmachines-co/basic-memory
  - https://github.com/upstash/context7
  - https://github.com/mem0ai/mem0
  - https://docs.mem0.ai/core-concepts/how-it-works
  - https://docs.mem0.ai/platform/features/memory-decay
  - https://github.com/getzep/graphiti
  - https://github.com/getzep/graphiti/blob/main/mcp_server/README.md
  - https://docs.letta.com/guides/agents/memory-blocks
  - https://platform.claude.com/docs/en/agents-and-tools/tool-use/memory-tool
  - https://docs.github.com/en/rest/repos/repos#get-a-repository
depends_on: [ctx/standard/principles, ctx/standard/maintenance, ctx/reference/agents-md, ctx/reference/llms-txt, ctx/reference/kb-patterns]
tags: [research, benchmark, mcp, positioning]
---

# Market benchmark — context-standard against current tools

## Contents

- TL;DR
- Comparison table
- What only context-standard does
- Where it is behind, and what to copy
- Implications for the MCP
- Positioning sentence for the README
- Assumptions made
- Open questions

All sources were read on 2026-10-21. "Not documented" means not on the pages listed in
`sources:`, not proven absent. AGENTS.md and llms.txt facts live in `ctx/reference/agents-md`
and `ctx/reference/llms-txt` and are not restated here. [OFFICIAL, 2026-10-21] Star
counts are `stargazers_count` from `GET /repos/{owner}/{repo}` on the GitHub REST API,
read at 2026-10-21 13:42 UTC.

## TL;DR

- [INFERENCE, 2026-10-21] None of the 16 tools in the table records, in plain files, both
  *when a file was last verified* and *how each claim is known*. The nearest pieces live
  elsewhere: Graphiti has fact validity windows and episode provenance inside a graph
  database; Spec Kit has one marker, `[NEEDS CLARIFICATION]`; Claude Code auto memory
  writes a `modified` timestamp, which records the last write, not the last check.
- [INFERENCE, 2026-10-21] The standard lacks three things several tools ship: automatic
  loading by file path (Cursor `globs`, Copilot `applyTo`, Claude Code `paths`, Kiro
  `fileMatch`), a validator (`skills-ref validate`, basic-memory `schema_validate`, Spec
  Kit `analyze`), and an explicit write-back trigger (Cline "update memory bank").
- [INFERENCE, 2026-10-21] By default Claude Code may never read a context folder's
  `AGENTS.md` (rule in `ctx/reference/agents-md`). The folder is then reached only if the
  skill fires or a `CLAUDE.md` imports it.
- [INFERENCE, 2026-10-21] A proposed MCP server on this standard should be the
  deterministic layer for freshness and evidence metadata over plain files: manifest, id
  resolution, freshness verdicts and the audit. It should not be another vector store, an
  LLM extraction pipeline, or a docs fetcher; mem0, Graphiti and Context7 already do those.
- [OFFICIAL, 2026-10-21] Adoption: this repo has 0 GitHub stars. Spec Kit has 142,261,
  mem0 67,577, Context7 63,419 and AGENTS.md 25,029.
- [INFERENCE, 2026-10-21] So every differentiator below is unproven in use.

## Comparison table

How to read the cells: a plain cell describes a documented mechanism and is
`[OFFICIAL, 2026-10-21]`, from that tool's own docs. A cell that starts with *judgement:*
is `[INFERENCE, 2026-10-21]`. Stars measure attention, not usage.

| Tool | Routing / discovery | Freshness | Provenance | Maintenance | Token efficiency | Automatic validation | Portability | Adoption |
|---|---|---|---|---|---|---|---|---|
| **context-standard** | `AGENTS.md` → `INDEX.md` → 1–3 files matched on `read_when`, with `do_not_read_when` as a negative trigger; `depends_on` one hop | Per file: `verified`, plus `review_by` derived from `volatility` (12/6/2 months); "past review_by = unverified, not wrong" | 7 claim tags (`[OFFICIAL]` … `[OPEN]`), dated numbers, source tiers A–E | Write-back table each session; CHANGELOG; supersede, never delete; audit checklist | *judgement:* good — two small files always, then ≤3; size ceilings | None; the linter has been `[OPEN]` since 2026-09-14 | Plain Markdown; front matter is ignored by other tools; installs as an Agent Skill | 0 stars, 0 forks (repo created 2026-10-10) |
| **Agent Skills** (spec + Anthropic best practices) | Only `name` + `description` preloaded ("~100 tokens"); body when the task matches; references "one level deep" | "Avoid time-sensitive information"; an "Old patterns" section | None | Evaluations first: "at least three evaluations" | Body "under 500 lines" / "< 5000 tokens"; table of contents for references over 100 lines | `skills-ref validate` (frontmatter and naming; "demonstration purposes only") | Open standard, "originally developed by Anthropic" | `anthropics/skills` 182,645 stars; `agentskills/agentskills` 26,336 |
| **AGENTS.md** | Nearest file wins; no fields | None | None | None | *judgement:* depends on the author | None | See `ctx/reference/agents-md` | See `ctx/reference/agents-md`; `agentsmd/agents.md` 25,029 stars |
| **Claude Code CLAUDE.md + rules + auto memory** | All levels concatenated; subdirectory files load on demand; `.claude/rules/` with `paths` globs; `@imports` up to 4 hops | Auto memory writes `modified`; nothing records verification | None | Auto memory: the agent writes `MEMORY.md` and topic files, with 4 `type`s | "target under 200 lines"; first 200 lines / 25 KB of `MEMORY.md` at start | `/doctor prompt-audit` (v2.1.283+): an LLM review for stale instructions, missing files and contradictions; edits only on request | Claude Code only; AGENTS.md handling in `ctx/reference/agents-md` | Not measured |
| **Cursor rules** | `.mdc` with `alwaysApply` / `globs` / `description` / manual; Team → Project → User | None | None | "update them when the agent errs" | "under 500 lines" | None | Cursor; also reads AGENTS.md, nested | Not measured |
| **GitHub Copilot instructions** | `copilot-instructions.md` + `*.instructions.md` with `applyTo`, `excludeAgent`; nearest AGENTS.md | None | None | None | None stated | None | Copilot; also reads AGENTS.md, CLAUDE.md, GEMINI.md | Not measured |
| **llms.txt** | H1 + blockquote + curated link lists, for websites | None | None | None | *judgement:* good for web fetch | None | Web only | `AnswerDotAI/llms-txt` 2,665 stars |
| **Cline Memory Bank** | None: "I MUST read ALL memory bank files at the start of EVERY task" | None | None; decisions sit in `activeContext.md` / `systemPatterns.md` | "update memory bank" command reviews all files | *judgement:* poor — everything loads every task | None | "works with any AI that can read docs" | `cline/cline` 70,701 stars (the tool, not the methodology) |
| **Kiro steering + specs** | Front matter `inclusion: always / fileMatch / manual / auto` (auto uses `description`); `#[[file:…]]` refs; specs requirements → design → tasks | None in content | None; "Explain why decisions were made" | "Treat steering changes like code changes - require reviews"; tasks are marked done | `always` is the default | Unresolved-reference marker; "Analyze Requirements" | Kiro IDE/CLI/Web; reads AGENTS.md | Not measured |
| **GitHub Spec Kit** | Commands read fixed artifacts (`constitution.md`, `specs/<branch>/…`) | None; "Specifications stay in sync with code because they generate it" | `[NEEDS CLARIFICATION: …]`, "Don't guess"; constitution amendments need a rationale | Command-driven; `/speckit-converge` until `Converged` | *judgement:* per-phase loading | `analyze`, checklists, `/speckit-converge` | 41 named agents on its integrations page | 142,261 stars |
| **MCP memory server** (official) | `search_nodes`, `open_nodes`, `read_graph` (the whole graph) | None | None | Agent CRUD on entities, relations, observations | *judgement:* `read_graph` dumps everything | Name-collision and missing-entity checks | Any MCP client; JSONL file | `modelcontextprotocol/servers` 91,445 stars (monorepo) |
| **basic-memory** | `search_notes`, `build_context` over `memory://`, `recent_activity`; wikilink relations | Not documented per note | Not documented | Humans and AI edit the same Markdown; sync; `doctor` | *judgement:* search-scoped | `schema_infer` / `schema_validate` / `schema_diff`, `doctor` | Any MCP client; Markdown + SQLite; AGPL-3.0 | 4,302 stars |
| **mem0 / OpenMemory** | Vector + entity search | Expiration (OSS and Platform); decay is a "soft ranking bias" (Platform, opt-in) | Only for Dream-synthesized memories | LLM extraction on `add()`; since April 2026 "ADD-only"; Dream supersedes and merges | *judgement:* returns snippets only | None | API, hosted MCP, CLI | 67,577 stars |
| **Graphiti / Zep** | Hybrid search over a temporal graph; `search_memory_facts` with `valid_at` / `invalid_at` | "Facts have validity windows… invalidated — not deleted" | "Every entity and relationship traces back to the episodes" | LLM extraction from episodes | *judgement:* returns facts only | None; MCP has "no authentication… no prompt-injection protection" | MCP; Neo4j/FalkorDB/Neptune | 32,108 stars |
| **Context7** | `resolve-library-id` → `query-docs` | "up-to-date, version-specific" (crawler is private) | Disclaims accuracy: "cannot guarantee the accuracy" | Community-contributed; Report button | *judgement:* query-scoped | None documented | Remote MCP + CLI | 63,419 stars |
| **Letta memory blocks** | Blocks are "always visible - no retrieval needed" | None | None | Agent tools; a write "completely replaces" the block | Character `limit` per block | None | Letta API | `letta-ai/letta` 25,216 stars |
| **Anthropic memory tool** (API) | "ALWAYS VIEW YOUR MEMORY DIRECTORY BEFORE DOING ANYTHING ELSE" | Advises deleting files "that haven't been accessed in a long time" | None | Claude writes through `create` / `str_replace` / `insert` / … | *judgement:* `view` then read on demand | None | API, client-side storage | Not measured |

## What only context-standard does

[DECISION, 2026-10-21] "Only" means among the 16 tools in the table, as far as their
docs described them on 2026-10-21.

1. [INFERENCE, 2026-10-21] **A claim-level evidence vocabulary.** Seven tags plus "never
   upgrade a tag without new evidence". Spec Kit's `[NEEDS CLARIFICATION]` matches only
   `[OPEN]`. Graphiti links a fact to the episode it came from, but does not say whether
   that episode was an authority, a measurement or a guess.
2. [INFERENCE, 2026-10-21] **Verification freshness, separate from write time.**
   `verified` and `review_by` say when someone last re-checked a file against its source.
   Graphiti's validity windows say when a fact was true in the world, mem0's expiration
   hides a memory, and Claude Code's `modified` records the last write. None of them says
   "nobody has checked this since X". The Anthropic memory tool advises the opposite
   policy, deleting files nobody reads; the standard says "never delete a stale file:
   verify it".
3. [INFERENCE, 2026-10-21] **A dedicated negative-trigger field.** `do_not_read_when`
   prevents wrong loads by task. Other tools can exclude only through free text in a
   description, or by agent (Copilot `excludeAgent`).
4. [INFERENCE, 2026-10-21] **Decisions with supersession, and unknowns with a resolution
   condition, as required files from day one.** Cline keeps decisions inside
   `activeContext.md` and `systemPatterns.md`, and Kiro asks authors to explain why. Neither
   supersedes a decision or records what would resolve an unknown. Spec Kit's
   constitution amendment rationale is the closest, but it covers only the constitution.
5. [INFERENCE, 2026-10-21] **A hierarchy with promotion.** Nearest-wins is shared with
   AGENTS.md. Moving a fact up a level and leaving a link behind is not described by any
   other tool surveyed.

[INFERENCE, 2026-10-21] Not unique, and not to be claimed as such: stable ids and
wikilinks (basic-memory has permalinks and `[[relations]]`), nearest-file precedence
(AGENTS.md), progressive disclosure (Agent Skills), validation over plain Markdown
(basic-memory), and treating facts as invalidated instead of deleted (Graphiti).

## Where it is behind, and what to copy

Ordered by value for effort. These were recommendations only when written; no rule was
changed in that edit. [DECISION, 2026-10-21] Items 2, 3 and 7 were adopted later the same
day: the companion `CLAUDE.md` template, tables of contents, the evaluations in `evals/`,
and the exact definition of `drifted` (see `CHANGELOG.md`).

1. [INFERENCE, 2026-10-21] **A validator — copy `skills-ref validate`, basic-memory
   `schema_validate` / `doctor`, and Spec Kit `analyze`.** Seven of the eleven audit
   checklist items are mechanical: index ↔ disk, id resolution, required fields,
   `review_by`, untagged numbers, sizes, `used_by`. Without a tool they depend on the
   agent's diligence. This is the MCP's first job. (Since 2026-10-21 the checklist has
   thirteen items, nine of them mechanical: the two added check `review_by` against
   `verified` and the five root files.)
2. [INFERENCE, 2026-10-21] **Claude Code interop — copy Claude Code's own `@import`.**
   Under the default rule in `ctx/reference/agents-md`, even a private `CLAUDE.local.md`
   turns `AGENTS.md` off, and the setting that changes this cannot be shipped in a repo.
   The AGENTS template could ship a one-line companion `CLAUDE.md` containing
   `@AGENTS.md`, the only fix available at repo level.
3. [INFERENCE, 2026-10-21] **Agent Skills conformance — copy the best-practices
   checklist.** `references/01`, `02`, `03` and `06` are 108–139 lines and have no table of
   contents; best practice asks for one above 100 lines. The skill has no evaluations,
   where best practice asks for "at least three". `SKILL.md` (139 lines with front matter)
   and its description (635 characters) are within limits.
4. [INFERENCE, 2026-10-21] **Path-scoped routing — copy Cursor `globs` / Copilot `applyTo`
   / Claude `paths` / Kiro `fileMatch`.** An optional `applies_to:` glob list would let a
   host load a file when matching code is touched, not only when the task text matches.
   Worth it only for context folders that sit next to code.
5. [INFERENCE, 2026-10-21] **An explicit write-back trigger — copy Cline's "update memory
   bank".** The standard depends on "the one habit that matters". A named prompt
   ("update context") that walks the write-back table would make the habit callable. It
   fits as an MCP prompt.
6. [INFERENCE, 2026-10-21] **Change review — copy Kiro's "require reviews".** Context
   changes go through PR review like code. Already true in this repo; it could be stated
   in `ctx/standard/maintenance`.
7. [INFERENCE, 2026-10-21] **Define "drifted" exactly.** The staleness table in
   `ctx/standard/maintenance` says ">2x past `review_by`" without saying 2× of what. Any
   tool that computes it needs one reading.

Do not copy:
- [INFERENCE, 2026-10-21] Search indexes or embeddings (basic-memory SQLite, mem0,
  Graphiti). The 40-file target keeps front matter small enough to scan, and
  `ctx/reference/kb-patterns` already puts bulky material in the retrieved-on-demand tier.
- [INFERENCE, 2026-10-21] Read everything at task start (Cline, Letta blocks, Kiro
  `always`). It contradicts "index first, load late" (`ctx/standard/principles` §3).
- [INFERENCE, 2026-10-21] Per-claim temporal validity (Graphiti). "Was X until DATE" in
  prose covers it at this scale.

## Implications for the MCP

[ASSUMPTION, 2026-10-21] The MCP is a proposed local stdio server, not yet in this repo,
that reads context folders on disk and treats the Markdown as the only source of truth.
Falsified if it is meant to be hosted or multi-user.

[INFERENCE, 2026-10-21] Design rule: the server does what is mechanical and checkable, and
leaves semantic matching and writing prose to the model. Every tool must give the same
answer for the same files on the same date.

| Tool | Input | Output | Why |
|---|---|---|---|
| `ctx_manifest` | `folder` (path; default: nearest `AGENTS.md` above cwd), `as_of?` (date) | Per file: `id`, path, `summary`, `read_when`, `do_not_read_when`, `status`, `confidence`, and a freshness verdict (`fresh` / `unverified` past `review_by` / `drifted` per the maintenance staleness table); plus the `AGENTS.md` and `INDEX.md` paths | Scout pattern in one call. The model matches the task against triggers without opening files. Freshness is computed once, not re-derived by each agent. |
| `ctx_get` | `id` | Body, parsed front matter, freshness verdict, `depends_on` resolved to paths, and dependents computed from `depends_on` across the folder | Cross-references are ids, and today they resolve only by grep. Computing dependents removes the need for hand-kept `used_by`. |
| `ctx_audit` | `folder`, `as_of?` | Findings `{rule, file, line?, detail}` for the seven mechanical checklist items, plus the four items left for the model to judge | Closes the linter question open since 2026-09-14. The output can go straight into the CHANGELOG audit entry. |
| `ctx_mark_verified` | `id`, `evidence` (URL or method), `date?` | Patch setting `verified` and recomputing `review_by` from `volatility`; the patch is returned, not applied | The arithmetic is mechanical and easy to get wrong by hand. Returning a patch keeps the write in the host's normal edit/review path. |
| `ctx_scaffold` | `folder`, `project` | The five root files from templates, returned as a patch | For hosts that do not load skills. Lowest priority. |
| prompt `update-context` | — | The write-back table as a guided checklist | Makes Cline's explicit trigger available in any MCP host. |
| resource `ctx://<id>` | — | The file | Lets hosts attach a file by id. |

[OPEN, 2026-10-21] Read-only server (patches) or write tools. Read-only matches
"context files are notes", avoids Graphiti's no-auth risk, and keeps the human review
gate. Write tools are cheaper per update (`ctx/standard/principles` §9). Resolved by: the
user's call before the MCP is implemented. [DECISION, 2026-10-21] Resolved: read-only in
v1, with `ctx_manifest`, `ctx_get` and `ctx_audit`; see `ctx/mcp/spec`.

What not to build, because others do it better:
- [INFERENCE, 2026-10-21] Vector or graph memory (mem0, Graphiti, the official memory
  server): they solve recall over large, unstructured histories; this standard targets
  about 40 curated files per folder.
- [INFERENCE, 2026-10-21] Automatic extraction of memories from conversations (mem0,
  Graphiti): it writes what the model already knows (`ctx/standard/principles` §2) and
  loses the evidence tag at the moment of writing.
- [INFERENCE, 2026-10-21] Library documentation (Context7): `ctx/standard/principles` §2
  says link, do not restate. Use Context7 next to this MCP.
- [INFERENCE, 2026-10-21] Server-side task-to-file matching with embeddings: the model
  matching on `read_when` from `ctx_manifest` is cheaper and auditable.
- [INFERENCE, 2026-10-21] Its own loading of `AGENTS.md` / `CLAUDE.md`: hosts already do
  it, and duplicating it double-loads context.

## Positioning sentence for the README

> [INFERENCE, 2026-10-21] Proposed, not adopted: *Plain-Markdown project context for AI
> agents in which every file says when it was last verified and every claim says how it is
> known, so an agent can tell a checked fact from a guess, and an audit can tell you what
> has gone stale.*

## Assumptions made

- [ASSUMPTION, 2026-10-21] Absence claims ("none", "not documented") cover only the pages
  in `sources:`. Falsified by a doc page showing the feature.
- [ASSUMPTION, 2026-10-21] GitHub stars stand in for adoption; Cursor, Copilot, Kiro and
  Claude Code publish no comparable number, so they are "not measured". Falsified by an
  official usage figure.
- [ASSUMPTION, 2026-10-21] `drifted` in `ctx_manifest` reads ">2x past `review_by`" as
  "more than one full volatility interval past `review_by`". Falsified if the standard
  defines it otherwise (see item 7 above). Resolved 2026-10-21: `ctx/standard/maintenance`
  now defines it exactly this way.
- [DECISION, 2026-10-21] One tag rule for the whole comparison table, stated above it,
  instead of a tag in every cell, to keep the table readable.
- [DECISION, 2026-10-21] Vendor benchmark scores are left out of the table: they measure
  conversational recall, which most tools here are not built for.
  [OFFICIAL, 2026-04] mem0's README reports LoCoMo 92.5 and LongMemEval 94.4 for its
  managed platform — vendor-reported, not audited here.
- [CONTESTED, 2026-10-21] Spec Kit's README spells commands with a hyphen
  (`/speckit-converge`) and lists no analyze command; `spec-driven.md` spells them with a
  dot (`/speckit.analyze`). This file uses README spellings, and plain `analyze` for the
  command only `spec-driven.md` names, because the README is the newer entry point.

## Open questions

- [OPEN, 2026-10-21] Which products support Agent Skills? The agentskills.io client list
  renders client-side and could not be read. Resolved by: reading it in a browser that
  runs JavaScript.
- [OPEN, 2026-10-21] Does the 7-tag vocabulary improve agent behaviour measurably over
  untagged notes? Resolved by: an eval of the same tasks on tagged and untagged folders,
  which is also the evaluation set Agent Skills best practices asks for.
- [OPEN, 2026-10-21] Should `applies_to` globs join the schema? Resolved by: one real
  context folder that lives next to code and misses a load because it has none.
