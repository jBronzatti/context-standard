---
id: ctx/reference/kb-patterns
title: Knowledge-base architecture patterns
summary: Structural patterns for agent knowledge bases — index-first, scout, phase-scoped, wiki-plus-recipes, dependency graphs, and the RAG boundary.
read_when: ["you are designing or restructuring a context folder", "a folder has outgrown flat organisation", "you are deciding between resident context and retrieval"]
status: stable
confidence: medium
volatility: slow
verified: 2026-09-14
review_by: 2027-03-14
sources:
  - https://www.mindstudio.ai/blog/progressive-disclosure-ai-agents-context-management
  - https://umesh-malik.com/blog/ai-agent-knowledge-base-architecture
  - https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
depends_on: [ctx/reference/context-engineering]
tags: [research, architecture, patterns]
---

# Knowledge-base patterns

## Loading patterns

**Index-first.** Agent reads a structured index describing available files, identifies
what the subtask needs, fetches only that. Requires genuinely informative descriptions of
*what each file covers and when it is relevant* — a bad index is worse than no index
because it routes confidently to the wrong file.

**Scout.** A lightweight preliminary pass analyses the task and returns a manifest of
relevant files before the main run begins. Use for tasks that arrive as a sentence rather
than a spec, or where the relevant material is dynamically sourced.

**Phase-based.** Structure work into phases (research, plan, execute, review) and load
per-phase context, scoping loads so context does not accumulate across phases. The
accumulation is the point — without explicit scoping, phase-based loading becomes
load-everything-eventually.

**Skill-file separation.** Process definitions stay pure: they describe *what to do*, not
contain the material needed to do it. Reference material is fetched by the step that needs
it.

**Required components** for any of these to work: condition detection (knowing when
content is needed), a fetch mechanism, and load scoping (preventing accumulation).

## Wiki plus recipes

[INFERENCE] Split knowledge into two file types:

| | Knowledge files | Recipes |
|---|---|---|
| Contain | Positions, definitions, thresholds, decision frameworks | Ordered multi-step procedures |
| Facts | Yes, owned | **None** — reference knowledge files by name |
| Change cadence | When the world changes | When the method changes |
| Access | Needed on most turns | Needed when performing that task |

The payoff: update a fact without re-validating a procedure; refine a procedure without
re-checking every fact.

## Dependency graph as infrastructure

[INFERENCE] YAML frontmatter declaring each file's `depends_on` and `used_by` turns the
knowledge base into a bidirectional graph that a linter can check — catching dangling
references, cycles, and contradictions before they cause a bad agent run.

We encode this (`depends_on` / `used_by` / `supersedes`). We do not yet have the linter.
[OPEN] Worth writing a 50-line script that validates every id resolves and every
`depends_on` has a matching `used_by`. Resolved by: writing it when the folder exceeds
~30 files.

## The maintenance flywheel

[INFERENCE] A five-phase cycle reported from one large rollout: collect feedback →
diagnose root cause → compile minimal edits → validate with replay testing → land with
audit trails. The claim is zero regressions across improvement cycles over six weeks.

The transferable part is **minimal edits + audit trail**, which is what our changelog and
supersession rules implement. The replay-testing part has no cheap local equivalent yet.

## The RAG boundary

[INFERENCE] Dense, frequently-accessed facts → resident in context files.
Sparse, situational material (historical records, one-off precedents, long reference docs)
→ retrieved on demand, not resident.

Practical translation for a filesystem-based context: `reference/` is the
retrieved-on-demand tier. It is indexed but not read by default, and `AGENTS.md` should
never point a routine task at it.

## Anti-pattern noted across sources

Loading the whole knowledge base up front. Reported as the difference between an 80%
token reduction and none. It is also the most natural thing to do, which is why the
standard makes it explicitly forbidden rather than merely discouraged.
