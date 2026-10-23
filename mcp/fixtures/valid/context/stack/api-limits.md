---
id: fx/valid/stack/api-limits
title: API limits
summary: What the API allows per key, and what we measured.
read_when: ["you are calling the API", "you are handling a 429 response"]
status: stable
confidence: high
volatility: fast
verified: 2026-09-30
review_by: 2026-11-30
sources:
  - https://api.example.com/v1/limits
used_by: [fx/valid/stack/hosting]
tags: [api, limits]
---

# API limits

## TL;DR

- [OFFICIAL, 2026-09] The API allows 100 requests per minute per key.
- [MEASURED, 2026-09] 429s start at 120 requests per minute — 4 runs × 250 requests, client log.
- [INFERENCE] The documented limit is conservative by about 20%.

## Details

[OFFICIAL, 2026-09] Each response carries the header `X-RateLimit-Remaining: 99`. See
ctx/standard/principles §3, decision D-001, client v2.1 and PR #1.

| Plan | Requests per minute |
|---|---|
| Basic | 100 |

```text
retries = 4
```

Verified on 2026-09-30 against https://api.example.com/v1/limits.
