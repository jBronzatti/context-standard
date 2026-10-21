# context-standard — the living-context standard

A standard for the context folders AI agents read, packaged as an Agent Skill for
Claude Code.

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
The `context-standard` skill teaches Claude that protocol: it loads automatically when you
create, edit, audit or navigate a context folder.

## Layout

| Path | What it is |
|---|---|
| `skills/context-standard/` | The skill. Installing it means copying this folder. |
| `skills/context-standard/SKILL.md` | The rules in short, the audit checklist, and which reference to open when. |
| `skills/context-standard/references/` | The seven rule documents: principles, file format, folder architecture, navigation, maintenance, anti-patterns, provenance. |
| `skills/context-standard/templates/` | Copy-paste starting points for every kind of file. |
| `reference/` | Distilled external research the standard is built on, with sources. Not part of the skill. |
| `evals/` | Four evaluations of the skill, with their fixtures. Not part of the skill. |
| `AGENTS.md`, `INDEX.md` | How an agent finds its way around this repository. |
| `CHANGELOG.md` | What changed in this standard and why. |

## Install

Copy the skill folder into your personal skills directory. It is then available in every
project.

**macOS / Linux**

```sh
git clone https://github.com/jBronzatti/context-standard.git
mkdir -p ~/.claude/skills
cp -R context-standard/skills/context-standard ~/.claude/skills/
```

**Windows (PowerShell)**

```powershell
git clone https://github.com/jBronzatti/context-standard.git
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills" | Out-Null
Copy-Item -Recurse -Force context-standard\skills\context-standard "$env:USERPROFILE\.claude\skills\"
```

To scope it to one project instead, copy the folder into that project's `.claude/skills/`.
To update, run `git pull` inside the clone, delete the installed `context-standard`
folder (so files removed upstream go too), and copy again. Start a new Claude Code session
and check that `/context-standard` appears in the `/` menu.

Agents that do not load skills can still use the standard: point their `AGENTS.md` at
`skills/context-standard/SKILL.md`.

## Example

```text
> Set up a context folder for product-a.
```

Claude creates `product-a/context/` with the five required files (`AGENTS.md`, `INDEX.md`,
`CHANGELOG.md`, `decisions.md`, `open-questions.md`) from the templates where one exists,
with front matter filled in, and asks you for the project facts it cannot infer.

```text
> We decided to release weekly instead of daily. Where does that go?
```

Claude records it in `decisions.md` from the decision-record template — a tagged
`[DECISION]`, the rejected alternative, and the trigger for revisiting it — instead of
scattering it into a topic file.

```text
> /context-standard audit product-a/context
```

Claude runs the audit checklist in `SKILL.md` against that folder, reports what
it found, and logs the audit in its `CHANGELOG.md`.

## The one habit that matters

At the end of any session that produced a durable fact, a decision, or a dead end —
write it to the right file before the session ends. Not "later". The value of this
system is entirely a function of whether that habit holds.

---
Created 2026-09-14. Maintained per `skills/context-standard/references/05-maintenance-protocol.md`.
