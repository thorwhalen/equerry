# equerry (public)

The organisation layer for a fleet of coding agents. Read `README.md`, then `docs/architecture.md`, then `docs/research/README.md`.

Teams (`docs/teams.md`): `.claude/skills/` holds the protocol (`equerry-team` for the calling session, with its formats in `references/` and team-folder `templates/`; `equerry-lead`, which every lead preloads, with the reply shape), `equerry-council`, `equerry-team-review` and one `equerry-<team>` domain skill per team, each with a seed eval set in `references/evals.json`; `.claude/agents/` holds the eight `<team>-lead` sub-agents and the shared `contradictor`, `checker` and `navigator`. Every file there must pass the placement test in `equerry-team/references/placement.md`.

This repository is public: nothing derived from the principal's private material (sessions, messages, records of people) may be committed here. The principal's own design records, plans, the fleet map and operator-specific pieces live in the private companion named in the host's own configuration (read its `.claude/CLAUDE.md` and its dev-map skill before building anything). Management data lives in the data repository cloned at `~/.local/share/equerry/`, never here.
