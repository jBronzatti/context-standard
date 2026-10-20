---
id: <folder>/<category>/<subject>
title: <Human-readable title>
summary: <One sentence: what question this file answers.>
read_when:
  - "<a task an agent might be doing>"
  - "<another task>"
do_not_read_when:
  - "<a nearby task this file does NOT help with>"
status: draft            # stable | draft | contested | superseded
confidence: medium       # high | medium | low
volatility: slow         # static (12mo) | slow (6mo) | fast (2mo)
verified: YYYY-MM-DD
review_by: YYYY-MM-DD
sources:
  - <url>
depends_on: []           # ids this file relies on — the source of truth
tags: []
---

# <Title>

## TL;DR

- <The answer, if the reader reads nothing else.>
- <Three to six bullets, tagged.>

## <Substantive section>

[OFFICIAL] <claim> — <source>
[MEASURED, YYYY-MM] <claim with units> — <n=, method>
[INFERENCE] <claim> — <who says so, and what they gain from it>
[CONTESTED] <claim A, source> vs <claim B, source>. We weight <A> higher because <reason>.
[ASSUMPTION] <what we assume> — falsified if <condition>.
[DECISION] <what we chose> — see decisions.md#<anchor>.

## Open questions

- [OPEN] <unknown> — resolved by <what would answer it>.

## Sources

- <url> — <what it supports, tier A/B/C/D>
