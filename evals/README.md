# Evaluations for the context-standard skill

Four scenarios that the skill must handle. They repeat the four manual test sessions of
2026-10-20 described in pull request #1: a project from scratch, storing a fact, an audit,
and a plain `AGENTS.md` in a Node repo. Re-run them after any change to `SKILL.md`'s
description or rules.

| File | Scenario | Should trigger |
|---|---|---|
| `new-context-folder.json` | Create a context folder from scratch | yes |
| `record-measured-fact.json` | Record a measured fact in an existing folder | yes |
| `audit-folder.json` | Audit an existing folder | yes |
| `plain-agents-md.json` | Edit the plain `AGENTS.md` of a Node repo | no |

## Format

Each file is one evaluation in the format of Anthropic's Agent Skills best practices
("Build evaluations first"): `skills`, `query`, `files`, `expected_behavior`. One field is
added, `should_trigger`, borrowed from the trigger-query format on agentskills.io, so that
a runner can check activation without parsing prose.

There is no built-in runner. To run one by hand:

1. Install the skill (see the root `README.md`).
2. Copy each path in `files` into an empty directory, dropping the leading `files/`:
   `files/product-a/context/INDEX.md` becomes `product-a/context/INDEX.md`.
3. Start a new Claude Code session in that directory and send `query` unchanged.
4. An evaluation passes when the skill loads exactly when `should_trigger` is true and
   every `expected_behavior` item holds. Run each one 3 times, with every model you
   support.

These files live outside `skills/context-standard/` so that installing the skill does not
copy test fixtures.

## Fixtures

`files/product-a/context/` is a small context folder with three planted defects, which
`audit-folder.json` expects to be found:

- `INDEX.md` lists `stack/pricing.md`, which does not exist.
- `stack/acme-api.md` is `volatility: fast` with `review_by: 2025-08-02`, so it is drifted
  on any date after 2025-10-02.
- `stack/acme-api.md` states a number (300 seconds) without a claim tag.

`CHANGELOG.md` and `open-questions.md` there have `review_by: 2026-12-01`, and the other
three root files `2027-04-01`. From the day after each date, an audit correctly reports
those files as unverified too. Before that happens, move `verified` and `review_by`
forward together, keeping `review_by` = `verified` + interval, and log it.

`files/node-app/` is a Node repo with a plain `AGENTS.md`: no front matter and no
`INDEX.md`, the case the skill's description excludes.
