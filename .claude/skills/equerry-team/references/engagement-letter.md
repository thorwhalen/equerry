# The engagement letter

The contract between the principal and one team, modelled on a fractional executive's engagement: a mandate, deliverables with a cadence, written decision rights, an out-of-scope list and the line where a licensed professional takes over. One per team, in the instance at `<Teams>/<team>/engagement.md`. **A team runs only under a letter that is `status: active`, carries a `signed` date and has a `review_by` date in the future**: the calling session checks all three before every ask (`equerry-team`, "Before step 1") and refuses the ask otherwise. A letter an agent drafted is `proposed` until the principal signs it.

Two failure modes the letter exists to prevent: a lead with accountability but no decision rights, and a lead with a "fix everything" scope. Both fail for human fractional executives and for agents alike.

## Format

Frontmatter (machine-read by the protocol), then a short body (read by the lead).

| Field | Required | Meaning |
|---|---|---|
| `team` | yes | the team id: `legal`, `finance`, `people`, `bizdev`, `marketing`, `product`, `engineering`, `ventures` |
| `lead` | yes | the lead sub-agent's name, normally `<team>-lead` |
| `status` | yes | `active` (runs, once signed), `paused` (asks are refused with a one-line reason), `proposed` (written, not yet signed: does not run) |
| `review_by` | yes | the date by which the principal re-reads and renews the letter; at most twelve months out, three to six is normal at the start. Past it, the team does not run |
| `signed` | yes | `null` until the principal approves the letter; then the date and how (`desk item <url>` or `in person`). Only the principal sets it |
| `jurisdictions` | yes | the jurisdictions whose rules the team applies, e.g. `[US, FR, EU]` (US with the state where it matters). The domain skills ship reference packs per jurisdiction label; a jurisdiction with no pack makes the knowledge dimension a gap |
| `growth_budget` | yes | per calendar month, `{train: <n>, hire: <n>}`; G0 refuses growth beyond it. Default `{train: 3, hire: 1}` |
| `cost_budget` | yes | per calendar month, `{agent_calls: <n>, tokens: <n or null>}`: every agent call the team's asks and growth spend (leads, checker, contradictor, navigator, G4 runs). G0 refuses growth past it. Default `{agent_calls: 60, tokens: null}` |
| `skill_cap` | yes | the most active skills the team's roster may hold, the domain skill included; G1 turns a proposal past it into a train or a merge. Default `3` |
| `permissions_ceiling` | yes | the tools no primitive of this team may exceed, with `Bash` written only as command patterns (`"Bash(python3 -c:*)"`, `"Bash(rg:*)"`), never bare, so a proposal can actually exceed it; G3 and G5 compare every proposal against it. Raising it is itself a G5 item. Default `[Read, Grep, Glob, WebSearch, WebFetch]` |

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

A lead may decide alone only an action the architecture's gate already lets through: **reversible by rule** (no spend, deletion, message to a person, public publication, merge outside the principal's own organisations, secret or account change) **and** low disagreement risk. In practice that leaves: choosing which sources to read, ordering its own work, choosing a checklist, drafting for the principal's own use, filing a desk item in the instance's own desk, proposing a primitive. Anything else goes under Recommends, including filing an issue in any other repository (a navigator's draft issue included): text born of a private ask needs the placement test and the principal's word before it leaves the instance.

Only two teams hold an **Agree** right (a veto the principal must resolve, never a decision): `legal` on privacy-legal one-way doors and `finance` on spend. Write each such right as a named class ("Agree: any new contract that assigns IP", "Agree: any recurring spend above <amount> a month"), never as a general power.

A letter may narrow the domain skill's default may-decide list; it may never widen it past the gate.

## Worked example (fictional)

A fictional principal, Ada, who runs a small bookkeeping service and is building a route-planning app for delivery firms.

```markdown
---
team: finance
lead: finance-lead
status: active
review_by: 2027-03-01
signed: 2026-11-12, desk item (Ada's tracker)
jurisdictions: [US]
growth_budget: {train: 3, hire: 1}
cost_budget: {agent_calls: 60, tokens: null}
skill_cap: 3
permissions_ceiling: [Read, Grep, Glob, WebSearch, WebFetch, "Bash(python3 -c:*)"]
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

What makes the example sound: every Never is a one-way door; the Agree right is a named class with a threshold; the escalation line names a profession and admits it is not yet a person; every cadence row names its trigger or says `on ask`; the ceiling allows Bash only as one command pattern; the first task has an acceptance line a checker can test.
