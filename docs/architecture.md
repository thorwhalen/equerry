# Architecture: an organisation layer for a fleet of agents

Design record, 2026-11-09. The principal's own words that started it: "a model where I am a sort of CEO … agents that do their work, but also agents that verify the work, possibly multiple agents that have different concerns … any relevant manager agent can utter their opinion, but I also have this filtered and translated for me, so I only have to see the most important stuff", and later: "I would like to have a secretary." Research: [docs/research/](research/README.md).

## Verdict: a chart and a protocol over existing tools, with a thin package for the glue

The sibling tools already cover most roles (watching live sessions, obligations and a phone board, past-session mining, records of people, publishing for a phone, channels). The organisation is a **chart** (which component plays which role), **contracts** (what each hands the next) and **one gate rule**. What is new and reusable (the desk schema, the brief pipeline's triage stages, reminders, the representative's verdict schema, the exception-report brief) is what this package will hold. Nothing here is a new agent framework.

## The chart

| Role | Plays it | Contract |
|---|---|---|
| Principal (CEO) | the person | decides one-way doors; reads exception items and briefs only |
| Chief of staff (secretary) | one long-lived agent with read tools over the fleet and a proposal channel; it relays, never performs | input: the principal's words (text; voice is an adapter); output: answers, proposals the principal confirms, dispatches to workers |
| Representative | a reader over the principal's sourced record of positions | input: an item with a recommendation; output: `risk ∈ {low, medium, high}`, reason citing a record line, what the principal would say instead, coverage ∈ {direct, adjacent, none} |
| Managers by concern | one strong reviewer model, one checklist per concern (design, architecture, privacy-legal, business, people, marketing), fresh context, read-only | input: a diff or a plan plus the checklist; output: findings in one opinion schema (severity, where, rule, fix) |
| Meta-judge | collapses N findings into exception items per breached tolerance | output: exception items (what, which tolerance, impact, options, recommendation, predicted objection) under a daily cap |
| Desk | the store of items that need the principal | one `Item` schema (below); every surface renders it |
| Brief | the cadence-driven "what needs me" | ingest → normalise → rank → compose → render → deliver → feedback |
| Reminders | intent + trigger policy + channel policy + escalation | a store and a scheduler; the brief is one of its channels |
| Memory | the override log, ledgers, issues, past sessions | append-only; the record is updated by a reviewed consolidation, never by re-mining |
| Workers | the agent sessions doing the work | dispatched by the chief of staff through the lookout; report by ledger |

## The gate

An action happens without the principal only when both hold:

1. **Reversible by rule.** No spend, deletion, message to a person, public publication, merge outside the principal's own organisations, secret or account change. The class is decided by a rule, never by a model.
2. **Low disagreement risk by the representative**, with direct or adjacent coverage.

Everything else becomes an exception item on the desk, annotated "Recommended: X. The preference model thinks you may disagree because Y [source]". Items under tolerance are logged, not shown. Near-misses are shown first: they teach the record most.

The item, its states, verbs, events and desk store are specified in [item.md](item.md) (schemas in `schemas/`); the action classes and their allowed / ask first / off settings in [consent-table.md](consent-table.md).

## Decision rights (RAPID)

Decide: the principal, for one-way doors and whatever the record says they keep. Recommend: the worker, then the representative. Agree (a veto to resolve): privacy-legal and spend. Input (may be overruled): design, architecture, business, marketing, people. Perform: the worker; the chief of staff relays.

## Seams (one keyword argument each; defaults are the strongest thing that needs no new dependency)

| Seam | v1 default | Replacement you can point at |
|---|---|---|
| where items live | GitHub issues plus a local JSON store | a database when volume demands it |
| who rates disagreement risk | the acquaint record reader | a fine-tuned classifier once the override log has enough points |
| how briefs are composed | the sibling overview pipeline | a different composer per channel |
| how reminders are scheduled | the host's scheduler (launchd / cron) | a durable workflow engine |
| how the principal is reached | the phone page plus push | voice in and out, chat channels |

Surfaces, in order: agent skills and CLI first; the phone page; voice as an adapter; MCP when another agent needs to drive it. The core never imports a surface.

## Learning

Every verdict is logged with the principal's later move (approved, overrode, edited). Overrides of `low` and acceptances of `high` go to the record as observations; a reviewed consolidation updates the positions. The `low`/`medium` threshold is recalibrated on that log. A restated preference supersedes older lines; it does not average with them.

## Metrics

False auto-approve rate (overrides of items the gate let through); items shown per day and the override rate on them (alert fatigue if it climbs); repeat corrections per month (should fall); the principal's own consistency on repeated items, as the ceiling.
