# Marketing checklists

Each checklist is something the `checker` sub-agent can verify a deliverable against: every item is a yes or no with the evidence that makes it yes. Numbers in brackets point to `sources.md`. Values marked "default" are the framework's; an engagement letter may set its own. Last checked 2026-11-09.

## 1. Launch checklist (per product or offer release)

Opened by the secretary as the marketing item of a launch; the product, legal, engineering and finance items run beside it.

Before:

- [ ] The positioning page for this product or offer is filled and dated within the last quarter (`positioning.md`) [45].
- [ ] The one sentence that says what it is, for whom, and why it beats the alternative, agreed by the principal.
- [ ] The audience for this launch named (which best-fit customers, reached where), and the channels chosen to match.
- [ ] Every claim in the launch material has evidence; comparative, client, performance, AI and children's claims cleared by legal.
- [ ] Customer evidence used only with permission recorded (checklist 2).
- [ ] The landing page, README or store listing reviewed against the review-mode checklist in `equerry-marketing`.
- [ ] Every text drafted for the principal, through acquaint-write when the reader is known, through deslop always, checked with ductus.
- [ ] The scorecard rows this launch should move named, with their value before the launch.
- [ ] Personal data collected by the launch (sign-ups, analytics) cleared by legal before it goes live.
- [ ] The date fits the principal's calendar and the engineering release; nothing announced before it ships.

On the day (the principal publishes; the team prepares):

- [ ] The announcement, the release notes and the short versions for each channel, ready as drafts.
- [ ] Where questions will come in, and who answers them in the first week.

After (two weeks, default):

- [ ] The scorecard rows read again; what moved, what did not, labelled measured or interpretation.
- [ ] New customer evidence logged with permission.
- [ ] One line for the monthly review: keep, change or drop this channel for the next launch.

## 2. Customer-evidence log (monthly)

One row per piece of evidence, in the instance's private log, never in a public repository.

- [ ] **What**: the quote, the case note or the usage fact, verbatim where it is a quote.
- [ ] **Who** by role and organisation type in any shared copy; the name only in the private log.
- [ ] **Source**: where it was said or measured, with the date.
- [ ] **Permission**: who granted it, when, in what form (a written message is the minimum), and for which uses (website, talk, proposal, named or anonymous).
- [ ] **Expiry or review date** for the permission (default: one year).
- [ ] **Used in**: every place it appears, so a withdrawn permission can be honoured everywhere.
- [ ] **Legal check** recorded for any use that names the client publicly.

## 3. Monthly scorecard

A few numbers, chosen to match the pipeline, not a dashboard [7].

- [ ] At most five rows (default), each with an owner, a target and the value for the last four weeks.
- [ ] At least one row ties to the pipeline (qualified conversations started by marketing, or proposals that cite a piece of content).
- [ ] Each value labelled measured (with its source) or estimate; derived values computed with `python -c`, code shown.
- [ ] The publishing rhythm against plan: what was published, what slipped, why.
- [ ] What got traction, with the evidence; what to stop.
- [ ] One recommendation for next month, at most; the principal decides.
- [ ] Any row off its target two months running becomes an item for the principal; nothing else does.
