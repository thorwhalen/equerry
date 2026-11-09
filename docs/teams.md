# Teams: one experienced generalist per domain, growing only from checked work

Design record, 2026-11-09 (phase 3). Builds on [architecture.md](architecture.md) (the chart, the gate, decision rights) and on three reports: [self-growing agent organisations](research/self-growing-agent-organisations.md) (the protocol, the logs, the gates), [domain experts for a solo technical founder](research/domain-experts-for-a-solo-technical-founder.md) (the eight domains, the engagement letter, the escalation line) and [the operator dossier](research/operator-dossier-and-resource-store.md) (facts about the principal). The shape of the expert reply and of the expert council comes from earlier private workspaces built for individual clients; this file keeps the shape and none of their content.

## Verdict

A **team** is a namespace of agent primitives (skills, references, checklists, sub-agents, tools) used by **one lead**: a strong generalist sub-agent modelled on a fractional executive. The lead works under a written **engagement letter**. On every ask it first judges coverage **from artefacts**, then does the work with research, has it checked, and only then distils a small change to its primitives. A primitive it wrote is never used before the work it came from has been checked, and it is pruned when the team review finds it unused or harmful. The team grows without waiting for anyone: the calling session decides, logs the change, and the next brief lists what was added. A team is not a crowd of role-playing agents: personas add nothing, primitives do.

Nothing here is new machinery. Teams are agent skills, sub-agent files and three plain files per team, run by the host (Claude Code); the only things that wait for the principal are the action classes the [consent table](consent-table.md) marks `ask_first`. Code comes later, only where a mechanic cannot be done by a skill (roster generation, a gate runner).

## The model

| Piece | What it is | Where it lives |
|---|---|---|
| Team | a domain namespace with an id (`legal`, `finance`, `people`, `bizdev`, `marketing`, `product`, `engineering`, `ventures`) | a folder per team in the instance (see seams) |
| Lead | one sub-agent per team, strong model, default tools Read, Grep, Glob, WebSearch and WebFetch (no shell, no write; the calling session may give more for one ask), `equerry-lead` and the domain skill preloaded | the framework's `.claude/agents/<team>-lead.md`, linked from the host's `.claude/agents/`; a registered agent only in a session started in the host directory, otherwise inlined by the calling session with its `tools:` line restated as a constraint |
| Lead skill | what a lead reads, returns per verdict and may never do; the reply shape | the framework's `equerry-lead` skill |
| Domain skill | the lead's playbook: recurring tasks and cadence, checklists, the escalation line, an index of references, a seed eval set | the framework's `.claude/skills/equerry-<team>/` |
| Engagement letter | the contract between the principal and the team (format below) | the instance: `<teams>/<team>/engagement.md` |
| Roster, growth log, coverage log | the team's three records (formats below) | the instance and the records root |
| Protocol | assess, act, verify, distil | the framework's `equerry-team` skill, run by the calling session |
| Council | several leads on one question, the contradictor always present | the framework's `equerry-council` skill |
| Team review | optional, when the brief shows a team struggling or on request: recurring gaps, unused or harmful primitives, retirements | the framework's `equerry-team-review` skill |
| Shared staff | `contradictor` (finds what does not hold), `checker` (verifies a deliverable with fresh context), `navigator` (knows what tools already exist) | the framework's `.claude/agents/` |

**Who writes what.** Leads never edit files, and by default cannot: their `tools:` line has no shell and no write tool (a session that gives a lead more for one ask says so in the coverage line). They return a verdict, a deliverable and, when warranted, a proposed primitive as text. The calling session (whoever ran `equerry-team`) runs every command a lead needs and pastes the output, writes the logs, writes each admitted primitive to its home only after the work it came from was checked, has the `checker` write the eval cases, runs the checks and files escalations. This keeps every lead's default permissions small and makes "a primitive is never used before its work was checked" true by construction, not by instruction.

**Formats have one source.** The exact formats (letter, roster, logs, reply shape, gates) live in the `equerry-team` skill's `references/` and in `equerry-lead/references/reply-shape.md`; this record gives their shape and the reasons. Where the two differ, the references win and this file is corrected.

## Lean: what is not emulated from human organisations

The first build copied a human firm's controls: letters to sign and renew, monthly budgets for hiring and training, a cap on skills, a permissions ceiling, an approval desk for every new primitive. The principal removed them on 2026-11-09, in these words: "No need for such formalism and bureaucracy. I want to be lean. It seems like a lot of what you've done there is replicate human organization, and policies. This set of teams made of AI agents. Many of the things do not apply. There is no budget for hiring and training. It is as we need it. Of course we should keep an eye on token usage versus our weekly limits to pace ourselves, but we shouldn't have a fixed monthly budget training and hiring. ... I definitely do not want to have my sessions constantly wait for me to click an 'accept' button. ... I want intelligence, not emulating what happens in the physical world with physical limitations that we do not need."

- **The teams are agents.** Hiring and training cost tokens and attention, nothing else. There is no growth budget, no monthly count of trains or hires, no skill cap and no renewal date.
- **Gates exist only for what is irreversible or costly in the real world:** spend, deletion, messages to people, publication, merges outside the principal's organisations, secrets and accounts. That is the [consent table](consent-table.md). Nothing waits for the principal to click accept unless the table says `ask_first` for that action class; the organisation's own growth is not such a class.
- **Growth is decided by the calling session.** A new or changed primitive, a lead's tools for an ask, a cadence trigger: the session decides, logs it in the growth log, and the next brief lists what was added. A mechanic bound for the public framework is still a pull request, reviewed by a fresh session (not the one that proposed it), and by the principal only when the consent table says so.
- **The one quality rule stays:** a new or changed primitive is used only after the work it came from was checked (a test or the `checker`), and it is pruned when the team review finds it unused or harmful. This is the research finding that holds: a primitive written before its work is checked hurt, one distilled after checked work helped, and an uncurated skill library degrades retrieval ([self-growing agent organisations](research/self-growing-agent-organisations.md), summary).
- **Token pacing is watched, not budgeted.** Every log line carries its cost; the brief reports the week's use against the weekly allowance so the principal can pace; nothing is refused for it. A growth step that will be large in tokens says so first (G0).
- **Letters are active by being present.** No signature, no expiry; `status: paused` stops a team; an optional `review_suggested` date is a hint for the team review, never a refusal.
- **What stays, and why:** the coverage log and the growth log (the team's memory), the `checker`'s verification and the `contradictor` (better work), the distil-after-checked-work rule (the research), the consent table, the Never lists, the Agree rights on named one-way doors and the escalation lines to licensed professionals (all about the real world).

## The engagement letter

One per team, in the instance. A team runs when its letter exists and is not `status: paused`; there is nothing to sign or renew. Format (field by field: `equerry-team/references/engagement-letter.md`):

```markdown
---
team: legal
lead: legal-lead
jurisdictions: [US, FR, EU]     # the rules the team applies; the domain skills ship packs per label
review_suggested: 2027-02-01    # optional: a hint for the team review, never a stop
---
# Legal, IP and compliance: engagement letter
Mandate: <one line>.
In scope: <the principal; the ventures this team serves, by venture page>.
Deliverables and cadence: <table: deliverable | cadence | trigger (a scheduled routine or tracker reminder, or "on ask") | output | delivered to (brief, desk, page)>.
May decide (two-way doors only): <list>.
Recommends (the principal decides): <list; filing an issue in another repository belongs here>.
Agree (legal and finance only): <named classes>.
Never: <list: signs, files, sends, pays, publishes, promises ...>.
Escalation line: <when> -> <which licensed professional, named or "to be named"> -> the brief (facts, question, documents, deadline).
Out of scope: <list>.
Leans on: <fleet tools and other teams>.
References: <the framework's domain skill; the instance's private references folder>.
First task: <one ask, with its acceptance line>.
```

The may-decide list holds only actions the architecture's gate already lets through (reversible by rule, low disagreement risk). Everything else is Recommend; only Legal and Finance hold Agree on their named one-way doors.

## The protocol: assess, act, verify, distil (checklist)

Run by the calling session through the `equerry-team` skill. Steps 1 to 3 run on every ask; 4 to 9 only on a gap (`train`, `hire`, or a `covered-with-gaps` whose candidate takes the distil route).

1. **Frame.** One line: the ask, the deliverable, the deadline, the consent class, the teams it touches, an id stamped with the time. A cross-domain ask names one owner team and consults the others (or goes to the council). Frame it neutrally: the lead gets the question, not the enthusiasm it was born in. **The session matches the ask against the domain's escalation table here**: a match, or a one-way consent class, forces `escalate` (or the council); the lead may escalate further, never less.
2. **Assess from artefacts.** The lead reads its letter, its roster (and the file behind every entry the ask needs), the coverage lines the session pasted for similar asks, each candidate primitive's last validation, the dates and jurisdiction of its references, the principal's dossier and the venture page. It does not consult its own sense of competence.
3. **Verdict.** One of `covered`, `covered-with-gaps`, `train`, `hire`, `escalate`, with one evidence line for each dimension (procedure, quality, knowledge, context, authority). For quality, a human-seeded primitive with a named checklist counts until its first G4: the checker's pass at step 7 validates it. `covered` and `covered-with-gaps`: verify (7), then 10; a `covered-with-gaps` whose `Candidate` names a delta may then be distilled (the distil route: every check at step 7 must have passed; the candidate becomes a train of a roster primitive, or a hire through the navigator; same checks, a pull request for a mechanic). `escalate`: the deliverable is a brief for the principal or the professional, filed as a desk item, and the coverage line is written now; stop. Another team's domain is a `Consult:` line, run as an ask to that team, never an escalation.
4. **Worth it (G0).** The session judges whether the gap will come back (similar asks in the coverage log are the evidence; the principal's word settles it). If not, it does the job once with research and logs `covered-with-gaps` with its candidate: the log is the queue. If growing will be large in tokens (a new sub-agent, a script that needs a security review, a with/without comparison), it says what that will cost in its reply. Nothing waits and nothing is refused for cost.
5. **Ask the navigator before any hire.** "Does the fleet already have this?" Its answer is one of: use tool X (a borrow, not a hire); extend package Y (the navigator's draft issue, passed through the placement test, is filed where the consent table allows and otherwise listed in the brief; do the job by hand meanwhile); a real gap (proceed).
6. **Research, then do.** Sources, not recollection, each with its date. The lead produces the deliverable on the strong model; numbers come from scripts the session runs.
7. **Verify against something outside the model:** a test, or the `checker` sub-agent with fresh context and a named checklist. If it fails, fix the work, not the check. Each check is recorded in the coverage line's `quality` list with its numbers (name, items passed, items total), so a passing check and a failing one are both visible. The contradictor is for decisions; its answer is another opinion from the same model, so work it alone "verified" stays `covered-with-gaps` and is never distilled.
8. **Distil** (only from work that passed a test or the checker). From the checked work, the smallest change that would have made this ask `covered`, in this order of preference: a reference line, a checklist item, a skill section, a new skill, a script or tool, a sub-agent. A delta, never a rewrite. Classed `mechanic` or `instance` by the placement test. **The 3 to 10 eval cases are written by the `checker`, in fresh context, from the ask, the checked deliverable and the similar asks' coverage and outcome lines, never by the lead that proposed the primitive.**
9. **Check and admit.** The session runs the checks below that apply and admits the primitive at once: an instance primitive moves to its active home, the growth log gets one line with `decided_by: session`, and the next brief lists it. **Nothing waits for the principal.** A mechanic reaches the public framework as a pull request carrying only the primitive and a generic description (the evidence and evals stay in the instance, in `<teams>/<team>/proposed/<name>-v<to_version>/`); a fresh session reviews and lands it, never the one that proposed it. Giving a lead more tools follows the same path: the session decides, logs it, and the brief lists it.
10. **Act and report.** Deliver; write the coverage line with the primitives used, the check's result and the cost; when the principal accepts, revises or rejects the result, append an outcome line with the same id (the log is append-only). A correction is a training candidate, not a rewrite.

### The checks

Run by the calling session, without waiting for anyone. They keep the work good; they are not permissions.

| Check | Applies to | Passes when | Run by |
|---|---|---|---|
| G0 cost note | a growth step that is large in tokens | its cost is said in the reply; never a refusal | the session |
| G1 not a duplicate | every proposal | no active primitive or fleet tool covers it (otherwise it becomes a train of that one); a team past two or three active skills prefers a train or a merge, since an uncurated library degrades retrieval | roster search plus the navigator |
| G2 format | every proposal | valid frontmatter and naming; body under 500 lines; one level of references | `skill validate`, `opsward lint` |
| G3 security | scripts, tools, network access, fetched references | static review clean; sources recorded; it can do nothing beyond what it needs | a fresh-context security review |
| G4 helps | on demand: a primitive the team review suspects of harm, or a new sub-agent when the session wants the evidence before using it | blind with/without comparison on its eval cases beats the baseline, no regression on the team's existing cases (the domain skill's seed set plus every admitted primitive's cases) | cases by the checker; runs on scratch copies of the lead and its skills, outside `.claude/`; grading and blind comparison by separate agents (the harness in `equerry-team/references/gate.md`) |
| G6 still needed | in a team review; worth running after a model change | evals re-run on the model that uses it; retire if it passes without the primitive, quarantine if it does worse than without | `equerry-team-review` |

There is no G5: the principal's approval of primitives, tools and mechanics was removed (Lean, above). What a primitive does in the world (a spend, a message, a publication) still goes through the consent table when it happens.

## The three files of a team

**Roster** (`<teams>/<team>/roster.yaml`): one entry per primitive the team uses, its own or borrowed.

```yaml
- id: legal/ip-chain-check        # <team>/<name>; matches the folder or file
  kind: skill                     # skill | reference | checklist | subagent | script | mcp-tool | fleet-tool
  class: mechanic                 # mechanic | instance | fleet (borrowed, owned elsewhere)
  where: <path or package verb>
  version: "1"
  origin: agent                   # human | agent | imported
  origin_ask: <issue url or coverage entry id>
  created: 2026-11-09
  last_validated: {date: 2026-11-09, model: <id>, pass: 0.9, baseline: 0.6}
  evals: <path to eval cases, or none>
  permissions: [Read, WebFetch]
  status: active                  # proposed | active | quarantined | retired
  author_model: <model id, or null for a person>
  sources: [<dated sources and the coverage ids it came from>]
```

**Growth log** (`<records>/<team>/growth.jsonl`, append-only, one line per train, hire, retire, merge, quarantine or tool change; an instance primitive writes one `admitted` line; a mechanic writes `proposed` with its pull request, then `admitted` when it lands): `when`, `team`, `kind`, `primitive`, `class`, `from_version`, `to_version`, `ask` (the ask that caused it, one line, plus its id), `coverage_ref`, `evidence` (the checked deliverable it was distilled from), `gates` (each check's result and numbers), `status`, `decided_by` (`session`, `pr <url>` or `principal`), `cost` (agent calls, tokens, minutes).

**Coverage log** (`<records>/<team>/coverage.jsonl`, append-only, one line per ask, written at step 10, or at step 3 for an escalation): `id`, `when`, `team`, `ask`, `verdict`, `procedure`, `quality`, `knowledge`, `context`, `authority`, `primitives_used`, `similar`, `candidate` (always set on `covered-with-gaps`), `cost`. `quality` is a list of checks, each `{check, pass, total}`. The outcome (`accepted`, `revised`, `rejected`) is a later line with the same id. Its share of `covered` asks the principal later rejected is the team's false-coverage rate.

Primitives a team owns carry the same facts in their frontmatter (`metadata.equerry: {team, class, origin, origin_ask, version, status, author_model, sources}`) so the roster can later be generated rather than kept by hand.

## The placement test

**Swap the principal for a stranger: if the file is still true and names no person, venture, client, private repository or fact about the principal, it is a mechanic and goes in the public framework; otherwise it is an instance and stays private.**

| Public framework (mechanic) | Private instance |
|---|---|
| the protocol, council and team-review skills; the leads, contradictor, checker and generic navigator; the domain skills and their public reference lists; the file formats and templates | engagement letters; rosters; private references and pointers to private repositories; venture pages; the dossier; a navigator that knows one person's fleet; logs |

Logs are data, not design: they live in the records root, never in a code repository.

## Seams

| # | Seam | v1 default (no new dependency) | Replacement you can point at |
|---|---|---|---|
| 1 | where a team's instance lives (letter, roster, private references, admitted instance primitives) | `teams/` in the host repository, named by a `Teams:` line in the host's `CLAUDE.md` | a scaffold-generated client workspace's private folder; the principal's private companion repository |
| 2 | where the logs live | the data root `~/.local/share/equerry/teams/<team>/` (logs are data, never in a code repository) | a synced management-data repository, or blob storage, named by the `Team records:` line |
| 3 | how the roster is produced | edited by the distil step | generated from `metadata.equerry` frontmatter by `opsward` (issue to be filed) |
| 4 | where facts about the principal come from | a facts file named by a `Dossier:` line, read whole only while it holds nothing above `green` (the session checks the labels before passing the path) | `acquaint`'s field-level read verb with purpose and audience (issue to be filed) |
| 5 | where escalations and reminders go | a GitHub issue labelled `manual-task` in the instance repository, which `openloops` already lists (`ol owed`, the board) | the desk `Item` of [equerry#1](https://github.com/thorwhalen/equerry/issues/1) |

NOT seams, written directly on purpose: the lead's runtime (a Claude Code sub-agent; `coact realize` can move it to another backend when a second backend has a consumer), the G4 evaluator, the log format (JSONL), the reply shape, the list of teams.

All five are lines in one block of the host's `CLAUDE.md`, so a host configures a team setup by writing six lines, plus an optional seventh (`Fleet map:`) for its navigator; the block's single source is the `equerry-team` skill:

```markdown
## equerry
- Teams: teams/
- Team records: ~/.local/share/equerry/teams/
- Dossier: <path to the principal's facts file>
- Venture pages: ventures/
- Navigator: navigator            # or the host's own navigator sub-agent
- Desk: manual-task issues in <owner/repo>
- Fleet map: <optional map files>
```

**Surfaces, in order.** (1) Agent skills and sub-agents: the whole of v1. (2) Desk items as issues: already rendered by the obligation tracker and its phone board. (3) A CLI, only when code exists: roster generation, log validation, a gate runner. (4) The desk page shows escalations, and the brief what the teams added. (5) A plugin for chat and phone clients, for hosts that are not Claude Code. None of these would need the core to change, because the core is the letter, the three files and their formats.

## How teams fit the chart

| Chart role ([architecture.md](architecture.md)) | Its relation to the teams |
|---|---|
| Principal | decides every Recommend and every action the consent table marks `ask_first`; reads in the brief what the teams added |
| Secretary (chief of staff) | the front door: routes an ask to a team or to the council, is the directly responsible individual for cross-domain items (a launch, a new client, a fundraise opens one item per team), puts team packs and escalations into the brief; never performs |
| Representative | annotates every team recommendation that reaches the desk with the principal's predicted objection |
| Managers by concern | a lead in review mode is the manager for its concern (legal for privacy-legal, marketing, bizdev and ventures for business, people, engineering for architecture), with its domain checklist and the chart's one opinion schema |
| Meta-judge | collapses team findings into exception items under the daily cap |
| Desk | holds escalations and decisions the letters or the consent table reserve |
| Workers | do the work a team recommends; engineering dispatches to corpus sessions, it does not code |

Each lead's reply has one shape: the coverage block, then **my view**, **what bothers me**, **what we would need to know** (at most two questions for the principal), **sources** numbered; every claim marked **certain** (with its source and the date checked), **to verify** (and with whom) or **interpretation**; a fact from the dossier cited by its field name, never its value, unless the field is labelled `clear`, because the session pastes replies into deliverables that last. In review mode the lead returns findings in the opinion schema instead.

## Terminology

| Term | Meaning here |
|---|---|
| team, lead | a domain namespace; its one generalist sub-agent |
| engagement letter | the team's contract (mandate, cadence, decision rights, escalation line); active by being present |
| ask | one request to a team; the unit of the coverage log |
| primitive | a skill, reference, checklist, sub-agent, script or tool |
| mechanic, instance | reusable for anyone; tuned to this principal (the placement test) |
| verdict | `covered`, `covered-with-gaps`, `train`, `hire`, `escalate` |
| train, hire, borrow | change an existing primitive; add a new one; use a fleet tool owned elsewhere |
| distil | write a primitive from checked work, as a delta |
| admitted, proposed | past the checks and in its active home; a mechanic waiting for its pull request to land |
| escalation line | where a licensed professional is required; crossing it turns the deliverable into a brief |
| council | several leads, blind first round, a confrontation round, a synthesis that keeps disagreements |
| navigator | the sub-agent that knows which tools already exist |
| records root | where the logs live |

## Deliberately not built

- No Python package and no code tonight; nothing a skill and a file can do.
- No standing session per team: a lead is called on demand; a dedicated session is spawned only when a thread with one team runs long.
- No team-to-team messaging: cross-domain work goes through the secretary or the council.
- No approval step and no budget for the organisation's own growth (Lean, above); no self-editing check: a lead never edits the checks or writes its own eval cases.
- No fine-tuning, ever; no automatic merge-and-prune; re-validation only from the first model change.
- No compliance-calendar engine: dates are data in the legal and finance references until reminders exist.
- No dossier fields above `green` until the read verb enforces labels in code.
- No scaffold here: generating a client's workspace stays with the consulting kit that already does it, which will render these mechanics.
