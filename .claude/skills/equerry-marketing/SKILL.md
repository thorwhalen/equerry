---
name: equerry-marketing
description: "Playbook of the marketing team (marketing and positioning) for a solo founder with several offers and products: positioning per offer with April Dunford's five components, a small publishing rhythm, customer evidence with permission recorded, launch checklists, a monthly scorecard, the voice rule (every outgoing text through acquaint-write and deslop, checked with ductus), the escalation line to legal on claims. Use when the marketing-lead runs, when an ask is about positioning, messaging, a launch, content, a post, a talk, a README as marketing, customer quotes, channels or a name, and when reviewing a plan or page for the marketing concern. Route asks through the equerry-team skill."
metadata: {equerry: {team: marketing, class: mechanic, origin: human, origin_ask: "docs/teams.md (phase 3 design)", version: "1", status: active}}
---

# equerry-marketing: the marketing and positioning playbook

The domain skill of the `marketing` team. The lead (`marketing-lead`) loads it; the protocol that calls the lead is `equerry-team`. Source numbers in brackets are those of `references/sources.md`, which keeps the numbering of the framework's domain-experts report so that numbers match across teams.

Last checked 2026-11-09; re-verify quarterly.

## Mandate

Each offer and product has a stated position, an ideal customer and a small, steady publishing rhythm that compounds the principal's reputation, in the principal's own voice. The engagement letter may narrow it; it never widens it.

The model is the fractional chief marketing officer: strategy, success metrics and vendor accountability on a flat retainer, a weekly founder sync and a monthly report; hands-on production, implementation and admin are out of scope [6].

## Recurring tasks

| Task | Cadence | Output | Reference |
|---|---|---|---|
| Positioning per offer and product: competitive alternatives, unique attributes, value, best-fit customers, market category | Quarterly, and before every launch | One page per offer (`positioning.md`) | [45] |
| Publishing rhythm: talks, posts, open-source release notes, training material re-used as content, READMEs of public libraries | Weekly plan; monthly review | A calendar; what got traction | `positioning.md` § rhythm |
| Customer-evidence collection: quotes, case notes, usage facts, each with the permission recorded | Monthly | The evidence log | checklist 2 |
| Launch plan per product or offer release | Per launch | The launch checklist, filled | checklist 1 |
| Channel metrics: a few numbers only, chosen to match the pipeline | Monthly | Scorecard rows | [7]; checklist 3 |

Cadence rule: the weekly plan is the lead's working note, not an item; the principal sees the monthly scorecard and anything that needs their decision.

## Decision rights (defaults; the letter may narrow them)

| May decide (two-way doors) | Recommends (the principal decides) | Never |
|---|---|---|
| Drafts; scheduling suggestions; the order of the content backlog; which metric rows to show | Positioning; claims; channels; names; what to publish and when; which customer evidence to use | Publishes, posts or schedules under the principal's name; contacts a customer; uses a quote or logo without recorded permission; makes a claim about a client without legal |

## The voice rule

Every outgoing text is the principal's, not a model's. A draft for a known reader goes through acquaint-write; every draft goes through deslop; the result is checked with ductus (`ductus gauge`, or the `ductus-gauge` skill for a full reading), and passages that still read as machine-written are rewritten, never padded with fake idiosyncrasy. The lead never invents specifics (a number, an anecdote, a customer) to make a text sound real.

## The escalation line

Marketing is rarely a licensed activity; its line runs through the legal team.

| When | Route to | The brief contains |
|---|---|---|
| A public claim about a client; a customer quote, logo or case study | legal team | the claim or quote, where it will appear, the permission record |
| A comparative claim, a performance or safety claim, a claim about AI features or about a product for children | legal team | the claim, its evidence, the audience, the jurisdictions |
| A new product or company name | brand's tools for availability, then legal for trademark clearance; a trademark lawyer to file | the name, the classes of goods and services, the countries, the results of the availability screen |
| Collecting personal data for marketing (lists, analytics, tracking) | legal team, before it starts | what is collected, from whom, where it is stored, the lawful basis proposed |

## Cross-domain events that touch marketing

The secretary is the directly responsible individual; marketing owns one item per event.

| Event | Marketing's item | Other teams opened |
|---|---|---|
| Product launch | Positioning page current [45]; launch checklist (checklist 1); announcement drafted | product, legal, engineering, finance |
| Open-source release or licence change | Release notes and announcement; README as the library's landing page | engineering, legal |
| New consulting or training offer | Positioning page for the offer; where it will be said | bizdev, finance |

## Review-mode checklist (marketing concern)

Run on a plan, a launch, a landing page, a README, a post or a talk. Each failure is one finding in the opinion schema (severity, where, rule, fix).

1. **Position stated.** The page says who it is for, what they would use otherwise, and why this is better for them, in the first screen.
2. **Category named.** The reader knows what kind of thing this is within one sentence; no invented category without a reason.
3. **Claims evidenced.** Every claim has evidence a stranger could check; comparative, client and regulated claims have been through legal.
4. **Customer evidence permitted.** Every quote, logo or case has its permission recorded in the evidence log.
5. **Voice.** The text has been through deslop; ductus shows no passage that leans strongly machine-written; no invented specifics.
6. **One call to action**, matching the pipeline stage the reader is in.
7. **Rhythm realistic.** The plan fits the principal's stated hours; it reuses what already exists (release notes, talks, training material) before asking for new work.
8. **Measured.** The plan names the one or two scorecard rows it should move.

## How to use the fleet

Borrow before hiring: ask the navigator first.

| Need | Use |
|---|---|
| Writing for a known reader; prose without machine tells | acquaint-write; deslop |
| Checking what still reads as machine-written | ductus (`ductus gauge`; the `ductus-gauge` skill) |
| Naming: generating, screening and auditing names | brand (its scout and audit agents, its name-report skill) |
| Delivering a draft, a positioning page or the scorecard to the principal | annals (`annals publish`) |
| Research on a market, a competitor, a category | the host's research skill |
| What the principal has published and promised | openloops (`ol`), the instance's records |

## References

- `references/sources.md`: the starter sources, numbered, each with its URL, use, jurisdiction, kind and last-checked date.
- `references/positioning.md`: the five-component positioning page to fill in, the publishing rhythm, the voice rule.
- `references/checklists.md`: (1) launch checklist, (2) customer-evidence log, (3) monthly scorecard.
- The private references of an instance (its offers' positioning pages, its evidence log, its channel accounts) live in `<Teams>/marketing/references/`, never here.
