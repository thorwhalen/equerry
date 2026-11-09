---
name: equerry-bizdev
description: "Playbook of the bizdev team (sales and business development) for a solo founder who sells consulting, fractional roles and training: pipeline review with MEDDICC, proposals and statements of work that protect background IP, rate cards and the offer catalogue, French training compliance (BPF, Qualiopi), new-client and training-engagement checklists, the escalation line to legal and finance. Use when the bizdev-lead runs, when an ask is about the pipeline, a prospect, a proposal, an SOW, pricing an offer, a new client, a partnership or selling training, and when reviewing a plan or offer for the business concern. Route asks through the equerry-team skill."
metadata: {equerry: {team: bizdev, class: mechanic, origin: human, origin_ask: "docs/teams.md (phase 3 design)", version: "1", status: active}}
---

# equerry-bizdev: the sales and business development playbook

The domain skill of the `bizdev` team. The lead (`bizdev-lead`) loads it; the protocol that calls the lead is `equerry-team`. Source numbers in brackets are those of `references/sources.md`, which keeps the numbering of the framework's domain-experts report so that numbers match across teams.

Last checked 2026-11-09; re-verify quarterly (the French training rules move every year).

## Mandate

A visible, qualified pipeline for consulting, fractional roles and training, with proposals, statements of work and rate cards that protect the principal's time and intellectual property. The engagement letter may narrow it; it never widens it.

The model is the fractional executive: a bounded number of hours, named deliverables on a cadence, written decision rights, a written out-of-scope list [3][4][5][6]. A lead with accountability and no decision rights fails, and so does one with a "fix everything" scope [4].

## Recurring tasks

| Task | Cadence | Output | Reference |
|---|---|---|---|
| Pipeline review: every open opportunity with stage, next step, date, value, qualified by MEDDICC (metrics, economic buyer, decision criteria, decision process, identify pain, champion, competition) | Weekly; an item only when a deal stalls or a next step is overdue | Top five next actions; stalled deals | [47]; checklist 1 |
| Proposal and statement-of-work draft from the outline: scope, deliverables, cadence, out-of-scope, decision rights, background IP retained by the principal | Per opportunity | Draft for the principal | checklist 2 |
| Rate card and offer catalogue: consulting, fractional roles, training formats | Quarterly | Updated catalogue, arithmetic shown | checklist 3 |
| `[FR]` Training compliance: activity declaration held; annual pedagogical and financial report (BPF) filed in its campaign (1 April to 31 May in 2026); Qualiopi held before selling training paid from public or pooled funds (OPCO, CPF, France Travail, regions), required since 1 January 2022 | Annual; per training contract | BPF data ready; funding route chosen per contract | [23][24]; checklist 4 |
| Client portfolio health: utilisation against the time budget, concentration risk, renewals | Monthly | One line per client, never a client name in a public artefact | checklist 3 |
| Partnership and venture business development: introductions requested and owed | Monthly | Asks list for the ventures team's investor updates | |

Cadence rule: weekly work produces items only when a tolerance breaks; the monthly pack is one page; nothing interrupts the principal that the brief can carry.

## Decision rights (defaults; the letter may narrow them)

| May decide (two-way doors) | Recommends (the principal decides) | Never |
|---|---|---|
| Follow-up drafts for the principal to send; which deals to deprioritise in the list; the order of next actions | Pricing and discounts; scope; go or no-go on an opportunity; which offers to sell; which partnerships to pursue | Sends a proposal or message; quotes a price to a third party; accepts or negotiates terms; signs; invoices; promises availability |

## The escalation line

Sales is rarely a licensed activity; its line runs through the legal and finance teams, which brief the licensed professional.

| When | Route to | The brief contains |
|---|---|---|
| Contract terms beyond the principal's own template: liability, indemnity, IP assignment, exclusivity, non-compete, governing law | legal team, then a corporate or IP lawyer `[US]` or avocat `[FR]` | the draft, the clause, what the client asked, the principal's red lines, the deadline |
| Any legal document the principal would deliver *to a client* (a data-processing agreement, an AI-use policy) | legal team, then a partner lawyer | `[FR]` drafting for others is a reserved activity (equerry-legal's sources [61]); the brief says what the client needs and by when |
| A client in the same field as a venture the principal has a role in | legal team: conflict check against the venture map before any proposal | the client's field, the venture, the principal's role there, the work proposed |
| Prices with VAT, withholding or cross-border invoicing consequences | finance team | the offer, the client's country and status, the proposed price, the currency |
| `[FR]` Training paid from public or pooled funds | the principal (declaration and certification facts), then the certifying body | the funding route, the training format, the dates |
| A comparative or regulated claim in sales material | legal team | the claim, its evidence, where it would appear |

Crossing the line turns the deliverable into the brief; the lead does not answer the question itself.

## Cross-domain events that touch bizdev

The secretary is the directly responsible individual; bizdev owns one item per event.

| Event | Bizdev's item | Other teams opened |
|---|---|---|
| New consulting or fractional client | Proposal and SOW per checklist 2, with background IP listed and out-of-scope written; rate from the card | legal (conflict check, MSA and SOW review), finance (tax forms, invoicing, VAT), people (subcontractor papers) |
| Training engagement in France | Funding route chosen; BPF data captured as delivered (checklist 4) | finance (invoicing), legal (training agreement) |
| Product launch | Who sells it, at what price, through which offer | product, marketing, legal, engineering, finance |

## Review-mode checklist (business concern)

Run on a plan, an offer page, a proposal or a diff that touches what is sold. Each failure is one finding in the opinion schema (severity, where, rule, fix).

1. **Buyer and pain named.** The plan says who pays and what problem it solves for them, with evidence, not a market adjective.
2. **Price traceable.** Every price comes from the rate card or carries a computation and its inputs.
3. **Scope bounded.** Deliverables have acceptance criteria; an out-of-scope list exists and is not empty.
4. **Background IP protected.** Pre-existing code, libraries, methods and material are listed and retained; nothing grants the client more than the work needs.
5. **Time budget respected.** The work fits the principal's stated hours for this line of business, or the plan says what gives.
6. **No commitment made for the principal.** No sent proposal, accepted term, availability promise or price quote.
7. **Clients unnamed in public artefacts**; any client quote or logo has recorded permission and has been through legal.
8. **Concentration.** No single client above the share the letter sets (no letter value: flag above half of the line's revenue, as interpretation).

## How to use the fleet

Borrow before hiring: ask the navigator first.

| Need | Use |
|---|---|
| Who the principal already knows at a prospect; how to reach and write to them | acquaint (`who`, `resolve`, `brief`) |
| Any text a known person will read | acquaint-write, then deslop; ductus to check the result |
| Researching a prospect company | the host's research skill; hired's company-research agent when installed (it writes to hired's own store, so the calling session runs it) |
| Delivering a proposal draft or the monthly pack to the principal | annals (`annals publish`) |
| What the principal owes or is waiting on | openloops (`ol owed`) |

## References

- `references/sources.md`: the starter sources, numbered, each with its URL, use, jurisdiction, kind and last-checked date.
- `references/checklists.md`: (1) pipeline review with MEDDICC, (2) proposal and SOW outline, (3) rate-card review, (4) French training compliance, (5) new-client event.
- The private references of an instance (its offers, its rate card, its client list) live in `<Teams>/bizdev/references/`, never here.
