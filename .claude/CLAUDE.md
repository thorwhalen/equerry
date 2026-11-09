# equerry (public)

The organisation layer for a fleet of coding agents. Read `README.md`, then `docs/architecture.md`, then `docs/research/README.md`.

Teams (`docs/teams.md`): `.claude/skills/` holds the protocol (`equerry-team`, with its formats in `references/` and team-folder `templates/`), `equerry-council`, `equerry-team-review` and one `equerry-<team>` domain skill per team; `.claude/agents/` holds the eight `<team>-lead` sub-agents and the shared `contradictor`, `checker` and `navigator`. Every file there must pass the placement test in `equerry-team/references/placement.md`.

This repository is public: nothing derived from the principal's private material (sessions, messages, records of people) may be committed here. The principal's own design records, plans, the fleet map and operator-specific pieces live in the private companion at `$PP/_equerry` (read its `.claude/CLAUDE.md` and the `equerry-dev-map` skill before building anything). Management data lives in the data repository cloned at `~/.local/share/equerry/`, never here.
