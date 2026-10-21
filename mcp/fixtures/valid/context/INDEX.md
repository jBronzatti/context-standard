---
id: fx/valid/index
title: Index — valid fixture
summary: Every file in this fixture and when to read it.
read_when: ["you need to find the right file"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-01
review_by: 2027-04-01
tags: [meta, index]
---

# INDEX — valid fixture

Read `AGENTS.md` first.

| File | Read it when |
|---|---|
| `AGENTS.md` | Always, first. |
| `CLAUDE.md` | Never; it only imports `AGENTS.md` for Claude Code. |
| `README.md` | You are a human. |
| `CHANGELOG.md` | You need to know what changed. |
| `decisions.md` | You are making or revisiting a choice. |
| `open-questions.md` | You are looking for what we do not know. |
| `stack/api-limits.md` | You are calling the API. |
| `stack/hosting.md` | You are changing where the service runs. |
| `stack/notes.draft.md` | You need the stack notes. |
| `vendor/upstream/limits.md` | You need the vendor's own wording. Excluded from this folder. |

## Downstream folders

| Folder | Scope |
|---|---|
| `feature-x/` | One feature, with its own `AGENTS.md`. |
