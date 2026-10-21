---
id: fx/valid/stack/hosting
title: Hosting
summary: Where the service runs and why.
read_when: ["you are changing where the service runs", "you are sizing the service"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-01
review_by: 2027-04-01
depends_on: [fx/valid/stack/api-limits]
supersedes: [fx/valid/stack/old-hosting]
tags: [hosting]
---

# Hosting

[DECISION] One region, sized for the API limit in `fx/valid/stack/api-limits`.
