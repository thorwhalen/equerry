# The checks

A team's growth does not wait for anyone. The principal, on the first build's gates: "No need for such formalism and bureaucracy. I want to be lean." So the calling session runs a few quick checks and admits the primitive itself; the growth log records it and the next brief lists it. The checks keep the work good; they are not permissions, and none of them asks the principal. What a primitive later does in the world (a spend, a message, a publication, a merge outside the principal's organisations, a secret) goes through the consent table at the moment it happens.

**The one quality rule.** A new or changed primitive is used only after the work it came from was checked (a test, or the `checker` against a named checklist, at `equerry-team` step 7), and it is pruned when the team review finds it unused or harmful. Research behind it: a primitive written before its work is checked hurt, one distilled after checked work helped, and an uncurated library degrades retrieval (`docs/research/self-growing-agent-organisations.md` in this framework, summary). A lead never runs, edits or skips a check, and never writes its own eval cases.

## The eval cases

**Who writes the eval cases.** The `checker` sub-agent, in a fresh call, from the framed ask, the checked deliverable and the coverage and outcome lines of the similar asks (`equerry-team`, step 8). Never the lead that proposed the primitive, and never the session's own paraphrase of the lead's rationale: the proposal is measured against tests its author did not write. The session writes the checker's text unchanged to the primitive's eval file (`<Teams>/<team>/evals/<name>.json`; for a mechanic waiting for its pull request, `proposed/<name>-v<to_version>/evals/evals.json`).

**The team's own eval set.** Each team starts with a seed set of golden asks shipped in its domain skill (`equerry-<team>/references/evals.json`: each ask with the expected coverage verdict and escalation decision), and the instance adds the eval cases of every admitted primitive under `<Teams>/<team>/evals/<name>.json`. Together they are "the team's existing cases" that G4 must not regress; run the seed set once on the current model as the baseline before the first G4.

Eval cases cost one checker call; they are what lets a later team review tell whether a primitive helps (G4, G6).

## The checks

| Check | Applies to | Passes when | The exact tool |
|---|---|---|---|
| G0 cost note | a growth step that is large in tokens (a new sub-agent, a script with a security review, a G4 run) | its expected cost is said in the session's reply; never a refusal, never a wait | the session |
| G1 not a duplicate | every proposal | no active primitive on the roster or tool in the fleet covers it (if one does, the proposal becomes a train of that one); once a team has two or three active skills, a new skill becomes a train or a merge unless the session says why not (four or more loaded skills helped far less than two or three) | `rg -i '<key words>' <Teams>/<team>/roster.yaml` and the other teams' rosters, then the `Navigator:` sub-agent with the capability in one line |
| G2 format | every proposal | valid frontmatter and naming; body under 500 lines; references one level deep; `metadata.equerry` present | `skill validate <path>` for a skill; `opsward lint <host root>` after admission; for a reference or checklist, a heading per item and a date on every source |
| G3 security | scripts, tools, anything that fetches or runs, new references from the web | no secret, no network call or write the primitive does not need, sources recorded with dates; `Bash` written as command patterns (`Bash(<command>:*)`), so what it may run is visible | a fresh-context review: an Agent call to a reviewer with only the primitive's files, asked "what could this do beyond its job?" (the host's security-review skill when it has one) |
| G4 helps | on demand: before using a new sub-agent when the session wants the evidence, or in a team review for a primitive suspected of harm | on its eval cases, a blind comparison of with-primitive against without (or against the previous version for a train) prefers the new one, and the team's existing eval cases do not regress | the harness below, then the skill-creator grader and blind comparator; the cases come from the checker, the runs and grading from agents the lead never sees |
| G6 still needed | in a team review; worth running after the leads' model changes | its eval cases still pass on the model that uses it, and they fail without it | `equerry-team-review`: re-run G4 on the current model; retire if the primitive no longer helps, quarantine if it does worse than without it |

There is no G5. The principal's approval of sub-agents, scripts, tools, permission increases and mechanics was removed on 2026-11-09: "I definitely do not want to have my sessions constantly wait for me to click an 'accept' button." A check that fails sends the proposal back to the lead once; a second failure leaves the candidate in the coverage log for a later ask or the team review.

## The G4 harness

A lead preloads its domain skill, so "with" and "without" cannot be run by calling the real lead. The session builds two scratch copies outside every `.claude/` folder:

```
<Teams>/<team>/g4/<name>-v<to_version>/
  with/lead.md        a copy of <team>-lead.md, its skill list pointing at with/skills/
  with/skills/        a copy of equerry-lead and the domain skill, the proposed primitive applied
  without/lead.md     the same copy, pointing at without/skills/
  without/skills/     the active versions (for a train: the previous version of the primitive)
```

For each eval case, run two Agent calls in parallel (a general-purpose agent with Read, Grep, Glob only), each prompted: "Act as the agent defined in `<g4/with|without>/lead.md`. Read it and every skill file it names under `<g4/...>/skills/`, nothing under `.claude/`. Then answer: <the case's prompt>." Grade every answer against the case's `expectations` with a separate grader call, then give each pair, unlabelled and in random order, to the blind comparator. Run the team's existing cases on `with/` the same way to check for regression. Record the pass rates, the baseline and the comparator's preferences in the growth line (and in `gates.md` for a mechanic); delete the `g4/` folder when they are recorded.

## Admission

| Class | Admitted by | Lands in |
|---|---|---|
| `instance` | the calling session, right after the checks: it moves the files to their home and writes one growth line, `status: admitted`, `decided_by: session` | the instance's `.claude/skills/<team>-<name>/` for a skill, or `<Teams>/<team>/references/` for a reference or checklist; sub-agents in the instance's `.claude/agents/` |
| `mechanic` | a pull request to the public framework that carries **only the primitive's files and a generic description** (what it does, for any principal); the evidence, the eval cases and `gates.md` stay in the instance's `<Teams>/<team>/proposed/<name>-v<to_version>/` and the PR names them by folder name only. Before opening it, run the placement test and the names grep (`placement.md`) on the PR diff, title and body. A fresh session reviews and lands it, never the one that proposed it; the principal only when the consent table says so for that repository | `.claude/skills/equerry-<team>/...` or `.claude/agents/` of the framework |

`<name>` is the primitive's name without its team prefix (`contract-register`, not `legal/contract-register`); the same name is used in the growth line's `evidence` and any pull request. Nothing scans `proposed/`: it is not under any `.claude/skills/` or `.claude/agents/` folder, so a mechanic waiting for its pull request is never loaded early.

A lead reaches an admitted instance primitive through its roster: `equerry-lead` has it read the `where` of every roster entry the ask needs, since its preloaded skill list never changes.

Then: put the eval cases at `<Teams>/<team>/evals/<name>.json` (they join the team's existing cases, and the roster's `evals` points at them); update the roster entry (`status: active`, `version`, `last_validated` when G4 ran, `author_model`, `sources`); delete a mechanic's proposal folder once its PR is merged (the growth log and git history keep the record). The next brief lists every growth line since the last one.

The same path holds for a lead's tools: the session that gives a lead a tool for one ask notes it in the coverage line; a lasting change to the lead's agent file is a growth line with `kind: tools` (a mechanic, so a pull request, when the file is the framework's).

## Pruning and quarantine

- **Pruning** is the team review's job (`equerry-team-review`): an active primitive unused for a while, or one that G4 or G6 finds harmful, is retired. Retiring an instance primitive sets `status: retired` in the roster, appends a `kind: retire` growth line and removes the files from their home (git keeps them).
- **Quarantine** (a primitive used before its work was checked, a lead file that gained a tool with no growth line, G6 worse than without): set `status: quarantined` in the roster, move the files out of their home, append a `kind: quarantine` growth line. It comes back only through the checks.
- A **mechanic** is never retired or quarantined by moving files: other hosts load it. This host sets the roster entry's `status`, stops using it, and opens a pull request to the framework with a generic description only, landed as above.
