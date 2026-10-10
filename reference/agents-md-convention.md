---
id: ctx/reference/agents-md
title: The AGENTS.md convention
summary: What the external AGENTS.md convention specifies, how it differs from what we do, and when to care.
read_when: ["you are integrating with external agent tooling", "you want the AGENTS.md convention as others define it", "you are deciding what goes in an AGENTS.md"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-14
review_by: 2027-03-14
sources:
  - https://agents.md/
  - https://ro14nd.de/what-goes-in-agents-md/
tags: [research, convention, agents-md]
---

# AGENTS.md convention

## What it is

[OFFICIAL] An open format described as *"a README for agents: a dedicated, predictable
place to provide the context and instructions to help AI coding agents work on your
project."* Compatible with 60+ agent tools. Deliberately just Markdown — no mandated
schema.

## What the spec actually mandates

Almost nothing. [OFFICIAL] *"AGENTS.md is just standard Markdown. Use any headings you
like; the agent simply parses the text you provide."*

Commonly used sections: project overview, build/test commands, code style, testing
instructions, security considerations, commit/PR conventions, deployment steps.

## Precedence

[OFFICIAL] Nested files are supported. **The nearest file in the directory tree takes
precedence.** Explicit user chat prompts override all written instructions.

We adopt this rule unchanged — see `ctx/standard/folder-architecture`.

## Stated design principles

[OFFICIAL] Three:
1. Give agents a clear, predictable place for instructions.
2. Keep READMEs concise and focused on human contributors.
3. Provide precise, agent-focused guidance complementing existing docs.

## What belongs in it (practitioner guidance)

[INFERENCE] Include what an agent **cannot discover independently**: non-obvious build
commands, tool-specific choices ("use podman not docker"), test setups requiring special
handling, counterintuitive conventions, hard invariants, security boundaries, review
expectations.

Exclude: codebase overviews (*"empirically useless"*), standard framework conventions,
README content, dependency lists, file-by-file descriptions.

[MEASURED] Keep under 150 lines. Generated files hurt performance by 0.5–2%.

## How our usage differs

| | Convention | Ours |
|---|---|---|
| Front matter | None | Mandatory YAML |
| Purpose | Coding-agent instructions | Routing + invariants + current state |
| Scope | Usually one repo | Nested across three workspace levels |
| Content | Build/test/style | Points to an indexed knowledge base |

We are a superset: an external tool that only understands plain AGENTS.md will still read
ours correctly, because YAML front matter degrades to harmless text and the body is plain
Markdown.

## When to care

If this workspace ever gets opened by an external coding agent, Cursor, Codex, or any of
the 60+ tools honouring the convention, our `AGENTS.md` files are already the file they
look for. That is the only reason we use the name.
