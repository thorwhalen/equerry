# The team's files: roster, growth log, coverage log, primitive frontmatter

Three records per team. The roster is design and lives with the letter; the two logs are data and live under the records root (`Team records:` in the instance block). All three are plain files the principal can read and edit; only the calling session writes them, never a lead.

| File | Where | Written | One entry per |
|---|---|---|---|
| `roster.yaml` | `<Teams>/<team>/roster.yaml` | edited in place by the distil and review steps | primitive the team uses, its own or borrowed |
| `growth.jsonl` | `<Team records>/<team>/growth.jsonl` | append-only | train, hire, retire, merge or quarantine event |
| `coverage.jsonl` | `<Team records>/<team>/coverage.jsonl` | append-only | ask, plus one outcome line when the principal reacts |

The log files are created by their first append, never pre-created. A missing file means "no entries yet".

## Roster

A YAML list. Field by field:

| Field | Meaning |
|---|---|
| `id` | `<team>/<name>`; for an owned primitive it matches its folder or file name |
| `kind` | `skill`, `reference`, `checklist`, `subagent`, `script`, `mcp-tool`, `fleet-tool` |
| `class` | `mechanic` (reusable for anyone, lives in the public framework), `instance` (tuned to this principal, lives in the instance), `fleet` (borrowed, owned by another package) |
| `where` | a path for an owned primitive; for a fleet tool, the package and verb (`acquaint brief`, `openloops: ol owed`) |
| `version` | a string, `"1"` and up; a train bumps it |
| `origin` | `human`, `agent`, `imported` |
| `origin_ask` | the coverage entry id or issue URL that caused it; `seed` for the first roster |
| `created` | ISO date |
| `last_validated` | `{date, model, pass, baseline}`: the last G4 or G6 run, the model that ran it, its pass rate and the without-primitive baseline; `null` until first run (a human-seeded primitive is then validated per ask by the checker against its named checklist) |
| `evals` | the path to its eval cases (`<Teams>/<team>/evals/<name>.json` for an admitted primitive; the domain skill's `references/evals.json` seed set for the lead and the domain skill), or `none` |
| `permissions` | the tool names it needs, `Bash` only as command patterns (`Bash(<command>:*)`); compared with the letter's `permissions_ceiling` |
| `status` | `proposed`, `active`, `quarantined`, `retired` |
| `author_model` | the model that wrote it (`claude-opus-...`), or `null` for a primitive a person wrote; a primitive distilled by a lead always has one, so a later review can tell what a model wrote about itself |
| `sources` | the dated sources and the evidence it was distilled from (paths, URLs, the coverage ids), or `[]` |
| `keep` | optional: a one-line note by the principal; the team review never retires a primitive that has one |

```yaml
- id: finance/offer-pricing
  kind: checklist
  class: mechanic
  where: .claude/skills/equerry-finance/references/checklists.md#offer-pricing
  version: "1"
  origin: human
  origin_ask: seed
  created: 2026-11-09
  last_validated: null
  evals: .claude/skills/equerry-finance/references/evals.json
  permissions: [Read]
  status: active
  author_model: null
  sources: []
```

## Coverage log

One line per ask, written by the calling session at step 10 (when the verdict, the check and the primitives used are all known), or at step 3 for an `escalate`. Keys, in this order:

| Field | Meaning |
|---|---|
| `id` | `<team>-<YYYYMMDD>-<HHMM>`, the time the ask was framed |
| `when` | ISO date and local time, `2026-11-09T21:40` |
| `team` | the owner team |
| `ask` | the framed ask, one line, neutral |
| `verdict` | `covered`, `covered-with-gaps`, `train`, `hire`, `escalate` |
| `procedure` | evidence that the team knows the steps: the primitive, or `none` |
| `quality` | a list, one entry per check that ran at step 7, with its last round's numbers: `{"check": <what ran, against which checklist or test>, "pass": <items passed>, "total": <items checked>}`. The work is unverified when an entry has `pass` below `total` after the second round; contradictor only is `[{"check": "contradictor", "pass": null, "total": null}]`; `[]` for an `escalate` |
| `knowledge` | the references used, their dates and jurisdictions; what was stale or missing |
| `context` | the dossier fields and venture pages needed; present or missing |
| `authority` | the letter's right that applied (may decide, recommends, agree, never) and whether the escalation line was crossed |
| `primitives_used` | list of roster ids |
| `similar` | the coverage ids of earlier similar asks, as the lead confirmed them; `[]` if none |
| `candidate` | for every `covered-with-gaps` (whether the lead judged it so or G0 refused growth), what would have closed the gap, one line, from the lead's `Candidate` line; otherwise `null` |
| `cost` | `{"agent_calls": <n>, "tokens": <n or null>}` spent on this ask; summed against the letter's `cost_budget` |

```json
{"id": "legal-20261110-0915", "when": "2026-11-10T09:15", "team": "legal", "ask": "Does the standard client agreement keep background IP with the studio?", "verdict": "covered-with-gaps", "procedure": "equerry-legal contract-register checklist", "quality": [{"check": "checker: equerry-legal checklists.md#contract-register", "pass": 7, "total": 7}, {"check": "test: clause list matches the template", "pass": 1, "total": 1}], "knowledge": "no reference on background-IP clauses; two sources found, dated 2026-11-10", "context": "venture page studio: agreement template linked", "authority": "recommends; escalation line not crossed", "primitives_used": ["legal/contract-register"], "similar": ["legal-20261021-1402"], "candidate": "reference: background-IP clause patterns", "cost": {"agent_calls": 2, "tokens": null}}
```

**Outcome lines.** The log is append-only, so the outcome is a second line with the same `id`: `{"id": ..., "when": ..., "outcome": "accepted" | "revised" | "rejected", "note": <one line>}`. A reader merges lines by `id`, the later line winning. A `revised` or `rejected` outcome with a reason is a training candidate.

```json
{"id": "legal-20261110-0915", "when": "2026-11-12T18:02", "outcome": "revised", "note": "principal wants the carve-out for pre-existing libraries named explicitly"}
```

## Growth log

One line per event. A proposal produces a `proposed` line when it is written to `proposed/` and a second line when it is admitted, rejected or withdrawn. Keys, in this order:

| Field | Meaning |
|---|---|
| `when` | ISO date and time |
| `team` | the team |
| `kind` | `train`, `hire`, `retire`, `merge`, `quarantine` |
| `primitive` | the roster id |
| `class` | `mechanic` or `instance` |
| `from_version`, `to_version` | `null` and `"1"` for a hire; the old and new versions for a train |
| `ask` | the ask that caused it: its id and one line |
| `coverage_ref` | the coverage entry id |
| `evidence` | where the checked deliverable is (a path, a published page, an issue) |
| `gates` | each gate's result and numbers, `{"G0": "pass: third similar ask", "G2": "pass", "G4": "0.9 vs 0.6", "G5": "pending <issue url>"}` |
| `status` | `proposed`, `admitted`, `rejected`, `withdrawn`, `retired`, `quarantined` |
| `approved_by` | `gate` (instance primitives without G5), `principal (in session)` with the quoted message and its time, or `principal (desk comment)` with the comment URL; `null` while pending |
| `cost` | `{"agent_calls": <n>, "tokens": <n or null>, "minutes": <n>}` spent growing (checker eval writing and G4 runs included) |

```json
{"when": "2026-11-10T10:05", "team": "legal", "kind": "train", "primitive": "legal/contract-register", "class": "mechanic", "from_version": "1", "to_version": "2", "ask": "legal-20261110-0915: background IP in the client agreement", "coverage_ref": "legal-20261110-0915", "evidence": "teams/legal/proposed/contract-register-v2/proposal.md", "gates": {"G0": "pass: third similar ask (legal-20261021-1402, legal-20261104-1130)", "G1": "pass", "G2": "pass", "G4": "pending", "G5": "pending"}, "status": "proposed", "approved_by": null, "cost": {"agent_calls": 3, "tokens": null, "minutes": 12}}
```

## Primitive frontmatter

Every skill or sub-agent a team owns carries the same facts as its roster entry, so the roster can later be generated from the files rather than kept by hand:

```yaml
metadata: {equerry: {team: legal, class: mechanic, origin: agent, origin_ask: "legal-20261110-0915", version: "2", status: active, author_model: "<model id>", sources: ["legal-20261110-0915"]}}
```

`author_model` and `sources` are required when `origin: agent` and optional for a primitive a person wrote.

`team: all` marks a primitive shared by every team (the protocol, council and review skills); changing one is a change to a shared mechanic and needs G5. A reference or checklist file that has no frontmatter records the same facts in its roster entry only.

## The false-coverage rate

Of the asks whose verdict was `covered` and that have an outcome line, the share whose outcome is `rejected`. Report `revised` separately: a revision means the work was close, a rejection means the coverage judgment was wrong. Compute it from the log, never estimate it:

```bash
python - "$RECORDS/$TEAM/coverage.jsonl" <<'EOF'
import json, sys
rows = {}
for line in open(sys.argv[1]):
    r = json.loads(line); rows.setdefault(r["id"], {}).update(r)
cov = [r for r in rows.values() if r.get("verdict") == "covered" and "outcome" in r]
rej = sum(r["outcome"] == "rejected" for r in cov); rev = sum(r["outcome"] == "revised" for r in cov)
print(f"covered with outcome: {len(cov)}, rejected: {rej}, revised: {rev}, false-coverage rate: {rej / len(cov):.2f}" if cov else "no covered asks with an outcome yet")
EOF
```

A rising rate means the lead is judging from its own sense of competence; the team review then tightens what counts as `covered` for that team.
