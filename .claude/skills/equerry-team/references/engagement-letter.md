# The engagement letter

The contract between the principal and one team, modelled on a fractional executive's engagement: a mandate, deliverables with a cadence, written decision rights, an out-of-scope list and the line where a licensed professional takes over. One per team, in the instance at `<Teams>/<team>/engagement.md`. **A team without a letter does not run**, and a letter past its `review_by` date runs only until the principal renews it (the monthly team review flags it).

Two failure modes the letter exists to prevent: a lead with accountability but no decision rights, and a lead with a "fix everything" scope. Both fail for human fractional executives and for agents alike.

## Format

Frontmatter (machine-read by the protocol), then a short body (read by the lead).

| Field | Required | Meaning |
|---|---|---|
| `team` | yes | the team id: `legal`, `finance`, `people`, `bizdev`, `marketing`, `product`, `engineering`, `ventures` |
| `lead` | yes | the lead sub-agent's name, normally `<team>-lead` |
| `status` | yes | `active` (runs), `paused` (asks are refused with a one-line reason), `proposed` (written, not yet signed: does not run) |
| `review_by` | yes | the date by which the principal re-reads and renews the letter; at most twelve months out, three to six is normal at the start |
| `growth_budget` | yes | per calendar month, `{train: <n>, hire: <n>}`; G0 refuses growth beyond it. Default `{train: 3, hire: 1}` |
| `permissions_ceiling` | yes | the tool names no primitive of this team may exceed; G3 and G5 compare every proposal against it. Raising it is itself a G5 item |
| `signed` | no | the date the principal approved the letter, and how (`desk item <url>` or `in person`) |

Body sections, each one paragraph, list or table:

| Section | What goes in it |
|---|---|
| Mandate | one line: what the team exists to keep true |
| In scope | the principal, and which ventures by venture-page name |
| Deliverables and cadence | a table: deliverable, cadence (weekly only if a tolerance breaks, monthly pack, quarterly plan, per event), output form, delivered to (brief, desk, venture page, published page) |
| May decide | two-way doors only (see the rule below) |
| Recommends | everything the lead may advise on; the principal decides |
| Never | actions the lead may not take or prepare as final: signs, files, sends, pays, publishes, promises, represents the principal before an authority |
| Escalation line | `<when> -> <which licensed professional, named or "to be named"> -> the brief (facts, question, documents, deadline)` |
| Out of scope | what the team does not do, even if asked; where it goes instead |
| Leans on | fleet tools and other teams it uses (by public name or the navigator's answer) |
| References | the framework's domain skill (`equerry-<team>`) and the instance's private references folder |
| First task | one ask with its acceptance line, so the letter is tested on day one |

## The may-decide rule

A lead may decide alone only an action the architecture's gate already lets through: **reversible by rule** (no spend, deletion, message to a person, public publication, merge outside the principal's own organisations, secret or account change) **and** low disagreement risk. In practice that leaves: choosing which sources to read, ordering its own work, choosing a checklist, drafting for the principal's own use, filing a desk item, proposing a primitive. Anything else goes under Recommends.

Only two teams hold an **Agree** right (a veto the principal must resolve, never a decision): `legal` on privacy-legal one-way doors and `finance` on spend. Write each such right as a named class ("Agree: any new contract that assigns IP", "Agree: any recurring spend above <amount> a month"), never as a general power.

A letter may narrow the domain skill's default may-decide list; it may never widen it past the gate.

## Worked example (fictional)

A fictional principal, Ada, a solo founder with one consultancy and one software product sold to schools.

```markdown
---
team: finance
lead: finance-lead
status: active
review_by: 2027-03-01
growth_budget: {train: 3, hire: 1}
permissions_ceiling: [Read, Grep, Glob, WebSearch, WebFetch, Bash]
signed: 2026-11-12, desk item (Ada's tracker)
---
# Finance, tax and administration: engagement letter

Mandate: Ada knows her cash, income and tax exposure for the consultancy and for herself every month, and every offer is priced by a script, never by mental arithmetic.

In scope: Ada personally; the consultancy (venture page `consultancy`); the schools product (venture page `schools-app`).

Deliverables and cadence:

| Deliverable | Cadence | Output | Delivered to |
|---|---|---|---|
| Monthly pack: cash, receivables, burn, runway, tax set-asides | monthly, by the 5th | one page | the brief |
| Offer pricing | per offer | the script, its inputs and its output | the desk, with bizdev |
| Admin estate sweep: subscriptions, domains, store accounts | quarterly | a table with renewal dates and costs | the brief |
| Statutory dates for the next 90 days | monthly | rows from the calendar reference | the brief |

May decide: which bank exports to reconcile first; the layout of the pack; filing a question to the principal on the desk.

Recommends: prices, set-aside rates, which subscriptions to cancel, when to invoice, which entity bills a client.

Agree: any new recurring spend above 100 a month.

Never: pays, files a return, signs, moves money, contacts the accountant or a tax authority, changes an account.

Escalation line: a tax return, a tax position, an R&D credit claim, payroll set-up, cross-border personal tax -> the chartered accountant ("to be named"; finding one is task 2) -> the brief (facts, question, documents, deadline).

Out of scope: bookkeeping entries themselves (the accountant's software); fundraising (ventures team); contract terms (legal team).

Leans on: a spreadsheet tool for the pack; the legal team for invoices that carry contract terms; the bizdev team for offer scope.

References: the framework's `equerry-finance` skill; `teams/finance/references/` for Ada's own documents.

First task: price the two-day workshop offer with a script whose inputs are visible. Accepted when the checker passes it against the offer-pricing checklist and Ada sets the price.
```

What makes the example sound: every Never is a one-way door; the Agree right is a named class with a threshold; the escalation line names a profession and admits it is not yet a person; the first task has an acceptance line a checker can test.
