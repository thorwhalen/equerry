---
name: equerry-team-review
description: "Review one team of the equerry organisation, when the brief shows it struggling or on request: read its roster, growth log and coverage log; find recurring gaps (the training queue), unused or harmful primitives and the false-coverage rate; prune what goes unused, re-run the still-needed check (G6) where there is doubt, apply the changes the calling session decides, and return a one-line summary for the brief. Use for 'review the <team> team', 'which primitives are unused', 'prune the team', 'what should the team learn next', 'we switched models', 'is the team still calibrated', or when the brief flags a team (rising false coverage, a candidate that keeps coming back, revised or rejected outcomes)."
metadata: {equerry: {team: all, class: mechanic, origin: human, origin_ask: "docs/teams.md", version: "2", status: active}}
---

# equerry-team-review: what the team learned, what it never uses, what it gets wrong

A team that only grows becomes noise: an uncurated skill library degrades retrieval. The review is how a team stays small. It is **optional**, not a calendar ritual: run it when the brief shows a team struggling, when the principal asks, or after the model that runs the leads changes. The calling session decides and applies the changes itself, logs them in the growth log, and the next brief lists them; nothing waits for the principal. Paths and the instance block: the `equerry-team` skill.

## When

- **The brief flags a team**: a false-coverage rate above 0.1 or rising, the same `candidate` three times, `revised` or `rejected` outcomes, or a primitive that has not been used in a long while.
- **On request**: "review the <team> team", "prune the teams".
- **After the leads' model changes**: G6 only (step 5) for the primitives with eval cases, worth doing before the next growth.
- A letter whose optional `review_suggested` date has passed: add "re-read the letter" to the summary. It is a hint; the team keeps running.

## Procedure

1. **Read the records.** The letter (`<Teams>/<team>/engagement.md`), the roster (`roster.yaml`), and the lines of `<Team records>/<team>/coverage.jsonl` and `growth.jsonl` since the last review (or all of them, the first time):

   ```bash
   rg '"when": ?"2026-1[01]' "$RECORDS/$TEAM/coverage.jsonl"   # the period under review
   rg '"when": ?"2026-1[01]' "$RECORDS/$TEAM/growth.jsonl"
   ```

   Missing logs mean no asks: say so, review only the letter's cadence triggers, and stop.

   Also list every row of the letter's deliverables table whose `Trigger` is `on ask` or missing while its cadence promises a date: nothing runs it. Each is a finding ("weekly sweep: no trigger; create one or strike the cadence"). Sum the period's `cost` over both logs for the summary line: that is information for pacing, not a budget.
2. **Find the training queue.** Group the period's `covered-with-gaps` lines by their `candidate` and by their `similar` ids; a candidate seen three times, or once on an ask the principal called important, is a training candidate. Also read `revised` and `rejected` outcome lines: each reason is a candidate.
3. **Find unused primitives.** Every active roster entry whose id appears in no `primitives_used` over the period is a retirement candidate (fleet tools included: remove a borrow nobody uses).
4. **Compute the false-coverage rate** with the snippet in `equerry-team/references/team-files.md`. Compare with the last review. A rate above 0.1, or rising, is itself a finding: the team is judging coverage from its own sense of competence; tighten what counts as `covered` (a stricter evidence line for one dimension).
5. **Run G6 (still needed) where there is doubt.** For a primitive suspected of harm, and for every primitive with eval cases after a model change, re-run its cases on the model the leads now use, with and without the primitive (the G4 harness in `equerry-team/references/gate.md`), with the domain skill's seed set (`equerry-<team>/references/evals.json`) as the baseline. Passing without it: retire (the model learned it). Worse than without it: quarantine now (`equerry-team/references/gate.md`, "Pruning and quarantine"). No eval cases: say so; having the checker write three is a candidate change.
6. **Pick the changes.** Rank the candidates from steps 2 to 5 by evidence; keep the list short (a handful of changes, and always look for at least one retirement: a team that only accumulates loses). One line each, with the evidence:

   ```text
   train finance/offer-pricing: add a VAT line for cross-border clients. Evidence: 3 covered-with-gaps asks (finance-20261003-1, -20261012-2, -20261027-1).
   retire finance/subscriptions-sweep-v1: unused since it was admitted; G6 passes without it (0.9 vs 0.9).
   ```

7. **Apply them.** A train or a hire goes through `equerry-team` steps 8 and 9 (the checker writes the eval cases; the quick checks; the session admits it). Retiring an **instance** primitive sets `status: retired` in the roster, appends a `kind: retire` growth line, and removes the files from their home (git keeps them). Retiring or quarantining a **mechanic** (a file of the public framework) never moves files here, since every host loads it: set this team's roster entry to `retired` or `quarantined`, stop using it, and open a pull request to the framework with a generic description only (`equerry-team/references/gate.md`). Nothing here asks the principal.
8. **Summarise in one line for the brief:**

   ```text
   <team> review <YYYY-MM-DD>: <n> asks, false coverage <rate>, <k> changes made, <r> retired, <cost> agent calls in the period.
   ```

   Put it in the principal's brief or, until the host has one, in the reply. Details go in a published page if the principal wants them, never as an interruption.

## Across teams

When several teams are reviewed together: the same gap in several teams is a candidate for a shared mechanic (a pull request to the framework); a primitive two teams duplicate is a merge (one becomes a borrow of the other).

## What the review never does

- Edits a log line, the checks or another team's roster.
- Retires something the principal marked as kept (a `keep:` note in its roster entry).
- Waits for the principal on a change to the team's own primitives.
