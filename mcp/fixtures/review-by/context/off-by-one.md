---
id: fx/review-by/off-by-one
title: Off by one
summary: A static file whose review_by is one day late.
read_when: ["you are testing review_by", "you are testing the review interval"]
status: stable
confidence: high
volatility: static
verified: 2026-10-01
review_by: 2027-10-02
tags: [dates]
---

# Off by one

[DECISION] The review date is one day after the interval ends.
