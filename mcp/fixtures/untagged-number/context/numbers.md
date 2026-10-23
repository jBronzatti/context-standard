---
id: fx/untagged-number/numbers
title: Numbers
summary: Fixture file for numbers.
read_when: ["you are testing numbers", "you are reading a fixture"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-01
review_by: 2027-04-01
tags: [fixture]
---

# Numbers

## Limits 2026

Responses are cached for 300 seconds.

[OFFICIAL] The API allows 100 requests per minute per key.

Up to 5 retries. [INFERENCE] Usually 2 are enough.

- [MEASURED, 2026-09] 340 ms p95 — 10k requests over 24 h, APM.
- Timeouts after 30 seconds.
  - [DECISION] We retry 3 times.
- [DECISION] We batch 50 payments.
  - Each batch takes 2 seconds.

> Quoted: the limit is 7 per second.

| Plan | Price |
|---|---|
| Basic | 10 |

```text
retries = 4
```

See ctx/standard/principles §3, decision D-001, v2.1, PR #1.

Verified on 2026-10-21 against https://example.com/v1/limits.

Set `timeout_ms: 300`.

<!-- draft: 9 items -->

A paragraph that starts plain
and holds 12 items on its second line.

***

[ASSUMPTION] A paragraph tagged on its first line
keeps 8 items covered on the next. Falsified by nothing; it is a fixture.
