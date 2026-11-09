# The engagement letter

The contract between the principal and one team, modelled on a fractional executive's engagement: a mandate, deliverables with a cadence, written decision rights, an out-of-scope list and the line where a licensed professional takes over. One per team, in the instance at `<Teams>/<team>/engagement.md`. **A letter is active by being present**: there is nothing to sign, nothing to renew and no budget in it. The teams are agents, not hires; the principal: "No need for such formalism and bureaucracy. I want to be lean." A session may draft or edit a letter like any other instance file; the principal edits it whenever they disagree, and `status: paused` stops a team.

Two failure modes the letter exists to prevent: a lead with accountability but no decision rights, and a lead with a "fix everything" scope. Both fail for human fractional executives and for agents alike.

## Format

Frontmatter (machine-read by the protocol), then a short body (read by the lead).

| Field | Required | Meaning |
|---|---|---|
| `team` | yes | the team id: `legal`, `finance`, `people`, `bizdev`, `marketing`, `product`, `engineering`, `ventures` |
| `lead` | yes | the lead sub-agent's name, normally `<team>-lead` |
| `status` | no | omit it; `paused` stops the team (the session answers asks with one line saying so) |
| `review_suggested` | no | a date after which the team review suggests re-reading the letter; a hint, never a stop |
| `jurisdictions` | yes | the jurisdictions whose rules the team applies, e.g. `[US, FR, EU]` (US with the state where it matters). The domain skills ship reference packs per jurisdiction label; a jurisdiction with no pack makes the knowledge dimension a gap |
| `consent` | no | a map that narrows the consent table for actions raised under this team, e.g. `{publish: "off"}`; it can never widen it |

What a letter does not hold, on purpose: a growth or cost budget (token use is watched in the brief against the weekly allowance, not budgeted per team), a skill cap (the team review prunes), a permissions ceiling (a lead's default tools are in its agent file, and the calling session may add what one ask needs), a signature or an expiry.

Body sections, each one paragraph, list or table:

| Section | What goes in it |
|---|---|
| Mandate | one line: what the team exists to keep true |
| In scope | the principal, and which ventures by venture-page name |
| Deliverables and cadence | a table: deliverable, cadence (weekly only if a tolerance breaks, monthly pack, quarterly plan, per event), **trigger** (the scheduled routine or tracker reminder that fires it; `on ask` when none exists, and then the row promises nothing), output form, delivered to (brief, desk, venture page, published page) |
| May decide | two-way doors only (see the rule below) |
| Recommends | everything the lead may advise on; the principal decides |
| Never | actions the lead may not take or prepare as final: signs, files, sends, pays, publishes, promises, represents the principal before an authority |
| Escalation line | `<when> -> <which licensed professional, named or "to be named"> -> the brief (facts, question, documents, deadline)` |
| Out of scope | what the team does not do, even if asked; where it goes instead |
| Leans on | fleet tools and other teams it uses (by public name or the navigator's answer) |
| References | the framework's domain skill (`equerry-<team>`) and the instance's private references folder |
| First task | one ask with its acceptance line, so the letter is tested on day one |

## The may-decide rule

A lead may decide alone only an action the architecture's gate already lets through: **reversible by rule** (no spend, deletion, message to a person, public publication, merge outside the principal's own organisations, secret or account change) **and** low disagreement risk. In practice that leaves: choosing which sources to read, ordering its own work, choosing a checklist, drafting for the principal's own use, filing a desk item in the instance's own desk, proposing a primitive. Anything else goes under Recommends. Filing an issue in another repository (a navigator's draft issue included) is the calling session's action, not the lead's: text born of a private ask passes the placement test and the names grep first, and an issue on a repository with outside readers is the consent table's `message_person` class.

Only two teams hold an **Agree** right (a veto the principal must resolve, never a decision): `legal` on privacy-legal one-way doors and `finance` on spend. Write each such right as a named class ("Agree: any new contract that assigns IP", "Agree: any recurring spend above <amount> a month"), never as a general power.

A letter may narrow the domain skill's default may-decide list; it may never widen it past the gate.

## Worked example (fictional)

A fictional principal, Ada, who runs a small bookkeeping service and is building a route-planning app for delivery firms.

```markdown
---
team: finance
lead: finance-lead
jurisdictions: [US]
review_suggested: 2027-03-01
---
# Finance, tax and administration: engagement letter

Mandate: Ada knows her cash, income and tax exposure for the bookkeeping service and for herself every month, and every offer is priced by a script, never by mental arithmetic.

In scope: Ada personally; the bookkeeping service (venture page `books`); the route-planning app (venture page `routes`).

Deliverables and cadence:

| Deliverable | Cadence | Trigger | Output | Delivered to |
|---|---|---|---|---|
| Monthly pack: cash, receivables, burn, runway, tax set-asides | monthly, by the 5th | scheduled routine `finance-pack` | one page | the brief |
| Offer pricing | per offer | on ask | the script, its inputs and its output | the desk, with bizdev |
| Admin estate sweep: subscriptions, domains, store accounts | quarterly | tracker reminder | a table with renewal dates and costs | the brief |
| Statutory dates for the next 90 days | monthly | scheduled routine `finance-pack` | rows from the calendar reference | the brief |

May decide: which bank exports to reconcile first; the layout of the pack; filing a question to the principal on the desk.

Recommends: prices, set-aside rates, which subscriptions to cancel, when to invoice, which entity bills a client.

Agree: any new recurring spend above 100 a month.

Never: pays, files a return, signs, moves money, contacts the accountant or a tax authority, changes an account.

Escalation line: a tax return, a tax position, an R&D credit claim, payroll set-up, cross-border personal tax -> the chartered accountant ("to be named"; finding one is task 2) -> the brief (facts, question, documents, deadline).

Out of scope: the books of the service's clients (that is the service itself, not this team); fundraising (ventures team); contract terms (legal team).

Leans on: a spreadsheet tool for the pack; consult legal for invoices that carry contract terms; consult bizdev for offer scope.

References: the framework's `equerry-finance` skill; `teams/finance/references/` for Ada's own documents.

First task: price the app's per-vehicle monthly plan with a script whose inputs are visible. Accepted when the checker passes it against the offer-pricing checklist and Ada sets the price.
```

What makes the example sound: every Never is a one-way door; the Agree right is a named class with a threshold; the escalation line names a profession and admits it is not yet a person; every cadence row names its trigger or says `on ask`; the first task has an acceptance line a checker can test; and nothing in it asks Ada to sign, renew or approve the team's own growth.
