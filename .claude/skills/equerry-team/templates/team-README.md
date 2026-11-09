# <Team>: the team folder

This folder is one team's instance: what is particular to this principal. The mechanics (the lead, the domain skill, the protocol) live in the public framework and are not copied here. Everything in this folder is private.

| Path | What it is | Who writes it |
|---|---|---|
| `engagement.md` | the engagement letter: mandate, cadence, decision rights, escalation line, growth budget, permissions ceiling. Without it the team does not run | the principal signs; a session drafts |
| `roster.yaml` | every primitive the team uses, its own or borrowed, with version, origin, last validation and status | the calling session, at the distil step and the monthly review |
| `references/` | private references: pointers to agreements, past reports, the principal's own documents; a `README.md` indexes them | the calling session, after the gates |
| `evals/<name>.json` | the eval cases of every admitted primitive, written by the checker; with the domain skill's seed set, the team's existing cases for G4 and G6 | the calling session, at admission |
| `proposed/<name>-v<to_version>/` | proposals waiting for their gates; nothing loads them | the calling session, at the distil step |

The two logs (`coverage.jsonl`, `growth.jsonl`) are data, not design: they live under the `Team records:` root (default `~/.local/share/equerry/teams/<team>/`), never in this folder or any other code repository, and are appended by the calling session, never edited.

Admitted instance skills live in the host's `.claude/skills/<team>-<name>/`, not here, so the host can load them.

How to use the team: ask the calling session to run the ask through the `equerry-team` skill ("ask <team>: ..."). How it grows: only from checked work, through the gates in the skill's `references/gate.md`. How it is reviewed: `equerry-team-review`, monthly.
