---
id: ctx/reference/llms-txt
title: The llms.txt convention
summary: The llms.txt format spec and whether it applies to a local context folder.
read_when: ["you are publishing documentation for machine consumption", "you are exposing context to external agents over the web", "someone asks whether we need an llms.txt"]
status: stable
confidence: high
volatility: slow
verified: 2026-09-14
review_by: 2027-03-14
sources:
  - https://llmstxt.org/
tags: [research, convention, llms-txt]
---

# llms.txt

## The format

[OFFICIAL] A file at `/llms.txt` (or any subpath, e.g. `/docs/llms.txt`) containing, in order:

1. Optional BOM.
2. **H1 heading** — the only required section. The project or site name.
3. **Blockquote** — a short summary containing the key information needed to understand
   the project.
4. Optional free content — paragraphs, lists, any Markdown **except headings**.
5. Optional **H2-delimited sections**, each a list of curated links in the form
   `- [name](url): optional notes`.

[OFFICIAL] Scope rule: *"A file covers the URLs under its path, and where more than one
file applies, agents should use the most specific one."* Same nearest-wins logic as
AGENTS.md.

[OFFICIAL] Companion convention: serve clean Markdown versions of pages at the original
URL with `.md` appended or substituted (`page.html.md`, or `index.md` for directory paths).

## Does it apply to us?

[DECISION] **No, not for the local context folders.** llms.txt solves discovery for agents
fetching a *website*. Our agents have filesystem access and read `AGENTS.md` + `INDEX.md`,
which carry strictly more routing information (front matter, `read_when`, freshness).

Adopt it only if this context is ever published to a URL. At that point `INDEX.md`
converts to `llms.txt` almost mechanically: H1 = project, blockquote = summary, H2
sections = the category tables.

## What is worth stealing from it

The **curated-link-list-with-notes** shape. `[name](url): what this is and when you'd want
it` is the same idea as our `read_when`, compressed to one line. Our index tables use it.
