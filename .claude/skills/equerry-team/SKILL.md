---
name: equerry-team
description: "Run an ask through a team of the equerry organisation: frame it neutrally, have the team's lead judge coverage from artefacts, do the work, verify it outside the model, distil a small primitive from the checked work, and gate that primitive before anything can load it. Use for any ask to a team (legal, finance, people, bizdev, marketing, product, engineering, ventures): 'ask legal', 'have finance look at', 'what would marketing say', 'run this past the team', 'which team handles this', 'is this covered', and whenever a <team>-lead sub-agent is about to be called. The leads preload it for the reply shape and what to do per verdict."
metadata: {equerry: {team: all, class: mechanic, origin: human, origin_ask: "docs/teams.md", version: "1", status: active}}
---

# equerry-team: assess, act, verify, distil, gate

A **team** is a namespace of primitives (skills, references, checklists, sub-agents, tools) used by one **lead**, a read-only generalist sub-agent working under a written **engagement letter**. This skill is the protocol. Two readers use it:

- **the calling session** (the one that received the ask) runs the ten steps below, writes every file and files every desk item;
- **the lead** (`<team>-lead`) reads "What the lead does, per verdict" and `references/reply-shape.md`, and returns text only.

The design record is `docs/teams.md` in this framework's repository.

## Who does what

| | The calling session | The lead |
|---|---|---|
| Frames the ask | yes (step 1) | no |
| Judges coverage | no | yes, from artefacts (step 2, 3) |
| Writes the coverage and growth logs | yes, by appending JSONL | never |
| Researches and produces the deliverable | no | yes (step 6) |
| Verifies | calls the `checker` or the `contradictor` (step 7) | fixes the work when the check fails |
| Distils a primitive | writes it to `proposed/` where nothing loads it | proposes it as text (step 8) |
| Runs the gates, files desk items | yes (step 9) | never |

A lead never edits a file, so a primitive can never grant itself permissions: the session writes it, and only after the gates.

## The instance block

Every host that runs teams has one block in its `CLAUDE.md` (or `.claude/CLAUDE.md`). Read it first: `rg -A10 '^## equerry' CLAUDE.md .claude/CLAUDE.md`.

```markdown
## equerry
- Teams: teams/
- Team records: teams/
- Dossier: <path to the principal's facts file>
- Venture pages: ventures/
- Navigator: navigator
- Desk: manual-task issues in <owner/repo>
- Fleet map: <optional: files listing the tools this host's fleet owns>
```

| Line | What it names | When the line is absent |
|---|---|---|
| `Teams:` | the folder holding `<team>/engagement.md`, `roster.yaml`, `references/`, `proposed/` | `teams/` at the host's root |
| `Team records:` | the root under which `<team>/coverage.jsonl` and `<team>/growth.jsonl` live | the same folder as `Teams:` (logs beside the letter) |
| `Dossier:` | the principal's facts file | no dossier: the lead marks the context dimension accordingly and asks for facts in "what we would need to know" |
| `Venture pages:` | one page per venture | no pages: same as above |
| `Navigator:` | the sub-agent that knows which tools exist | `navigator` (this framework's generic one) |
| `Desk:` | where hire, escalate and G5 items go | **say so and stop** at the first step that needs a desk item: print the item in your reply, write nothing, and tell the principal the host has no desk |
| `Fleet map:` | extra map files the navigator reads | none |

Below, `$TEAMS`, `$RECORDS` and `$TEAM` stand for the resolved `Teams:` path, `Team records:` path and the team id. A team without `$TEAMS/$TEAM/engagement.md` does not run: say "this team has no engagement letter" and stop.

## The protocol (checklist for the calling session)

Steps 1 to 3 run on every ask; 4 to 9 only on a gap; 10 always.

1. **Frame.** Write one line each: the ask, the deliverable, the deadline, the consent class (`reversible` when the architecture's gate could let the action through, otherwise `one-way`), the owner team and any consulted teams. A cross-domain ask has one owner; the others are consulted, or the ask goes to `equerry-council`. A cross-domain *event* (a launch, a new client, a fundraise) opens one item per team: `references/cross-domain-events.md`. **Frame neutrally**: the question, not the enthusiasm it was born in; never "isn't this a great idea", always "does this hold, and what would it take". Give the ask an id: `<team>-<YYYYMMDD>-<n>`, `n` counting that team's asks that day.
2. **Call the lead.** One Agent call, `subagent_type: <team>-lead`, with this prompt and nothing else (pointers, not pasted documents):

   ```text
   Ask <id>: <the neutral one-line question>
   Deliverable: <what>, by <deadline>. Consent class: <reversible|one-way>.
   Owner team: <team>. Consulted: <teams or none>.
   Instance: Teams <path>; Team records <path>; Dossier <path or none>; Venture pages <path or none>.
   Pointers: <paths, issue URLs, venture pages the ask needs>.
   Already decided: <pointers to decisions, or none>.
   Reply per equerry-team/references/reply-shape.md.
   ```

3. **Record the verdict.** The reply starts with the coverage block (`references/reply-shape.md`). For `covered` or `covered-with-gaps` it carries the work too; go to step 7. For `escalate` it carries the brief: append the coverage line, file the escalate desk item (below), stop. For `train` or `hire`, go to step 4 before appending: the coverage line is written once, with the verdict as it stands after G0.
4. **Worth it (G0).** Grow only if the ask is high-value (the principal said so, or it touches money, rights or a deadline) or this is at least the third similar ask, and the month's budget in the letter's `growth_budget` is not spent. Count both from the logs:

   ```bash
   rg -i '<two or three words of the ask>' "$RECORDS/$TEAM/coverage.jsonl" | wc -l      # similar asks so far
   rg "\"when\": ?\"$(date +%Y-%m)" "$RECORDS/$TEAM/growth.jsonl" | rg -c '"kind": ?"train"'   # trains this month
   rg "\"when\": ?\"$(date +%Y-%m)" "$RECORDS/$TEAM/growth.jsonl" | rg -c '"kind": ?"hire"'    # hires this month
   ```

   A missing log file means zero. G0 fails: the verdict becomes `covered-with-gaps`, the line's `candidate` field keeps what would have been grown (the log is the training queue), and you ask the lead to do the job once with research (step 6).
5. **Ask the navigator before any hire.** One Agent call to the `Navigator:` sub-agent: "Does this host already have <the capability, one line>?" Its verdict decides: `exists` means borrow that tool (add it to the roster as `class: fleet`, no hire); `partial` means do the job by hand now and file the navigator's draft issue in the package it names; `gap` means proceed. A `train` skips this step only if the roster names the primitive to change.
6. **Do.** Send the lead the G0 and navigator results and ask for the work (continue the same lead agent if the host can, otherwise a fresh call with the same prompt plus those results). Research uses dated sources, not recollection; every claim is marked certain, to verify or interpretation.
7. **Verify outside the model.** Pick one, in this order: a test the deliverable must pass; the `checker` sub-agent with the deliverable and a named checklist (the domain skill's `references/checklists.md` section, or this skill's references); the `contradictor` for a decision or a recommendation. If it fails, send the failing items back to the lead and fix the work, never the check. After two failed rounds, deliver with the failures stated and mark the coverage line `quality: unverified`.
8. **Distil** (only on `train` or `hire` that passed G0). Ask the lead, after the check passed: "From the checked work, the smallest change that would have made ask <id> covered, as a delta, with its class and 3 to 10 eval cases." Preference order: a reference line, a checklist item, a skill section, a new skill, a script or tool, a sub-agent. Classify it with `references/placement.md`: `mechanic` or `instance`.
9. **Gate.** Write the proposal to `$TEAMS/$TEAM/proposed/<primitive-id>/` (`proposal.md`, the files, `evals/evals.json`), where no session loads it. Append a growth line with `status: proposed`. Run the gates in `references/gate.md`. Hires, scripts, tools, permission increases and any change to a mechanic shared by several teams also need the principal (G5): file the hire desk item. Admit only what passed every gate that applies, then append a growth line with `status: admitted`.
10. **Act and report.** Deliver to where the letter says (the brief, the desk, a venture page, a published page). Append the coverage line if not yet written, with `primitives_used`. When the principal accepts, revises or rejects the result, append an outcome line (`references/team-files.md`). A correction is a training candidate for next time, not a rewrite now.

## Appending a log line

Logs are append-only JSONL, one object per line, compact keys in the order given in `references/team-files.md`. The file is created by the first append, never beforehand.

```bash
mkdir -p "$RECORDS/$TEAM"
cat >> "$RECORDS/$TEAM/coverage.jsonl" <<'EOF'
{"id": "finance-20261109-1", "when": "2026-11-09T21:40", "team": "finance", "ask": "Price a two-day workshop for a 12-person team", "verdict": "covered", "procedure": "equerry-finance offer-pricing checklist", "quality": "checker against checklists.md#offer-pricing, pass", "knowledge": "rate references checked 2026-11-09", "context": "dossier: day rate present", "authority": "recommends; principal sets the price", "primitives_used": ["finance/offer-pricing"], "candidate": null}
EOF
tail -1 "$RECORDS/$TEAM/coverage.jsonl" | python -m json.tool > /dev/null && echo ok
```

Use a quoted heredoc delimiter (`'EOF'`) so the shell expands nothing; escape any double quote inside a value. Never rewrite or sort a log.

## What the lead does, per verdict

For leads. After the "Before anything" reading your own file lists, write the coverage block, then:

| Verdict | When | What you return after the block |
|---|---|---|
| `covered` | all five dimensions hold from artefacts | the work, in the advice shape |
| `covered-with-gaps` | something is missing but the job can be done once with research | the work, with the gaps named in "what bothers me" |
| `train` | a primitive on the roster is close and a delta would cover it | stop: one line naming the primitive and the missing delta; the session runs G0 and comes back for the work |
| `hire` | no primitive covers a recurring or high-value need | stop: one line describing the missing capability, for the navigator |
| `escalate` | the authority dimension crosses the letter's escalation line or "never" list | the escalation brief only, no answer to the underlying question |

`escalate` wins over every other verdict. When the session comes back for the work (step 6) or the distillation (step 8), you still return text only: a proposed primitive is a block of text with its target path, class, kind, permissions and eval cases, inert until the gates pass.

## Desk items

A desk item is a GitHub issue in the repository the `Desk:` line names, with `manual-task` and one of `hire` or `escalate`. Create the labels once with `gh label create hire --repo <owner/repo> --force` (same for `escalate`, `manual-task`).

```bash
gh issue create --repo <owner/repo> --label manual-task --label hire \
  --title "hire(<team>): <primitive-id>: <one line>" --body-file <file>
```

The body, in this order: what is proposed and its kind; the ask that caused it (id and one line); the evidence (the checked deliverable); each gate's result with numbers; permissions requested against the letter's `permissions_ceiling`; **the predicted objection** (the host's representative if it has one, otherwise the strongest objection you can name, labelled as yours); how to approve (a comment saying "approved", read back with `gh issue view <n> --comments` before admitting). An escalate item carries the brief from `references/reply-shape.md` and the professional to consult. Nothing in a desk item may contain a secret or a value the dossier labels above `green`.

## Budget and cadence

- Growth budget: the letter's `growth_budget` per calendar month (default `{train: 3, hire: 1}`); G0 refuses beyond it.
- One lead call per ask for `covered` and `escalate`; at most three (assess, work, distil) for a gap.
- Recurring deliverables (a monthly pack, a calendar sweep) are asks like any other, triggered by the letter's cadence table; each gets a coverage line.
- `equerry-team-review` runs monthly per team and at every model change.

## Signs it went wrong

- The lead says `covered` without naming a primitive or a reference: that is its sense of competence, not an artefact. Ask again with the roster path.
- A log line was edited or deleted, or a proposal was loaded before its gates: revert and record it in the growth log as `kind: quarantine`.
- The lead's reply has no sources, or no claim marking: send it back.
- Two teams answer the same ask separately: it was cross-domain; route it to one owner or to the council.

## References

| File | Read it when |
|---|---|
| `references/engagement-letter.md` | writing or reading a letter; checking what a team may decide |
| `references/team-files.md` | writing a roster entry, a coverage or growth line, primitive frontmatter; computing the false-coverage rate |
| `references/gate.md` | running G0 to G6; where a proposal sits and how it is admitted |
| `references/placement.md` | deciding mechanic or instance; deciding which repository a file goes in |
| `references/cross-domain-events.md` | an ask is an event that touches several teams |
| `references/reply-shape.md` | writing or reading a lead's reply, an escalation brief or a review finding |
| `templates/engagement.md`, `templates/roster.yaml`, `templates/team-README.md` | setting up a new team folder |
