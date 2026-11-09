# The venture page

One page per venture, in the instance folder the host's `Venture pages:` line names, refreshed monthly by the ventures team (`checklists.md#monthly-page-refresh`). The template merges section H of `docs/research/domain-experts-for-a-solo-technical-founder.md` with four additions: what the venture needs from the principal this month, kill signals with a review date, the teams engaged, and the sources the page is built from.

A page **links to a venture's own records; it never copies them**. It holds the principal's view of their position in the venture, not the venture's internal documents. Fields only the principal can fill are written `owner: ?` until they do; an agent never guesses them. Write "none" for a field that genuinely has nothing, so an empty field always means "not yet looked at".

A "venture" is any commitment of the principal's time and rights: a start-up, a client engagement that may become a company (role `client` until that changes), a line of consulting or training, an own product, a shelf of open-source libraries.

## Template

Copy into `<Venture pages>/<slug>.md`. The slug is short, lowercase, hyphenated, and the same id every team uses for this venture.

```markdown
---
slug: <slug>
updated: <YYYY-MM-DD>
next_review: <YYYY-MM-DD>
---
# <Venture code name>

**Role:** <founder | co-founder | director | officer | employee | contractor | advisor | shareholder | client lead>, since <date>
**Entity:** <form: C-corp, SAS, SASU, LLC, sole proprietorship, none>, <jurisdiction: [US] state | [FR] | other>, fiscal year end <month>
**Stage:** <idea | pre-seed | seed | A | revenue | client engagement | wind-down>

## Time
- Budget: <hours/week agreed with the principal, or owner: ?>
- Actual, last 4 weeks: <hours/week> (<measured from ... | estimate, basis ...>)

## Ownership
- Holding: <% fully diluted>, <instrument: shares, options, SAFE, BSPCE, none>
- Vesting: <schedule, vested to date, cliff, acceleration>
- Clocks: <[US] QSBS dates | [FR] BSPCE window | none>; last 409A <date or n/a>

## Rights and duties
- Board seat: <yes, as ... | observer | no>
- Information rights: <yes | no>
- Non-compete or exclusivity: <scope and end, or none>
- IP assignment: <what the principal assigned, to which entity, under which agreement>
- Corporate-opportunity renunciation: <[US] where written, or none, or n/a>

## This month it needs from me
1. <a concrete act, with a date>
2. <optional>
3. <optional>

## Decisions waiting on me
- <link to a desk item or issue, oldest first, or none>

## Next dates
- <date>: <filing, board, update, renewal, review> (<team>)

## Risks
- <conflict | IP | cash | people | regulatory>: <one line> (<owning team>)

## Health
- Cash or revenue: <one line from the finance pack, with its date>
- Last investor update sent: <date or n/a>
- Last board: <date or n/a>

## Kill signals
Review date: <YYYY-MM-DD>
- <a signal stated so that anyone can tell whether it fired, e.g. "fewer than N paying users by <date>", "time actual above budget three months running">: <not fired | fired: evidence | cannot tell: missing what>
- What I would do if one fires: <reduce | hold | end my role; owner: ?>

## Teams engaged
- <team>: <the open item, linked>

## Sources
- <link to the venture's repository, hub, ledger, issue tracker, cap-table record, agreements folder: pointers only>
```

## Field rules

| Field | Rule |
|---|---|
| `updated`, `next_review` | set on every refresh; `next_review` is at most a month away, or the kill-signal review date if sooner |
| Role, Entity | from the role documents and registration; `owner: ?` until a document is linked |
| Time budget | the principal's decision; the ventures team recommends, never sets it |
| Time actual | computed, labelled measured or estimate, with its basis |
| Ownership, Clocks | from the cap-table position table (`checklists.md#cap-table-position`); every figure from shown code |
| Rights and duties | from the signed documents; "not addressed" when a document is silent; a duty question goes to counsel |
| This month it needs from me | one to three concrete acts with dates; if nothing, write "nothing this month" |
| Kill signals | written before they are needed, checkable by someone who is not the principal; each refresh marks each one |
| Teams engaged | only teams with an open item for this venture |
| Sources | links and pointers; never pasted content |

## What never goes on a page

- Content copied from another venture, or anything that lets one venture's reader learn another's facts.
- Credentials, account numbers, tax identifiers, personal data of collaborators beyond their record id.
- A client's confidential information beyond what the engagement letter allows the principal to keep.
