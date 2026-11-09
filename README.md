# equerry

An organisation layer for a fleet of coding agents, for one principal who cannot read everything.

An equerry is the officer who manages a sovereign's day. This one manages yours when your work is done by many autonomous agents: it knows who is doing what, predicts which of their recommendations you would object to, has managers review the work by concern, briefs you on a cadence with only what needs you, takes "remind me of X" and decides when and how, and lets you act from a phone. Voice is a layer on top of it, not its core.

**Status (2026-11-09): design and research.** No code yet. What exists is the design and the research under `docs/`, and a working first version of the "representative" (the principal's preference model and the reviewer that uses it) built on sibling tools. The name was checked free on PyPI, npm, crates.io and GitHub on 2026-11-09 and is reserved for this project.

## What it is made of

| Role | What it does | Shape |
|---|---|---|
| Chief of staff (the secretary) | the one agent the principal talks to; relays, never performs | an agent with read tools and a proposal channel |
| Representative | rates a recommendation's disagreement risk against the principal's recorded positions, with the reason | a reader over a sourced record; a verdict schema |
| Managers by concern | design, architecture, privacy, business, people, marketing: each reviews with a checklist, fresh context, read-only | one strong model, N checklists; opinions in one schema |
| Meta-judge | collapses findings into exception items, under a daily cap | a brief |
| Desk | the items that need the principal, with recommendation and predicted objection | a store with one schema, rendered on a phone |
| Brief | "what needs me", on a cadence (per weekday, local time), from sessions, GitHub, chat, mail | ingest → rank → compose → render → deliver → feedback |
| Reminders | intent + trigger policy + channel policy + escalation; timing inferred when not given | a store and a scheduler |
| Memory | every verdict and override logged; the record learns from corrections, not from re-mining | an append-only log and a reviewed consolidation |
| Gate | act without the principal only when the action is reversible by rule AND the representative says low risk | one rule |

The design is in [docs/architecture.md](docs/architecture.md). The research that led to it is in [docs/research/](docs/research/README.md).

The desk's one record (the item, its states, verbs and event log) is specified in [docs/item.md](docs/item.md) with JSON Schemas in [schemas/](schemas/); what agents may do without asking is the [consent table](docs/consent-table.md), with the default as data in [templates/consent.yaml](templates/consent.yaml).

## Teams

Domain work (legal, finance, people, bizdev, marketing, product, engineering, ventures) is done by **teams**: one experienced generalist lead per domain, modelled on a fractional executive, working under a written engagement letter, judging coverage from artefacts before acting, and growing its skills only from checked work through gates. The design is in [docs/teams.md](docs/teams.md). Everything is agent skills and sub-agent files under `.claude/`; there is no code.

| Kind | Name | What it does |
|---|---|---|
| Skill | `equerry-team` | the calling session's protocol: frame, assess, act, verify, distil, gate; the letter, roster, log and gate formats; templates |
| Skill | `equerry-lead` | the lead's side, preloaded by every lead: what it reads, what it returns per verdict, the reply shape, how it proposes a primitive as text |
| Skill | `equerry-council` | several leads on one question, the contradictor always, disagreements kept |
| Skill | `equerry-team-review` | monthly per team: recurring gaps, unused primitives, false-coverage rate, retirements |
| Skill | `equerry-secretary` | the front desk, on demand: a ranked, capped brief of what needs the principal; "remind me of X" as a desk item; routing an ask to a team |
| Skills | `equerry-<team>` | each domain's playbook: recurring tasks, escalation line, checklists, dated sources |
| Agents | `<team>-lead` | the eight leads; tools Read, Grep, Glob, WebSearch and WebFetch only: no shell, no write tool, so the calling session runs every command and writes every file |
| Agents | `contradictor`, `checker`, `navigator` | shared staff: find what does not hold; verify against a checklist; know which tools already exist |

A host turns teams on with a `## equerry` block in its `CLAUDE.md`: six lines (where letters, logs, the dossier, venture pages, the navigator and the desk live, with the principal's login for approvals) plus an optional `Fleet map:` line for the navigator; the secretary adds `Brief sources:` and `Briefs:`. See `docs/teams.md`, "Seams". The leads are registered agents when the session starts in the host directory, whose `.claude/agents/` links to this repository's; from anywhere else the calling session inlines them (`equerry-team`).

## Relation to its siblings

equerry is glue, not a new framework. It composes tools that exist: [crowsnest](https://github.com/thorwhalen/crowsnest) (watch the live sessions), [openloops](https://github.com/thorwhalen/openloops) (obligations between you and your agents), [astern](https://github.com/thorwhalen/astern) (mine past sessions), [acquaint](https://github.com/thorwhalen/acquaint) (records of people, including the principal's own record that the representative reads), [annals](https://github.com/thorwhalen/annals) (where agents publish for a phone), [correspond](https://github.com/thorwhalen/correspond) (one facade over GitHub, mail, Slack, WhatsApp and push channels). Where a capability is missing, it is built in the sibling that owns that kind of thing, and equerry wires it.

## Licence

MIT.
