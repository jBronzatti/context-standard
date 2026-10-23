---
id: product-a/stack/acme-api
title: Acme payments API — limits and errors
summary: What the Acme API allows, how it fails, and how product-a must react.
read_when:
  - "you are calling the Acme API"
  - "you are handling an error returned by the Acme API"
status: stable
confidence: medium
volatility: fast
verified: 2025-06-02
review_by: 2025-08-02
sources:
  - https://docs.acme.example/payments/errors
tags: [acme, api, limits]
---

# Acme payments API — limits and errors

## TL;DR

- [OFFICIAL] Errors come back as JSON with a `code` and a `message`.
- [OFFICIAL] Rate-limit errors use HTTP status `429`.
- [ASSUMPTION] The API does not limit our request rate in practice, because its docs give
  no limit. Falsified if a load test gets `429` responses.

## Caching

Responses to `GET /payments/{id}` are cached by the client for 300 seconds.

## Open questions

- [OPEN] Whether the limit, if any, is per shop or per key. Resolved by: a load test with
  two shops on one key; tracked in `product-a/open-questions`.
