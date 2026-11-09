# <Team>: the team folder

This folder is one team's instance: what is particular to this principal. The mechanics (the lead, the domain skill, the protocol) live in the public framework and are not copied here. Everything in this folder is private.

| Path | What it is | Who writes it |
|---|---|---|
| `engagement.md` | the engagement letter: mandate, cadence, decision rights, escalation line. Active by being present; `status: paused` stops the team | a session drafts; the principal edits when they disagree |
| `roster.yaml` | every primitive the team uses, its own or borrowed, with version, origin, last validation and status | the calling session, at the distil step and in a team review |
| `references/` | private references: pointers to agreements, past reports, the principal's own documents; a `README.md` indexes them | the calling session, once the work they came from was checked |
| `evals/<name>.json` | the eval cases of every admitted primitive, written by the checker; with the domain skill's seed set, the team's existing cases for G4 and G6 | the calling session, at admission |
| `proposed/<name>-v<to_version>/` | a mechanic waiting for its pull request to the public framework, with its evidence; nothing loads it | the calling session, at the distil step |

The two logs (`coverage.jsonl`, `growth.jsonl`) are data, not design: they live under the `Team records:` root (default `~/.local/share/equerry/teams/<team>/`), never in this folder or any other code repository, and are appended by the calling session, never edited.

Admitted instance skills live in the host's `.claude/skills/<team>-<name>/`, not here, so the host can load them.

How to use the team: ask the calling session to run the ask through the `equerry-team` skill ("ask <team>: ..."). How it grows: only from checked work, admitted by the calling session after the quick checks in the skill's `references/gate.md`, with no approval step; the brief lists what was added. How it is reviewed: `equerry-team-review`, when the brief shows the team struggling or on request.
