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
| `last_validated` | `{date, model, pass, baseline}`: the last G4 or G6 run, the model that ran it, its pass rate and the without-primitive baseline; `null` until first run |
| `evals` | the path to its eval cases, or `none` |
| `permissions` | the tool names it needs; compared with the letter's `permissions_ceiling` |
| `status` | `proposed`, `active`, `quarantined`, `retired` |
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
  evals: none
  permissions: [Read, Bash]
  status: active
```

## Coverage log

One line per ask, written by the calling session once the verdict is final (after G0 for a gap). Keys, in this order:

| Field | Meaning |
|---|---|
| `id` | `<team>-<YYYYMMDD>-<n>` |
| `when` | ISO date and local time, `2026-11-09T21:40` |
| `team` | the owner team |
| `ask` | the framed ask, one line, neutral |
| `verdict` | `covered`, `covered-with-gaps`, `train`, `hire`, `escalate` |
| `procedure` | evidence that the team knows the steps: the primitive, or `none` |
| `quality` | what the result was checked against and how it did; `unverified` after two failed checks |
| `knowledge` | the references used, their dates and jurisdictions; what was stale or missing |
| `context` | the dossier fields and venture pages needed; present or missing |
| `authority` | the letter's right that applied (may decide, recommends, agree, never) and whether the escalation line was crossed |
| `primitives_used` | list of roster ids |
| `candidate` | for `covered-with-gaps` after a G0 refusal, what would have been grown (one line); otherwise `null` |

```json
{"id": "legal-20261110-1", "when": "2026-11-10T09:15", "team": "legal", "ask": "Does the standard client agreement keep background IP with the consultancy?", "verdict": "covered-with-gaps", "procedure": "equerry-legal contract-register checklist", "quality": "checker against checklists.md#contract-register, pass", "knowledge": "no reference on background-IP clauses; two sources found, dated 2026-11-10", "context": "venture page consultancy: agreement template linked", "authority": "recommends; escalation line not crossed", "primitives_used": ["legal/contract-register"], "candidate": "reference: background-IP clause patterns (second similar ask)"}
```

**Outcome lines.** The log is append-only, so the outcome is a second line with the same `id`: `{"id": ..., "when": ..., "outcome": "accepted" | "revised" | "rejected", "note": <one line>}`. A reader merges lines by `id`, the later line winning. A `revised` or `rejected` outcome with a reason is a training candidate.

```json
{"id": "legal-20261110-1", "when": "2026-11-12T18:02", "outcome": "revised", "note": "principal wants the carve-out for pre-existing libraries named explicitly"}
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
| `approved_by` | `gate` or `principal`; `null` while pending |
| `cost` | `{"tokens": <n or null>, "minutes": <n>}` spent growing |

```json
{"when": "2026-11-10T10:05", "team": "legal", "kind": "train", "primitive": "legal/contract-register", "class": "mechanic", "from_version": "1", "to_version": "2", "ask": "legal-20261110-1: background IP in the client agreement", "coverage_ref": "legal-20261110-1", "evidence": "teams/legal/proposed/contract-register-v2/proposal.md", "gates": {"G0": "pass: high value", "G1": "pass", "G2": "pass", "G4": "pending"}, "status": "proposed", "approved_by": null, "cost": {"tokens": null, "minutes": 12}}
```

## Primitive frontmatter

Every skill or sub-agent a team owns carries the same facts as its roster entry, so the roster can later be generated from the files rather than kept by hand:

```yaml
metadata: {equerry: {team: legal, class: mechanic, origin: agent, origin_ask: "legal-20261110-1", version: "2", status: active}}
```

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
