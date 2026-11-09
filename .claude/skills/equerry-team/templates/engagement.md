---
team: <team id: legal | finance | people | bizdev | marketing | product | engineering | ventures>
lead: <team>-lead
status: proposed          # active | paused | proposed; a team runs only when active AND signed AND review_by is in the future
review_by: <YYYY-MM-DD>   # three to six months out for a new letter
signed: null              # set by the principal only: <YYYY-MM-DD>, <desk item URL or "in person">
jurisdictions: [<US | FR | EU | ...>]   # the rules the team applies; a jurisdiction with no reference pack is a knowledge gap
growth_budget: {train: 3, hire: 1}      # per calendar month; G0 refuses growth beyond it
cost_budget: {agent_calls: 60, tokens: null}   # per calendar month; G0 refuses growth past it
skill_cap: 3              # most active skills on the roster, the domain skill included
permissions_ceiling: [Read, Grep, Glob, WebSearch, WebFetch]   # Bash only as command patterns, e.g. "Bash(python3 -c:*)"; never bare
---
# <Full domain name>: engagement letter

Mandate: <one line: what this team exists to keep true>.

In scope: <the principal; the ventures this team serves, by venture-page name>.

Deliverables and cadence:

| Deliverable | Cadence | Trigger | Output | Delivered to |
|---|---|---|---|---|
| <deliverable> | <weekly if a tolerance breaks / monthly / quarterly / per event> | <scheduled routine or tracker reminder that fires it; "on ask" if none, and then the row promises nothing> | <one page, a table, a script and its output> | <brief / desk / venture page / published page> |

May decide (two-way doors only, see the equerry-team engagement-letter reference): <list>.

Recommends (the principal decides): <list; filing an issue in any other repository belongs here>.

Agree (legal and finance only; a named class, never a general power): <list, or delete this line>.

Never: <signs, files, sends, pays, publishes, promises, represents the principal before an authority, ...>.

Escalation line: <when> -> <which licensed professional, named or "to be named"> -> the brief (facts, question, documents, deadline).

Out of scope: <list, and where each goes instead>.

Leans on: <fleet tools; other teams, as consults>.

References: the framework's `equerry-<team>` skill; `<Teams>/<team>/references/` for the principal's own documents.

First task: <one ask>. Accepted when <an acceptance line a checker can test>.
