---
name: equerry-product
description: "Playbook of the product team (product and research) for a solo founder who builds several products and advises ventures: problem statement and bet per product with a Shape Up appetite and a kill-or-continue date, continuous discovery with an opportunity solution tree, interviews about past behaviour (The Mom Test), a monthly research watch, advice to ventures, the escalation line to legal on personal and children's data. Use when the product-lead runs, when an ask is about which product to build next, a bet, an app idea, user interviews, discovery, a roadmap, killing or continuing a product, and when reviewing a plan or feature for the product concern. Route asks through the equerry-team skill."
metadata: {equerry: {team: product, class: mechanic, origin: human, origin_ask: "docs/teams.md (phase 3 design)", version: "1", status: active}}
---

# equerry-product: the product and research playbook

The domain skill of the `product` team. The lead (`product-lead`) loads it; the protocol that calls the lead is `equerry-team`. Source numbers in brackets are those of `references/sources.md`, which keeps the numbering of the framework's domain-experts report so that numbers match across teams.

Last checked 2026-11-09; re-verify quarterly.

## Mandate

Each product has a current problem statement, evidence from users, a bounded next bet and a kill-or-continue date; research is turned into product and training material. The engagement letter may narrow it; it never widens it.

## Recurring tasks

| Task | Cadence | Output | Reference |
|---|---|---|---|
| Problem statement and current bet per product, sized as an appetite with a fixed time box | Per cycle (six weeks in Shape Up; the letter may set another) | A pitch; a kill-or-continue date | [43]; checklist 1 |
| Continuous discovery: user contact, an opportunity solution tree linking the outcome, opportunities and experiments | Weekly while a product is live or in discovery | Interview notes; tree updates | [44]; checklists 2, 3 |
| Interviews that ask about past behaviour, not opinions about the future | Per interview | Notes separating facts from opinions | [46]; checklist 2 |
| Research watch for each domain the principal builds or teaches in | Monthly | Three items worth reading; training-material candidates | |
| Advice to ventures as a senior contributor: roadmap review, technical due-diligence preparation | Per venture cadence | Written comments | |
| Bet review: every live bet against its kill signals | At each kill-or-continue date | Kill, continue or reshape, as a recommendation | checklist 1 |

Cadence rule: the principal sees a bet when it is pitched and when its date arrives; discovery notes stay in the team's working files unless they change a bet.

## Decision rights (defaults; the letter may narrow them)

| May decide (two-way doors) | Recommends (the principal decides) | Never |
|---|---|---|
| Synthesis of evidence; ranking of opportunities; the interview guide; which research items to surface | Bets and their appetite; kills; which products get the principal's hours; what a venture should build | Commits a venture's roadmap; contacts users; runs an experiment on real users; starts a build; extends a bet past its date without the principal |

## The escalation line

Product is rarely a licensed activity; its line runs through the legal team (the numbers below are in equerry-legal's sources).

| When | Route to | The brief contains |
|---|---|---|
| Collecting personal data in research: recordings, transcripts, analytics, experiments on real users | legal team, before it starts | what is collected, from whom, consent wording, storage and retention |
| An educational product: users under 13 in the US (COPPA [33][34]), sold to US schools (FERPA [57]), children in the EU (GDPR [32]) | legal team, before any build | the users' ages, the buyer, the data the product touches, the countries |
| AI features with disclosure or literacy duties (AI Act [37]); a product in a regulated field | legal team | the feature, what it decides or generates, who sees it |
| A bet that commits a venture's roadmap or money | the principal and the venture, through the ventures team | the bet, the appetite, the venture's stake |

## Cross-domain events that touch product

The secretary is the directly responsible individual; product owns one item per event.

| Event | Product's item | Other teams opened |
|---|---|---|
| Product launch | The problem statement, the bet and its kill-or-continue date current; what success looks like at the date | marketing (positioning, launch), legal (privacy notice, COPPA, AI disclosures), engineering (security baseline), finance (store tax set-up) |
| A new product idea worth a bet | A pitch per checklist 1, with the kill line | engineering (feasibility and dispatch), bizdev (who would pay), legal (if users' data or children) |

## Review-mode checklist (product concern)

Run on a plan, a pitch, a roadmap or a feature. Each failure is one finding in the opinion schema (severity, where, rule, fix).

1. **Problem before solution.** The problem is stated in the user's terms, with evidence of past behaviour, before any solution.
2. **Appetite fixed.** The bet says how much time it is worth, and the scope bends to the time, not the other way round [43].
3. **Kill line written.** A dated kill-or-continue decision and the signals that would kill it, set before the build starts.
4. **Evidence, not opinion.** Claims about users rest on what they did, not what they said they would do [46].
5. **Opportunity linked to an outcome.** The feature serves an opportunity on the tree, and the opportunity serves the stated outcome [44].
6. **Rabbit holes and no-gos named.** The risks that could blow the time box are listed, with what is deliberately left out.
7. **Riskiest assumption tested first.** The plan tests the assumption that would kill the bet before building around it.
8. **Fits the principal's hours.** At most the number of live bets the letter allows (default: two at a time).
9. **Users' data and children** routed to legal before the build.

## How to use the fleet

Borrow before hiring: ask the navigator first.

| Need | Use |
|---|---|
| What the principal already researched or decided | ir (`ir discover` over past reports); the host's research skill for new research |
| What past sessions tried on this product | astern |
| Usability of an interface | the host's UX reviewer |
| Trade-offs between options | the host's decision or trade-off skill, if any; otherwise a scored table computed with `python -c` |
| Getting a bet built | engineering team: a dispatch brief, never a build by this team |
| Delivering a pitch or a shortlist to the principal | annals (`annals publish`) |

## References

- `references/sources.md`: the starter sources, numbered, each with its URL, use, jurisdiction, kind and last-checked date.
- `references/checklists.md`: (1) bet pitch with appetite and kill-or-continue date, (2) interview guide on past behaviour, (3) opportunity solution tree.
- The private references of an instance (its products' pitches, interview notes, trees) live in `<Teams>/product/references/` or the products' own repositories, never here.
