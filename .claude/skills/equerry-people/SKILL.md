---
name: equerry-people
description: "The people team's playbook (people and contracting) for a founder who works with co-founders, contractors, advisors and occasional employees across several ventures, in the jurisdictions its engagement letter names (reference packs for the US, France and the EU ship with it), and whose own facts agents keep needing: recurring tasks with cadence, what the team may decide, recommends and never does, the escalation line to an employment lawyer or payroll provider, the cross-domain events it joins, a review-mode checklist, dated references and the rules for reading the principal's facts. Preloaded by the people-lead sub-agent. Load it directly for 'ask people', contractor classification, requalification risk, IP assignment, invention assignment, equity grant, vesting, 83(b), BSPCE, onboarding or offboarding a collaborator, a people register, the principal's bio, title or handles, what may be disclosed to whom, or a people review of a plan, an agreement or a message."
metadata: {equerry: {team: people, class: mechanic, origin: human, origin_ask: "docs/teams.md", version: "1", status: active}}
---

# equerry-people: people and contracting

Last checked 2026-11-09; re-verify quarterly. Classification, IP and equity-tax rules differ by country and changed recently (the BSPCE tax treatment for 2025, 409A practice after SAFEs); a rule checked more than a quarter ago is a rule to verify. Nothing here is employment or tax advice: the team organises, checks, drafts for the principal's own use and briefs the professional.

Jurisdiction labels: `[US]` federal United States, with the state named where state law decides (California for invention assignment); `[FR]` France; `[EU]` European Union law that applies in France. Numbers in brackets are entries of `references/sources.md`: plain numbers follow the domain research report's numbering, `D`-numbers the dossier research report's, so a number means the same source in every team. The letter's `jurisdictions:` field says which of these apply to this principal: skip the rows of any other label, and treat a jurisdiction the letter names but this skill has no pack for as missing knowledge (the coverage block says so). US, FR and EU are the packs this framework ships, not a profile of anyone.

The protocol (assess, act, verify, distil, gate) is the `equerry-team` skill; this skill is what the people team knows. The instance's engagement letter (`<Teams>/people/engagement.md`) may narrow anything here; it never widens the "never" list.

## Mandate

Every person who works with a venture or with the principal is correctly classified, papered, paid and assigned their IP, and every equity promise is written down; the principal's own facts are accurate, labelled and used only where their labels allow; text written to a person is written for that person.

## Recurring tasks

| Task | Cadence | Output | Jurisdiction |
|---|---|---|---|
| Classification check for every contractor: US common-law test (behavioural control, financial control, relationship) [26]; France: requalification risk whenever a subordination link exists (orders, monitoring, sanctions), whatever the contract is called [27] (`references/checklists.md#classification-test`) | On engagement, then every six months | Risk note per person | `[US]` `[FR]` |
| IP assignment in every agreement: US invention-assignment agreements, within state limits (California excludes inventions made on one's own time and equipment, unrelated to the employer's business) [28]; France vests employee-written software in the employer automatically, but not contractor or pre-incorporation code, which needs a written assignment [29] (`references/checklists.md#ip-assignment`) | On engagement | Signed agreement on file | `[US]` `[FR]` |
| Equity promises written down: vesting, cliff, acceleration, advisor grants; a 409A valuation current before US option grants (up to 12 months, or until a material event such as a SAFE or a priced round) [13]; BSPCE eligibility in France (company under 15 years old, among other conditions; gains' tax treatment reformed for 2025) [22] (`references/checklists.md#equity-grant`) | Per grant | Grant checklist completed | `[US]` `[FR]` |
| 83(b) election for restricted stock within 30 days of the transfer; no late election; Form 15620 is optional [11] | Per grant | Election mailed with proof of mailing | `[US]` |
| Co-founder and advisor agreements: roles, time commitment, departure terms | On creation; yearly review | Signed agreements | any |
| Onboarding and offboarding: access, accounts, equipment, tax forms, return of IP and data (`references/checklists.md#onboarding-and-offboarding`) | Per person | Checklist completed | any |
| People register: who works with the principal on what, in which venture, under which agreement and classification, with the gaps (unpapered collaborators, missing IP terms) | Quarterly | Register with a gap list | any |
| The principal's facts: fields past their `review_by` date, unconfirmed proposals, labels to confirm (`references/dossier.md`) | Monthly glance; each field on its review date | A list of fields for the principal to confirm, never a change made by the team | any |

## Decision rights (defaults)

| Class | Actions |
|---|---|
| May decide (two-way doors only) | which checklist applies; the layout of the people register; which gaps to raise first; drafts for the principal's own use |
| Recommends (the principal decides) | a classification; which agreement template; grant size bands; whether to onboard or offboard; a label for a proposed fact |
| Never | hires, fires, promises equity, sends a message to anyone, discloses a fact about the principal or another person, stores a credential, an identifier or special-category data, promotes a proposed fact to a confirmed one |

## Escalation line

The rule: an agent may organise, check, draft for the principal's own use and prepare a brief; it may not be the professional of record. Classification errors are retroactive in France: requalification applies from the first day [27].

| When | Escalate to | The brief contains |
|---|---|---|
| Hiring an employee | Employment lawyer in the country of work; payroll provider | the role, the entity, the start date, the classification note, the IP and confidentiality terms wanted |
| Terminating anyone's engagement | Employment lawyer (with a consult to legal) | the agreement, the dates, the reason as the principal states it, IP and data still to return, access to revoke |
| Reclassifying a contractor, or a high classification risk | Employment lawyer | the classification note with the evidence lines [26][27], the agreement, how long the relationship has run |
| Equity to employees; an option or BSPCE plan; a grant with unclear tax treatment | Company counsel (with consults to ventures and finance) | the cap-table position, the 409A date [13], the grant terms, the 83(b) or BSPCE question [11][22] |
| A dispute about unpaid work or IP | Counsel (with a consult to legal for the brief) | facts in date order, the agreement, the work in question, the deadline |
| A personal-data breach about a collaborator, a candidate or the principal (a record or message sent to the wrong person, a leaked file) | **The principal at once, directly** (the calling session interrupts; a desk item alone is too slow), with a consult to legal, which owns the breach brief and the regulator's deadline (72 hours under GDPR Article 33 `[EU]`) | what leaked, when it was found, whose data, to whom, which countries; never the leaked values themselves |
| Storing or disclosing a value above `green` to an audience not cleared for it | The principal | the field (never its value), the purpose, the audience, why it seems needed |

On crossing the line the deliverable is the brief, filed as an `escalate` desk item by the calling session; the lead does not answer the underlying question.

## Tools the team leans on

The public acquaint package holds records of people, including the principal's own; correspond knows who can read a conversation. The lead uses their read verbs only: `acquaint who`, `resolve`, `brief`, `reach`, `disclosure`, `check`, `style-lint`; `correspond audience`. Writing to a person goes through the acquaint-write skill (and its `recipient-reader` sub-agent for a red-team pass) and deslop; ductus can gauge a draft. Recording an observation (`acquaint remember`) is the calling session's act, after the principal's say-so where the dossier rules require it.

## Cross-domain events that touch people

The secretary is the directly responsible individual for each event, which opens one item per team. People's item in each event is the lines tagged `[people]` in `equerry-team/references/cross-domain-events.md`, the only copy of those checklists (source numbers there are the same as in this skill's `references/sources.md`).

## Review-mode checklist

When the people lead reviews a plan, a diff, an agreement or a message for the people concern, each finding cites one of these rules, in the opinion schema (severity, where, rule, fix).

- **PPL-1 Classification.** Anyone who will work under the plan has a classification note for each country of work [26][27].
- **PPL-2 IP before work.** An IP assignment (with a background-IP schedule) is signed before work starts, or the plan says when it will be [28][29].
- **PPL-3 Equity in writing.** Every equity promise has written terms: amount, instrument, vesting, cliff, acceleration.
- **PPL-4 Equity clocks.** A grant of restricted stock has its 83(b) date; a grant in France names the BSPCE conditions; an option grant has a current 409A [11][13][22].
- **PPL-5 Access.** Access is granted least-privilege, listed, and the offboarding plan revokes the same list.
- **PPL-6 Written for the reader.** A message to a person is drafted with that person's record, passed through deslop, and checked with `acquaint check`.
- **PPL-7 Labels respected.** A fact about the principal is used only where its label allows; nothing `amber` or `red` reaches an audience not cleared for it [D58].
- **PPL-8 Never stored.** No credential, identifier or special-category value is stored, logged or pasted into an issue, a memory or a message [D15].
- **PPL-9 Proposals, not facts.** A new fact about a person enters as a sourced observation; only the principal confirms a fact about themselves.

Severity: `blocker` (an unassigned asset about to ship or be sold, a no-cure date, a disclosure above a label), `major` (a classification risk, an unwritten promise), `minor` (register hygiene).

## References

| File | Read it when |
|---|---|
| `references/evals.json` | running G4 or G6 for this team: the seed eval set (three golden asks with the expected verdict and escalation decision), the team's no-regression baseline |
| `references/sources.md` | citing a rule; checking a source's jurisdiction, level and last-checked date |
| `references/checklists.md` | classification test, IP assignment, equity grant, onboarding and offboarding |
| `references/dossier.md` | reading, using or proposing a fact about the principal; deciding what may be disclosed to whom |

Private pointers (the agreements themselves, the people register, the principal's facts file) live in the instance at `<Teams>/people/references/` and in the `Dossier:` line, never here.
