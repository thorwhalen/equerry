# Bizdev checklists

Each checklist is something the `checker` sub-agent can verify a deliverable against: every item is a yes or no with the evidence that makes it yes. Numbers in brackets point to `sources.md`. Values marked "default" are the framework's; an engagement letter may set its own. Last checked 2026-11-09.

## 1. Pipeline review with MEDDICC (weekly)

Per open opportunity, one row:

- [ ] **Stage** named from the instance's stage list (default: lead, qualified, proposal, negotiation, won, lost).
- [ ] **Next step** written as an action with an owner and a date; a row with no dated next step is stalled.
- [ ] **Value** stated, with "estimate" when it is one, computed with `python -c` when it is more than one number.
- [ ] **Metrics**: the measurable outcome the buyer wants, in their words, or "unknown" [47].
- [ ] **Economic buyer**: the person who can approve the spend, by role, or "unknown".
- [ ] **Decision criteria**: what they will judge the proposal on.
- [ ] **Decision process**: the steps and dates to a signature, including the paper process (procurement, legal review), which MEDDPICC adds.
- [ ] **Identify pain**: the problem that makes acting now worth it, with the evidence it was said, not assumed.
- [ ] **Champion**: someone inside who sells for the principal when the principal is not in the room, or "none".
- [ ] **Competition**: the alternatives, including doing nothing and doing it in-house.
- [ ] Every "unknown" becomes a question for the next conversation, at most two per deal.

Output: the top five next actions across the pipeline, ranked by value times likelihood (likelihood labelled as interpretation); the stalled deals (default: no dated next step, or the date passed more than 14 days ago); deals the principal should drop, with the reason. An item reaches the principal only when a deal stalls or a decision is theirs.

## 2. Proposal and statement-of-work outline (per opportunity)

A draft for the principal's own use, built from the principal's own template. Contract terms beyond the template go to legal.

- [ ] **Parties** by role; the client's legal entity name only in the private copy.
- [ ] **Context and problem**, in the client's words, with the pain from the pipeline row.
- [ ] **Objectives and success measures**: what will be true at the end, and how both sides will know.
- [ ] **Scope**: each deliverable with its acceptance criterion and owner.
- [ ] **Out of scope**: a written list, never empty (default entries: hands-on production beyond the named deliverables, sustained coding, daily management, anything not listed in scope) [4][6].
- [ ] **Cadence and time budget**: hours per week or month, meeting rhythm, response times, the end date or renewal rule.
- [ ] **Decision rights**: what the principal decides, what the client decides, who on the client side approves.
- [ ] **Assumptions and dependencies**: access, data, people, tools the client provides, and what happens if they do not.
- [ ] **Background IP schedule**: the principal's pre-existing code, libraries, open-source packages, methods, templates and training material, listed; retained by the principal; licensed to the client only as far as the deliverables need.
- [ ] **Foreground IP**: what the client owns or receives at the end, as a recommendation marked for legal review.
- [ ] **Confidentiality** and how client material is stored and returned.
- [ ] **Fees and payment terms** from the rate card, arithmetic shown, currency and VAT treatment marked "to verify with finance".
- [ ] **Change control**: how scope changes are requested, priced and approved.
- [ ] **Term and termination**, marked for legal review.
- [ ] The draft says "draft for the principal" at the top and has been through acquaint-write (when the reader is known) and deslop.

## 3. Rate-card review (quarterly)

- [ ] Every offer listed with its format, unit (hour, day, month, workshop, seat), price, minimum engagement, inclusions and exclusions.
- [ ] Effective rate per offer computed with `python -c`, code shown: price divided by all the hours it really takes (preparation, travel, follow-up), each input labelled measured or estimate.
- [ ] Utilisation over the last quarter against the time budget, per offer, from the instance's records.
- [ ] Concentration: share of revenue per client, the largest flagged when above the letter's threshold.
- [ ] Discount policy written (who may grant what), and every discount granted last quarter checked against it.
- [ ] Comparison with market rates for the same format, each with its source and date, labelled as secondary.
- [ ] Currency, VAT and cross-border points sent to finance as questions, not answered here.
- [ ] Output: the updated catalogue as a recommendation; the principal decides every price.

## 4. French training compliance `[FR]` (annual; per training contract)

- [ ] The activity declaration number (numéro de déclaration d'activité) exists, or the principal has decided training is sold without one and therefore without public funding.
- [ ] BPF: the annual pedagogical and financial report is filed in its campaign; in 2026 the campaign ran from 1 April to 31 May [23]. The next year's dates confirmed each spring from Centre Inffo or the administration (to verify each year).
- [ ] BPF data captured as each training is delivered, not reconstructed in May: trainees, hours, revenue by funding source (the exact fields to verify against the year's form).
- [ ] Qualiopi: held before any training is sold with public or pooled funding (OPCO, CPF, France Travail, regions); required since 1 January 2022 [24]. Without it, the funding route is the client's own budget.
- [ ] Per contract: the funding route chosen and written; the training agreement sent to legal for review.
- [ ] The AI Act's AI-literacy duty on client organisations noted as a selling point and an obligation, with legal's source (equerry-legal's sources [37]).

## 5. New-client event (one item per team; the secretary is the DRI)

- [ ] Conflict check requested from legal against the venture map before the proposal goes out.
- [ ] Proposal and SOW pass checklist 2, background IP listed, out-of-scope written.
- [ ] Rate taken from the card; any deviation is a recommendation with its reason.
- [ ] Finance item opened: tax forms, invoicing, VAT, payment terms.
- [ ] People item opened when a subcontractor will work on it: classification, IP assignment, papers.
- [ ] The client recorded in the instance's private client list; never named in a public artefact.
- [ ] Time budget updated: the hours this client takes, and what they come out of.
