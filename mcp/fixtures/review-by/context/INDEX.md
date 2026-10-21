---
id: fx/review-by/index
title: Index — review-by fixture
summary: Every file in this fixture and when to read it.
read_when: ["you need to find the right file"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-01
review_by: 2027-04-01
tags: [meta, index]
---

# INDEX — review-by fixture

Read `AGENTS.md` first.

| File | Read it when |
|---|---|
| `AGENTS.md` | Always, first. |
| `CHANGELOG.md` | You need to know what changed. |
| `decisions.md` | You are making or revisiting a choice. |
| `open-questions.md` | You are looking for what we do not know. |
| `clamped.md` | You need a review_by computed through the end-of-month clamp. |
| `wrong-interval.md` | You need a review_by set from the wrong volatility. |
| `off-by-one.md` | You need a review_by one day late. |
