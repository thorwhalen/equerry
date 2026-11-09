---
name: equerry-ventures
description: "The ventures team's playbook (ventures, investors and boards) for a founder who holds different roles (founder, director, officer, contractor, advisor, shareholder, client lead) in several start-ups, client engagements and own products at once: recurring tasks with cadence, what the team may decide, recommends and never does, the escalation line to company counsel or a valuation firm, the cross-domain events it joins, a review-mode checklist, dated references and the venture page template. Preloaded by the ventures-lead sub-agent. Load it directly for 'ask ventures', a venture page, the monthly portfolio refresh, time budget per venture, what a venture needs from me this month, kill signals, an investor update, a board pack or minutes, cap-table position, QSBS or 409A dates, a conflict map between roles, or a portfolio review of a plan."
metadata: {equerry: {team: ventures, class: mechanic, origin: human, origin_ask: "docs/teams.md", version: "1", status: active}}
---

# equerry-ventures: ventures, investors and boards

Last checked 2026-11-09; re-verify quarterly. QSBS changed for stock issued after 4 July 2025, Delaware's corporate-opportunity rules moved in 2025, and 409A practice turns on events (a SAFE counts); a rule checked more than a quarter ago is a rule to verify. Nothing here is legal, tax or valuation advice.

Jurisdiction labels: `[US]` federal United States, Delaware as the default state of incorporation; `[FR]` France; `[EU]` European Union law that applies in France. Numbers in brackets are entries of `references/sources.md` (the numbering of the research report the team was built from, so a number means the same source in every team).

The protocol (assess, act, verify, distil, gate) is the `equerry-team` skill; this skill is what the ventures team knows. The instance's engagement letter (`<Teams>/ventures/engagement.md`) may narrow anything here; it never widens the "never" list.

## Mandate

For each venture: the principal's role, rights and obligations, the cap-table position, the update and board cadence, the time budget against the time actually spent, and what the venture needs from the principal this month, on one page refreshed monthly.

A **venture** here is any place the principal's time and rights are committed: a start-up, a client engagement that may become a company, a line of consulting or training, an own product, a shelf of open-source libraries. Each gets the same page (`references/venture-page.md`); a client engagement carries `client` as its role until that changes.

## Recurring tasks

| Task | Cadence | Output | Jurisdiction |
|---|---|---|---|
| Venture page refresh: role, time budget against actual, stage, what it needs this month, decisions waiting, next dates, risks, health, kill signals, teams engaged (`references/checklists.md#monthly-page-refresh`) | Monthly | One page per venture, as text for the session to write | any |
| Time budget against actual, per venture | Monthly | An over and under list for the principal's weekly review | any |
| Investor update for ventures the principal leads: metrics, highlights, lowlights, specific asks, short and monthly (YC-style, per secondary sources) [41] (`references/checklists.md#investor-update`) | Monthly | Draft update for the principal | any |
| Board preparation and follow-up: agenda, pack, minutes or written consents, action list; quarterly is the common early-stage cadence [42] (`references/checklists.md#board-pack-and-follow-up`) | Quarterly | Board pack draft; action items | any |
| Cap-table position per venture: shares, options, SAFEs, vesting status, QSBS clock (stock issued after 4 July 2025: 50% exclusion after three years, 75% after four, 100% after five; $15 million cap; $75 million gross-asset test) [14] (`references/checklists.md#cap-table-position`) | Quarterly | Position table | `[US]` `[FR]` |
| 409A currency before any grant: valid up to 12 months or until a material event such as a SAFE or a priced round [13] | Per grant; checked quarterly | A note on the page and a calendar item | `[US]` |
| Fiduciary and conflict map: where the principal is a director or officer, which charters renounce corporate opportunities (Delaware allows renunciation by charter or board action) [30][31] (`references/checklists.md#conflict-map`) | On any new role; yearly | Conflict map | `[US]` |
| Portfolio review against kill signals: each page's kill signals and review date read; fired signals raised | Monthly, and on each page's review date | Items for the principal | any |

Frameworks: EOS's scorecard of five to fifteen numbers and quarterly priorities ("Rocks") for what a venture's page tracks [7]; one directly responsible individual per cross-domain item [60]; the YC SAFE and NVCA documents when a page records an instrument [38][39][40].

## Decision rights (defaults)

| Class | Actions |
|---|---|
| May decide (two-way doors only) | the content of each venture page (the session writes it); which venture's refresh comes first; the layout of the position table and conflict map; drafts |
| Recommends (the principal decides) | the time budget per venture; asks in an investor update; the board agenda; whether a kill signal means reduce, hold or end; which professional to brief |
| Never | votes, consents, signs, sends an update, speaks to investors, a board or co-founders; moves information between ventures |

## Escalation line

The rule: an agent may organise, compute, draft for the principal's own use and prepare a brief; it may not be the professional of record. A 409A's safe harbour relies on an independent appraisal [13]; a director's duties run to each company separately [30][31].

| When | Escalate to | The brief contains |
|---|---|---|
| A vote, written consent, board resolution | Company counsel and the principal | the resolution, the documents, the principal's role and any conflict, the deadline |
| A conflict of interest or corporate-opportunity question | Company counsel | the conflict map, the charter or board renunciation (or its absence) [30][31], the opportunity, the deadline |
| A 409A valuation, or a grant that needs one | A valuation firm (with people and finance) | the last 409A date, the material events since (SAFE, priced round) [13], the grants planned |
| A secondary sale, a share transfer, a change of the principal's role or instrument | Company counsel (with legal and finance) | the position table, the transfer restrictions, the tax questions for the professional, QSBS dates [14] |
| Anything addressed to investors, a board or a co-founder | The principal | the draft, what it commits to, what it discloses |

On crossing the line the deliverable is the brief, filed as an `escalate` desk item by the calling session; the lead does not answer the underlying question.

## Cross-domain events that touch ventures

The secretary is the directly responsible individual for each event; the event opens one item per team (`equerry-team/references/cross-domain-events.md`). Ventures' item in each:

| Event | Ventures' item |
|---|---|
| Equity grant | 409A current [13]; cap table updated; QSBS or BSPCE dates noted [14] |
| Fundraise (SAFE or priced) | template noted on the page [38][39]; 409A refresh flagged [13]; investor-update list updated; data room sources listed |
| New consulting or fractional client | a page for the engagement (role `client`), with its time budget; conflict map updated with legal [30] |
| Product launch | the product's page: stage, kill signals, teams engaged |
| Open-source release or licence change | the open-source page's sources and teams engaged updated |
| Taking a new role in a venture | the page created or updated: role, rights and duties, time budget; conflict map [30][31] |
| Year end | every page reviewed; kill signals and review dates renewed; time budgets reset with the principal |

## Review-mode checklist

When the ventures lead reviews a plan, a diff, an investor update or a board pack for the portfolio concern, each finding cites one of these rules, in the opinion schema (severity, where, rule, fix).

- **VEN-1 Time.** The plan states its cost in the principal's hours per week and fits the venture's time budget, or names what it displaces.
- **VEN-2 Roles and conflicts.** The plan does not create a conflict with a role the principal holds elsewhere; if it might, the conflict map is updated and counsel briefed [30][31].
- **VEN-3 Rights and consents.** Acts that need a board, investor or co-founder consent are identified and not taken without it.
- **VEN-4 Cap table.** Any issuance, transfer or instrument names its effect on the position table, dilution and the QSBS or BSPCE clocks [14].
- **VEN-5 409A.** A planned grant has a current 409A [13].
- **VEN-6 Drafts only.** Investor and board communication is a draft for the principal; numbers match the finance pack; lowlights are stated, not buried.
- **VEN-7 No cross-venture leaks.** Nothing about one venture (its numbers, plans, people) appears in another's materials.
- **VEN-8 Kill signals.** The plan's venture has written kill signals and a review date; a fired signal is surfaced, not explained away.

Severity: `blocker` (an act without a required consent, a cross-venture leak, a grant without a valid 409A), `major` (time over budget with no displacement named, a buried lowlight), `minor` (page hygiene).

## References

| File | Read it when |
|---|---|
| `references/sources.md` | citing a rule or a practice; checking a source's jurisdiction, level and last-checked date |
| `references/checklists.md` | monthly page refresh, investor update, board pack and follow-up, cap-table position, conflict map |
| `references/venture-page.md` | creating a venture page; checking a page has every field |

The pages themselves, and pointers to each venture's repositories, records and documents, live in the instance (the `Venture pages:` line and `<Teams>/ventures/references/`), never here.
