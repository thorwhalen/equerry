---
name: equerry-team-review
description: "Review one team of the equerry organisation, monthly and at every model change: read its roster, growth log and coverage log; find recurring gaps (the training queue), unused primitives and the false-coverage rate; propose at most three changes and one retirement, each with evidence; re-run the still-needed gate (G6); apply only through the gates; return a one-line summary for the brief. Use for 'review the <team> team', 'monthly team review', 'which primitives are unused', 'what should the team learn next', 'we switched models', 'is the team still calibrated', or when a letter's review date is near."
metadata: {equerry: {team: all, class: mechanic, origin: human, origin_ask: "docs/teams.md", version: "1", status: active}}
---

# equerry-team-review: what the team learned, what it never uses, what it gets wrong

A team that only grows becomes noise. Once a month per team, and for every team when the model that runs the leads changes, the calling session reviews the team's three records and proposes a small, evidenced set of changes. The review proposes; the gates and the principal decide. Paths and the instance block: the `equerry-team` skill.

## When

- **Monthly**, one team at a time, in the first week of the month (cover the previous calendar month).
- **At every model change** for the leads or the host: all teams, G6 only (step 5), before any other review step.
- **When a letter's `review_by` is within 30 days**: add "renew or pause the letter" to the proposals.

## Procedure

1. **Read the records.** The letter (`<Teams>/<team>/engagement.md`), the roster (`roster.yaml`), and the period's lines of `<Team records>/<team>/coverage.jsonl` and `growth.jsonl`:

   ```bash
   rg '"when": ?"2026-10' "$RECORDS/$TEAM/coverage.jsonl"   # the month under review
   rg '"when": ?"2026-10' "$RECORDS/$TEAM/growth.jsonl"
   ```

   Missing logs mean no asks: say so, review only the letter's dates, and stop.
2. **Find the training queue.** Group the period's `covered-with-gaps` lines by their `candidate` and by similar asks; a candidate seen three times, or once on a high-value ask, is a training candidate. Also read `revised` and `rejected` outcome lines: each reason is a candidate.
3. **Find unused primitives.** Every active roster entry whose id appears in no `primitives_used` over the last three months is a retirement candidate (fleet tools included: remove a borrow nobody uses).
4. **Compute the false-coverage rate** with the snippet in `equerry-team/references/team-files.md`. Compare with last month's review. A rate above 0.1, or rising two months in a row, is itself a finding: the team is judging coverage from its own sense of competence; propose tightening what counts as `covered` (a stricter evidence line for one dimension).
5. **Run G6 (still needed).** For each active primitive with eval cases, re-run them on the model the leads now use, with and without the primitive (the skill-creator eval loop, as in `equerry-team/references/gate.md`). Passing without it: retirement candidate (the model learned it). Below its admission score: quarantine now (`equerry-team/references/gate.md`, "Rejection and quarantine"). No eval cases: say so; writing three is a candidate change.
6. **Propose at most three changes and one retirement.** Rank the candidates from steps 2 to 5 by evidence and pick the strongest. One line each, with the evidence:

   ```text
   train finance/offer-pricing: add a VAT line for cross-border clients. Evidence: 3 covered-with-gaps asks (finance-20261003-1, -20261012-2, -20261027-1).
   retire finance/subscriptions-sweep-v1: unused for 3 months; G6 passes without it (0.9 vs 0.9).
   ```

   A team that only accumulates loses; always look for the one retirement, and say why if there is none.
7. **Apply only through the gates.** Each proposed change goes through `equerry-team` steps 8 and 9 (written to `proposed/`, gated, G5 when it applies). A retirement sets `status: retired` in the roster, appends a `kind: retire` growth line, and removes the files from their active home (git keeps them). The review never edits an active primitive directly.
8. **Summarise in one line for the brief:**

   ```text
   <team> review <YYYY-MM>: <n> asks, false coverage <rate>, <k> changes proposed, <r> retired, letter review <date>.
   ```

   Put it in the principal's brief or, until the host has one, in the reply. Details go in a published page if the principal wants them, never as an interruption.

## Across all teams (quarterly)

Run the monthly review for each team, then look across them: the same gap in several teams is a candidate for a shared mechanic (G5); a primitive two teams duplicate is a merge (one becomes a borrow of the other); the sum of growth against the budgets says whether the budgets are right. Propose budget changes as letter amendments for the principal.

## What the review never does

- Edits an active primitive, a log line, the gate or another team's roster.
- Admits a change because the review proposed it: the gates still run.
- Retires something the principal marked as kept (a `keep:` note in its roster entry).
- Reports more than three changes and one retirement; the rest stay in the queue for next month.
