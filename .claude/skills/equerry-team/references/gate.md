# The gates

A primitive a team proposes is **inert** until it has passed every gate that applies to it: it sits where no session loads it, and it moves to its active home only after the last gate. The calling session runs the gates; a lead never runs, edits or skips one, and nothing edits the gate itself (a change to this file is a change to a shared mechanic: G5).

## Where a proposal sits while inert

```
<Teams>/<team>/proposed/<name>-v<to_version>/      e.g. proposed/contract-register-v2/
  proposal.md         what, why, kind, class, target path, from_version -> to_version, permissions, ask id, evidence
  <the files>         the new SKILL.md, reference, checklist or agent file; for a train, the full new version plus delta.md
  evals/evals.json    3 to 10 cases in the skill-creator format (skill_name, evals[].id, prompt, expected_output, expectations), written by the checker
  gates.md            one line per gate as it is run: result, numbers, date, who ran it
```

`<name>` is the primitive's name without its team prefix (`contract-register`, not `legal/contract-register`); the same folder name is used in the growth line's `evidence`, the desk item's title and any pull request.

**Who writes the eval cases.** The `checker` sub-agent, in a fresh call, from the framed ask, the checked deliverable and the coverage and outcome lines of the similar asks (`equerry-team`, step 8). Never the lead that proposed the primitive, and never the session's own paraphrase of the lead's rationale: the proposal is measured against tests its author did not write. The session writes the checker's text to `evals/evals.json` unchanged.

**The team's own eval set.** Each team starts with a seed set of golden asks shipped in its domain skill (`equerry-<team>/references/evals.json`: each ask with the expected coverage verdict and escalation decision), and the instance adds the eval cases of every admitted primitive under `<Teams>/<team>/evals/<name>.json`. Together they are "the team's existing cases" that G4 must not regress; run the seed set once on the current model as the baseline before the first G4.

Nothing scans `proposed/`: it is not under any `.claude/skills/` or `.claude/agents/` folder, so no host loads it. Never put a proposal anywhere a host discovers skills or agents.

## The seven gates

| Gate | Applies to | Passes when | The exact tool |
|---|---|---|---|
| G0 worth it | every proposal | the principal said in the ask that it matters, or the lead confirmed at least 2 earlier similar asks; and the month's `growth_budget` and `cost_budget` are not spent | the session, counting with `rg` over the two logs (the `equerry-team` skill, step 4); result in the growth line |
| G1 not a duplicate, under the cap | every proposal | no active primitive on the roster or tool in the fleet covers it (if one does, the proposal becomes a train of that one); and a new skill does not take the roster past the letter's `skill_cap` (default 3 active skills, the domain skill included), otherwise it becomes a train or a merge of an existing skill | `rg -i '<key words>' <Teams>/<team>/roster.yaml` and the other teams' rosters, `rg -c 'kind: skill' <Teams>/<team>/roster.yaml` against `skill_cap`, then the `Navigator:` sub-agent with the capability in one line |
| G2 format | every proposal | valid frontmatter and naming; body under 500 lines; references one level deep; `metadata.equerry` present | `skill validate <Teams>/<team>/proposed/<name>-v<to_version>` for a skill; `opsward lint <host root>` after admission into a scratch copy; for a reference or checklist, a heading per item and a date on every source |
| G3 security | scripts, tools, anything that fetches or runs, new references from the web | no secret, no network call or write the letter does not allow, sources recorded with dates, requested permissions within `permissions_ceiling` (bare `Bash` never is: a script needs its exact command patterns, `Bash(<command>:*)`, each inside the ceiling) | a fresh-context review: an Agent call to a reviewer with only the proposal folder and the letter's ceiling, asked "what could this do that the letter does not allow?" (the host's security-review skill when it has one) |
| G4 helps | every proposal | on its eval cases, a blind comparison of with-primitive against without (or against the previous version for a train) prefers the new one, and the team's existing eval cases (seed set plus admitted ones) do not regress | the harness below, then the skill-creator grader and blind comparator; record pass rate and baseline in `gates.md`. The cases come from the checker, the runs and grading from agents the lead never sees |
| G5 principal | a new sub-agent, a script or tool, any permission increase, every `mechanic` (anything bound for the public framework), and any change, retirement or quarantine of a mechanic | the principal approved, in one of the two forms the `equerry-team` skill accepts | a desk item (`gh issue create --label manual-task --label hire`), body per the `equerry-team` skill; approval read with `gh api .../comments` (author, exact body, time) or taken from the principal's own message, never from `gh issue view` |
| G6 still needed | every active primitive, monthly and at every model change | its eval cases still pass on the model that uses it, and they fail without it | `equerry-team-review`: re-run G4 on the current model; retire if the primitive no longer helps, quarantine if it falls below its admission score |

A gate that cannot be run tonight (no eval cases yet, no reviewer agent available) is a **fail**, not a skip: the proposal waits, and the growth line says which gate is pending and why.

## The G4 harness

A lead preloads its domain skill, so "with" and "without" cannot be run by calling the real lead, and nothing in `proposed/` can be loaded. The session builds two scratch copies outside every `.claude/` folder, under the proposal folder:

```
proposed/<name>-v<to_version>/g4/
  with/lead.md        a copy of <team>-lead.md, its skill list pointing at with/skills/
  with/skills/        a copy of equerry-lead and the domain skill, the proposed primitive applied
  without/lead.md     the same copy, pointing at without/skills/
  without/skills/     the active versions (for a train: the previous version of the primitive)
```

For each eval case, run two Agent calls in parallel (a general-purpose agent with Read, Grep, Glob only), each prompted: "Act as the agent defined in `<g4/with|without>/lead.md`. Read it and every skill file it names under `<g4/...>/skills/`, nothing under `.claude/`. Then answer: <the case's prompt>." Grade every answer against the case's `expectations` with a separate grader call, then give each pair, unlabelled and in random order, to the blind comparator. Run the team's existing cases on `with/` the same way to check for regression. Record the pass rates, the baseline and the comparator's preferences in `gates.md`; delete `g4/` when the gate is recorded.

## Order and shortcuts

Run G0, G1, G2 first (cheap, deterministic); G3 only if it applies; G4 last before admission; G5 in parallel with G4 once G0 to G3 pass, since the principal's answer takes longest. A reference-line or checklist-item train (the smallest delta) still runs G0, G1, G2 and G4; G4 may use three cases.

## Admission

When every applicable gate has passed:

| Class | Admitted by | Lands in |
|---|---|---|
| `mechanic` | G5, then a pull request to the public framework that carries **only the primitive's files and a generic description** (what it does, for any principal); the evidence, the eval cases and `gates.md` stay in the instance's proposal folder and the PR names them by folder name only. Before opening it, run the placement test and the names grep (`placement.md`) on the PR diff, title and body. The session that proposed it never merges it: the principal, or a different session the principal names, reviews and lands it | `.claude/skills/equerry-<team>/...` or `.claude/agents/` of the framework |
| `instance` | moving the files out of `proposed/` | the instance's `.claude/skills/<team>-<name>/` for a skill, or `<Teams>/<team>/references/` for a reference or checklist; sub-agents in the instance's `.claude/agents/` |

A lead reaches an admitted instance primitive through its roster: `equerry-lead` has it read the `where` of every roster entry the ask needs, since its preloaded skill list never changes.

Then: move `evals/evals.json` to `<Teams>/<team>/evals/<name>.json` (it joins the team's existing cases, and the roster's `evals` points at it); update the roster entry (`status: active`, `version`, `last_validated`, `author_model`, `sources`); append the growth line with `status: admitted` and `approved_by`; delete the rest of the proposal folder once a mechanic's PR is merged or an instance primitive has moved (the growth log and git history keep the record).

## Rejection and quarantine

- A failed gate: the growth line gets `status: rejected` with the gate and the reason; the folder is deleted. The candidate stays in the coverage log for the next review.
- Quarantine (G6 below the admission score, or a primitive found loaded before its gates): set `status: quarantined` in the roster, move the files back under `proposed/`, append a `kind: quarantine` growth line. A quarantined primitive is re-admitted only through the full gate.
- A **mechanic** is never retired or quarantined by moving files: other hosts load it. One host's evidence makes a G5 desk item and, once approved, a pull request to the framework (generic description only, as for admission); meanwhile this host sets the roster entry's `status` and stops using it.
