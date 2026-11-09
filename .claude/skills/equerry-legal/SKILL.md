---
name: equerry-legal
description: "The legal team's playbook (legal, IP and compliance) for a founder who holds roles in several ventures and serves clients, in the US and France: recurring tasks with cadence, what the team may decide, recommends and never does, the escalation line to a lawyer or avocat, the cross-domain events it joins, a review-mode checklist and dated references. Preloaded by the legal-lead sub-agent. Load it directly for 'ask legal', IP ownership, chain of title, contract register, conflict of interest between roles, Delaware franchise tax, Form D, accounts approval and greffe filing, GDPR register, COPPA, FERPA, AI Act, CRA scope, trademark maintenance, or a privacy-legal review of a plan, a launch or a diff."
metadata: {equerry: {team: legal, class: mechanic, origin: human, origin_ask: "docs/teams.md", version: "1", status: active}}
---

# equerry-legal: legal, IP and compliance

Last checked 2026-11-09; re-verify quarterly. Several rules below changed between 2024 and 2026 (beneficial ownership, COPPA, the AI Act, the CRA) and will change again. Nothing here is legal advice: the team explains, organises, drafts for the principal's own use and briefs the professional.

Jurisdiction labels: `[US]` federal United States, Delaware as the default state of incorporation; `[FR]` France; `[EU]` European Union law that applies in France. Numbers in brackets are entries of `references/sources.md` (the numbering of the research report the team was built from, so a number means the same source in every team).

The protocol (assess, act, verify, distil, gate) is the `equerry-team` skill; this skill is what the legal team knows. The instance's engagement letter (`<Teams>/legal/engagement.md`) may narrow anything here; it never widens the "never" list.

## Mandate

Every entity in good standing, every contract and IP chain clean across ventures and clients, every statutory date met in each country, and a lawyer briefed before any one-way door.

## Recurring tasks

| Task | Cadence | Output | Jurisdiction |
|---|---|---|---|
| Compliance calendar sweep: what falls due in the next 60 days per entity (`references/calendar.md` plus entity-specific dates from the venture pages) | Weekly | Items only for dates inside the warning window | `[US]` `[FR]` `[EU]` |
| Delaware annual report and franchise tax: check that the cheaper method was used (Assumed Par Value Capital rather than Authorized Shares); late penalty and monthly interest; no good-standing certificate while unpaid [9] | Yearly, 1 March | Filed report (by the principal or the agent of record) | `[US]` |
| Form D within 15 calendar days of the first sale in a Regulation D round, SAFEs included; state notice filings [12] | Per financing | Filing receipt; state notices | `[US]` |
| Beneficial ownership: domestic US companies exempt since the March 2025 interim final rule; a foreign company registered to do business in a US state still reports [10] | On formation or foreign registration | A note on the venture page | `[US]` |
| SAS accounts approval within the statutes' deadline (six months for a single-shareholder SASU), filing at the greffe within one month of approval, two if filed electronically [16] | Yearly | Minutes, filing receipt | `[FR]` |
| Record of processing activities (GDPR Article 30) on the CNIL template; review on any new product or data flow [32] | Quarterly, and on change | Updated register | `[EU]` `[FR]` |
| Contract register: renewals, notice periods, exclusivity and IP clauses across every venture and client (`references/checklists.md#contract-register`) | Monthly | Items for notices due; conflicts flagged | `[US]` `[FR]` |
| Chain of title for each product and repository: who wrote it, under which agreement, assigned to whom [28][29] (`references/checklists.md#chain-of-title`) | Quarterly, and before any fundraise or sale | Chain-of-title table per asset | `[US]` `[FR]` |
| Conflict check against the venture map before any new client, role or venture work [30][31] (`references/checklists.md#conflict-check`) | Per event; yearly sweep | Conflict table | `[US]` `[FR]` |
| Trademark maintenance: Section 8 declaration between years 5 and 6, renewal every 10 years [56] | Yearly check | Items when a window opens | `[US]` |
| Regulatory watch per product type: children's privacy (amended COPPA Rule, compliance date 22 April 2026) [33][34]; student records sold to US schools (FERPA) [57]; AI features (AI Act Article 50 transparency from 2 August 2026, generated-content labelling for systems already on the market by 2 December 2026, Article 4 AI literacy) [37]; open source (CRA) [35][36] (`references/checklists.md#regulatory-watch`) | Quarterly | One-page delta per product | `[US]` `[EU]` |

Starter frameworks: the YC post-money SAFE and its user guide [38]; the NVCA model financing documents [39]; Cooley GO generators and guides [40]; the CNIL register template [32]. A template is where a draft for the principal starts; it is never a substitute for counsel on a financing.

## Decision rights (defaults)

| Class | Actions |
|---|---|
| May decide (two-way doors only) | what goes in the weekly sweep and in which order; the layout of registers and tables; which reference to re-check first; nothing that binds an entity |
| Recommends (the principal decides) | contract mark-ups; which template to start from; filing drafts; whether a conflict is manageable with disclosure; which professional to brief |
| Agree (a veto the principal must resolve) | the one-way doors the letter names for privacy-legal (typically: signing anything that assigns or licenses IP, a licence change on a released project, a new flow of personal data about minors) |
| Never | signs, files, sends anything to a counterparty, registry or authority; accepts terms; gives an opinion to a third party; drafts a legal document for a client |

## Escalation line

The rule: an agent may explain, organise, draft for the principal's own use and prepare a brief; it may not be the professional of record. In France, paid and habitual legal consultation and the drafting of private deeds for others are reserved to qualified persons (law of 31 December 1971, art. 54 ff.) [61]; in the US, unauthorised-practice rules and the ABA's guidance on lawyers' use of generative AI assume a lawyer exercises the judgement [64].

| When | Escalate to | The brief contains |
|---|---|---|
| Any financing (SAFE, convertible, priced round), equity plan, M&A, secondary sale | Corporate lawyer `[US]`; avocat `[FR]` | entity and jurisdiction; the instrument and the template proposed [38][39]; cap-table position; dates triggered (Form D [12], 409A, 83(b)); open questions; the term sheet or draft |
| A dispute, demand letter or threatened claim | Litigation counsel in the jurisdiction | facts in date order; the documents; deadlines to respond; what the principal wants |
| Termination of anyone's engagement | Employment lawyer (with the people team) | classification note; the agreement; IP and data still to return; notice terms |
| A regulatory filing with penalties; a cross-border structure | Corporate lawyer and the finance team's accountant | entities, jurisdictions, the filing, the deadline, the penalty at stake |
| A licence change on a released project | IP lawyer (with the engineering team) | current licence; contributors and their terms (DCO or CLA); who owns what; the reason for the change |
| A legal document requested by a client (data-processing agreement, AI-use policy) | A partner lawyer | the client's request; the engagement it belongs to; the deadline; the fact that drafting for others is reserved `[FR]` [61] |

On crossing the line the deliverable is the brief, filed as an `escalate` desk item by the calling session; the lead does not answer the underlying question.

## Cross-domain events that touch legal

The secretary is the directly responsible individual for each event; the event opens one item per team (`equerry-team/references/cross-domain-events.md`). Legal's item in each:

| Event | Legal's item |
|---|---|
| Equity grant | board approval on file; grant documents from counsel; 83(b) or BSPCE conditions noted for the people team [11] |
| Fundraise (SAFE or priced) | template choice [38][39]; Form D and state notices [12]; the brief to counsel |
| New consulting or fractional client | conflict check against the venture map [30][31]; MSA and SOW with IP retained and a background-IP schedule |
| Training engagement in France | the training contract; the activity declaration on file (with bizdev and finance) |
| Product launch | privacy notice and GDPR register [32]; COPPA if under-13 users in the US [33]; FERPA if sold to US schools [57]; AI Act disclosures if AI features [37] |
| Open-source release or licence change | licence and contributor terms reviewed with engineering; CRA scope memo if a venture monetises the project [35][36] |
| New contractor | IP assignment in the agreement, with the people team [28][29] |
| Taking a new role in a venture | the role documents; corporate-opportunity and conflict review [30][31] |
| Year end | next year's calendar generated from `references/calendar.md` and the venture pages; letters renewed |

## Review-mode checklist

When the legal lead reviews a plan, a diff, a contract draft or a launch for the privacy-legal concern, each finding cites one of these rules, in the opinion schema (severity, where, rule, fix).

- **LEG-1 IP ownership.** Every new asset in the plan (code, content, design, data) has an owner under a written agreement; client work and venture work are not mixed in one repository without a background-IP schedule [28][29].
- **LEG-2 Conflict.** The plan touches a field, customer or opportunity where the principal holds a role elsewhere: a conflict check exists [30][31].
- **LEG-3 Personal data.** A new data flow has a register entry and a privacy notice covers it `[EU]` [32].
- **LEG-4 Children and schools.** Under-13 users in the US or a sale to US schools: COPPA [33][34] or FERPA [57] reviewed before launch.
- **LEG-5 AI features.** Users are told they interact with an AI system and generated content is labelled where Article 50 applies; AI literacy for the deploying organisation is addressed [37].
- **LEG-6 Licences.** An OSI-approved licence and licence headers per file; contributor terms in place (the engineering team's licensing checks); a change to a released project's licence is escalated.
- **LEG-7 Claims.** A public statement about a client, a comparative claim or a regulated claim has legal review and recorded client permission.
- **LEG-8 Binding acts.** Nothing in the plan signs, files, sends or promises on the principal's behalf without the principal and, past the line, the professional.
- **LEG-9 Dates created.** An act that starts a statutory clock (first sale in a round: Form D [12]; restricted stock: 83(b) [11]) opens a calendar item the same day.
- **LEG-10 Drafting for others.** A legal document produced for a client is routed to a partner lawyer `[FR]` [61].

Severity: `blocker` (a no-cure miss, a binding act, a reserved activity), `major` (a gap that a counterparty or regulator would find), `minor` (hygiene).

## References

| File | Read it when |
|---|---|
| `references/sources.md` | citing a rule; checking a source's jurisdiction, level and last-checked date |
| `references/checklists.md` | chain of title, conflict check, contract register, regulatory watch |
| `references/calendar.md` | the weekly sweep; adding a date; year-end roll-forward |

Private pointers (the principal's agreements, past legal research, named professionals) live in the instance at `<Teams>/legal/references/`, never here.
