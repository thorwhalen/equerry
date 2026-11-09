# Ventures team: checklists

Five procedures the ventures lead runs and the `checker` verifies against. Each section ends with "Done when", the line the checker tests. Numbers in brackets are entries of `sources.md`. Ventures are referred to by the slug of their page; one venture's facts never appear in another's materials.

## Monthly page refresh

Per venture page (`venture-page.md` is the template), once a month and on the page's review date.

1. Read the page as it stands and note its `updated` date.
2. Read the venture's sources as linked from the page, with dated read-only commands: open obligations (`ol owed`, `ol blocked`), the corpus ledger of any session working on it (`crowsnest ledger <name>`), open issues (`gh issue list`), recent activity (`git log --since=<last update>`). Never copy their content into the page: summarise in one line and link.
3. Time actual over the last four weeks: from whatever the instance records (a time log, session ledgers, the principal's own estimate), computed with `python -c` and labelled measured or estimate.
4. **What it needs from the principal this month**: one to three items, each a concrete act with a date; nothing vague.
5. Decisions waiting on the principal: links to the desk items or issues, oldest first.
6. Next dates: from the legal and finance calendars applied to this entity, board and update dates, renewals.
7. Risks: conflicts, IP, cash, people; each one line, with the team that owns it.
8. Health: cash or revenue line from the finance pack, date of the last investor update, date of the last board.
9. Kill signals: read each one; mark `not fired`, `fired` (with the evidence) or `cannot tell` (with what is missing). A fired signal becomes an item for the principal.
10. Teams engaged: which teams have open items for this venture.
11. Fields only the principal can fill stay `owner: ?`; never guessed.
12. Return the refreshed page (full text, or a field-by-field diff) for the calling session to write, with `updated` set and `next review` set.

Done when: every field of the template has a value, `owner: ?` or "none", every claim in the page has a link or a dated command in the sources list, and every fired kill signal has an item.

## Investor update

Per venture the principal leads, monthly. A draft for the principal; the team never sends it [41].

1. Metrics: the same three to five numbers as last month, same definitions, with the change; numbers taken from the finance pack and matching it.
2. Highlights: two or three, concrete.
3. Lowlights: stated plainly, with what is being done; never omitted to make the month look better.
4. Asks: specific (an introduction to a named kind of person, a hire, advice on one question), each one sentence.
5. Short: readable in two minutes.
6. Nothing confidential about other ventures, clients or people; claims about clients go through legal.
7. Written through the host's writing-for-a-reader skill (acquaint-write) for the investor list's audience and passed through deslop.

Done when: all four parts are present, every metric matches the finance pack and its definition is unchanged (or the change is stated), and the asks are specific.

## Board pack and follow-up

Per venture where the principal sits on or reports to a board; quarterly is the common early-stage cadence [42].

**Before the meeting:**

1. Agenda with the decisions first, each with the options and a recommendation.
2. The pack: financials from the finance team, the scorecard numbers [7], risks, progress on the quarter's priorities.
3. Sent ahead by the number of days the letter or the venture's practice sets, by the principal.
4. Any item where the principal has a conflict: flagged, with the conflict map line, for counsel and the chair.

**After the meeting:**

1. Minutes or written consents drafted for company counsel (the team drafts, counsel finalises) [40].
2. Action list: each action with one owner and a date [60].
3. Cap-table effects of any resolution passed on to the position table.
4. The venture page's `last board` and decisions updated.

Done when: every decision on the agenda has options and a recommendation, every action has an owner and a date, and minutes or consents are with counsel.

## Cap-table position

Per venture, quarterly and after any issuance, grant or transfer. One table, every line pointing to its source document.

| Field | What goes in it |
|---|---|
| `holding` | shares by class, options, warrants, BSPCE, SAFEs, convertible notes |
| `terms` | for SAFEs and notes: cap, discount, post-money or pre-money [38]; for options: strike, expiry |
| `vesting` | schedule, cliff, acceleration, vested to date (computed with `python -c`) |
| `fully_diluted` | percentage, computed, with the fully diluted share count and its date |
| `qsbs` | `[US]` issue date and the dates the 50%, 75% and 100% exclusions are reached for stock issued after 4 July 2025; the $15 million cap and $75 million gross-asset test noted [14] |
| `bspce` | `[FR]` grant date, exercise window, conditions |
| `409a` | `[US]` date of the last valuation and material events since [13] |
| `restrictions` | transfer restrictions, rights of first refusal, drag and tag |
| `source` | the document each line comes from (a path or pointer in the instance) |

Done when: every holding has a source document, every percentage comes from shown code, and the 409A and QSBS or BSPCE dates are filled or marked not applicable.

## Conflict map

On any new role, a new client engagement, a venture entering a new field; yearly as a sweep. Built with the legal team's conflict check.

1. Rows: every venture page. Columns: role held (director, officer, employee, contractor, advisor, shareholder, client lead), field, customers, non-compete or exclusivity, IP assignment scope, corporate-opportunity renunciation (charter or board, where written) `[US]` Delaware [30][31].
2. For each pair of ventures that overlap in field, customers or IP: the duty or clause that decides, and `none`, `manageable with disclosure or consent`, or `blocking`.
3. Disclosures made, and to whom, with the date.
4. A director's or officer's duty question, or a `blocking` cell: escalate to company counsel.

Done when: every role on every page appears, every overlapping pair has a deciding clause or "to verify with counsel", and every `blocking` cell has an escalation.
