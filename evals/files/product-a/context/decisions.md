---
id: product-a/decisions
title: Decisions — product-a
summary: Choices made for product-a, the alternatives rejected, and when to revisit each.
read_when: ["you are making or revisiting a choice", "you are about to contradict an earlier choice"]
status: stable
confidence: high
volatility: slow
verified: 2026-10-01
review_by: 2027-04-01
tags: [meta, decisions]
---

# Decisions

## D-001 — Call the Acme API directly, without the SDK

- **Date:** 2026-10-01
- **Status:** active
- **Decided by:** the product-a team
- **Scope:** every call from product-a to the Acme API.

**Context.** The official Acme SDK pins an outdated major version of an HTTP library.

**Decision.** [DECISION] product-a calls the Acme REST API with its own HTTP client.

**Alternatives rejected.**
| Option | Why not |
|---|---|
| Official Acme SDK | Pins an outdated HTTP library |

**Rests on.** product-a/stack/acme-api

**Revisit when.** The SDK drops the pinned dependency.

**Outcome.**
