# context/ — the living-context standard

This folder defines how every context folder in this workspace is written and maintained.
It is deliberately small.

**Start with `AGENTS.md`** if you are an AI agent.
**Start here** if you are a human.

## What problem this solves

Each new chat session with an AI agent starts from zero. Everything decided, discovered,
ruled out or verified in the last session is gone. The usual workaround — pasting a giant
project document into every conversation — makes things worse, because model accuracy
falls as the context window fills.

A *living context* is the fix: a small set of well-indexed, self-describing files that an
agent can navigate to find exactly the 2–3 documents its current task needs, plus a
protocol that makes each session leave the folder slightly better than it found it.

## Layout

| Path | What it is |
|---|---|
| `AGENTS.md` | Routing rules. The file an agent reads first. |
| `INDEX.md` | Map of every file here, with one-line "read this when…" descriptions. |
| `standard/` | The seven rule documents. Principles, file format, folder architecture, navigation, maintenance, anti-patterns, provenance. |
| `templates/` | Copy-paste starting points for every kind of file. |
| `reference/` | Distilled external research the standard is built on, with sources. |
| `CHANGELOG.md` | What changed in this standard and why. |

## How to use it

Creating a new project context folder:

1. Copy `templates/AGENTS.template.md` → `<project>/context/AGENTS.md`, fill it in.
2. Copy `templates/INDEX.template.md` → `<project>/context/INDEX.md`.
3. Create topic files from `templates/topic-file.template.md`, one subject each.
4. Add `decisions.md` and `open-questions.md` from their templates on day one, even empty.

Working in an existing one: read its `AGENTS.md`, then its `INDEX.md`, then only what you need.

## The one habit that matters

At the end of any session that produced a durable fact, a decision, or a dead end —
write it to the right file before the session ends. Not "later". The value of this
system is entirely a function of whether that habit holds.

---
Created 2026-09-14. Maintained per `standard/05-maintenance-protocol.md`.
