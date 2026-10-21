---
id: fx/valid/feature-x/retries
title: Retries
summary: When feature-x retries a request.
read_when: ["you are changing retries", "you are handling a 429 in feature-x"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-01
review_by: 2027-04-01
depends_on: [fx/valid/stack/api-limits]
tags: [retries]
---

# Retries

[DECISION] Retry on 429 only, within the limit in the parent folder.
