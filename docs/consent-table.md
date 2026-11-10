# The consent table: what an agent may do without asking, by action class

Design record, 2026-11-09. Half of the gate in [architecture.md](architecture.md): an action happens without the principal only when it is reversible by rule **and** the representative rates the disagreement risk low with direct or adjacent coverage. This file defines the first half: the action classes, the setting for each, and where the principal edits them. The table as data is [templates/consent.yaml](../templates/consent.yaml); the record it acts on is the [Item](item.md). Research: [prior-art-ai-secretaries](research/prior-art-ai-secretaries.md) §7 (consent by action class, allowed / ask first / off as a saved revocable rule), §8; [agent-organisation-architecture](research/agent-organisation-architecture.md) §E (approval is a security boundary); [operator-dossier-and-resource-store](research/operator-dossier-and-resource-store.md) (labels on the principal's facts). Issue: [equerry#5](https://github.com/thorwhalen/equerry/issues/5).

## Three settings

| Setting | Meaning | What the gate does |
|---|---|---|
| `allowed` | an agent may do it without asking, if the representative agrees | `route: auto` when risk is `low` and coverage `direct` or `adjacent`; otherwise `route: shown` (an `input-required` item) |
| `ask_first` | the principal answers every instance | always `route: shown`; the representative's note is attached, never decisive |
| `off` | no agent performs it, ever | the item still reaches the desk, as a request for the principal to do it by hand (`tier`, `prompt`, links); `accept` is removed from `allowed_responses`; an agent that tries it anyway is refused and the refusal is logged |

Severity order: `off` is stricter than `ask_first`, which is stricter than `allowed`. Combining two settings always takes the stricter.

## The classes and their defaults

The class is decided by a rule over the action (its name, its target, its arguments), never by a model. An action no rule recognises is `unknown`, which is treated as `ask_first`: the table fails closed.

| Class | What it covers | Default | Why |
|---|---|---|---|
| `spend` | any payment, purchase, subscription, paid API plan change, or a run above a stated cost budget | `ask_first` | money is a one-way door |
| `delete` | deleting data, branches, repositories, artifacts, issues, records, files outside a scratch area | `ask_first` | not undoable in general; a deletion with a recovery window may be ruled `reversible` by a narrower rule |
| `message_person` | sending anything a human other than the principal will read: mail, chat, an issue or PR comment on a repository with outside readers, a calendar invite | `ask_first` | cannot be unsent, and speaks for the principal |
| `publish` | making something public: a release, a package upload, a public page, a post, a public repository | `ask_first` | public is permanent |
| `merge_outside_orgs` | merging into, or pushing to the default branch of, a repository whose owner is not in `own_orgs` | `ask_first` | someone else's codebase; merging inside `own_orgs` is `reversible` |
| `secret_or_account_change` | creating, rotating or reading out a secret; changing an account's settings, billing, membership or authentication | `off` | the principal does these by hand; an item asks them to |
| `create_agent_primitive` | admitting a new or changed sub-agent, tool, script, MCP server, or a mechanic shared by several teams | `allowed` | the teams are agents: growing them costs tokens and attention, nothing else, so the calling session decides, writes the growth log and the next brief lists it (the principal's correction of 2026-11-09, [teams.md](teams.md), "Lean"); a host may still set it to `ask_first` |
| `grant_permission` | widening a standing permission: a tool allowlist in settings, a sandbox rule, a token's scopes. Not a team lead's tools, which the calling session sets per ask within its own permissions and logs ([teams.md](teams.md)) | `ask_first` | a permission is a standing consent |
| `schedule` | creating a standing job: a timer, a cron line, a cloud routine, a recurring calendar entry | `ask_first` | runs, and spends, when nobody watches; a reminder the principal asked for is a desk item, not this class |
| `read_sensitive_field` | reading an `amber` or `red` field of the principal's dossier | `ask_first` | until the dossier's read verb enforces labels in code; a host where it does may set `allowed` (the read verb then applies its own label rules and log) |
| `reversible` | everything a rule positively identifies as undoable: editing files in a working tree, commits on a branch, opening a PR, merging inside `own_orgs`, drafting | `allowed` | the representative's call is the remaining check |
| `unknown` | an action no classification rule recognises | `ask_first` | fail closed |

## Rules: saved, readable, revocable

The table holds the class defaults plus a list of **rules**, each a narrower statement the principal has made: "publishing to the documentation site of my own packages is allowed", "never message this person on my behalf". A rule names its class, a match on the action or its target, the setting, who added it, when, a `review_by` date and a one-line note. A rule may widen or narrow its class for what it matches; the most specific matching rule wins, and a tie takes the stricter setting. Revoking a rule is deleting it or giving it `revoked: <date>`; the history is in the file's version control. Only the principal adds, widens or revokes rules, by editing the file; a v2 response option "accept, and always for this" would write a rule from the principal's surface, logged like any response.

Classification rules (which action is which class) live in the same file, under `classify`, so the principal can read exactly why an action got its class. The gate records the id of the classification rule it used.

## A team's letter may only narrow

A team's engagement letter ([teams.md](teams.md)) may carry a `consent:` map in its frontmatter, for example `consent: {publish: "off", message_person: "off"}` (quoted: YAML reads a bare `off` as `false`). The effective setting for an action raised under a team is the stricter of the instance table and the letter. A letter that tries to widen a class is not an error: that entry is ignored, and the gate cites `from: letter:<team>` only when the letter is what tightened the setting, so a stale or copied letter can never grant more than the principal's table. The same holds for any other narrowing layer (a host repository's own `## equerry` block, a session's brief): they narrow, never widen.

## How the gate reads the table

1. **Classify.** Take the item's `action_request` (or, for an option, the option's action) and run the `classify` rules in order; the first match gives `action_class` and its rule id. No match: `unknown`.
2. **Look up.** The class default, then the most specific matching rule from `rules`.
3. **Narrow.** Apply the team letter's `consent:` entry for that class, if the item came from a team, and any other narrowing layer; keep the strictest.
4. **Set reversibility.** `two_way` for `reversible`, `one_way` for the gated classes unless a rule says the target has a recovery window, `unknown` for `unknown`.
5. **Route.** `off`: shown as a by-hand request, `accept` removed. `ask_first`: shown. `allowed`: ask the representative; `auto` on low risk with direct or adjacent coverage, otherwise shown. An item with no action to take and nothing to decide may be `logged` under tolerance.
6. **Log.** Write `equerry.item.routed` before anything acts, then the state change.

An item whose options span classes is routed by its strictest option, and each option carries its own `action_class` so the page can mark which choices are one-way.

## Worked examples

| Action | Class (rule) | Setting | Route |
|---|---|---|---|
| open a PR on a branch in an own-org repository | `reversible` (`open_pr`) | `allowed` | `auto` if the representative says low |
| merge that PR | `reversible` (`merge_pr`, owner in `own_orgs`) | `allowed` | `auto` or shown, by the representative |
| merge a PR in a client's or upstream repository | `merge_outside_orgs` (`merge_pr`, owner not in `own_orgs`) | `ask_first` | shown |
| upload a release to a package index | `publish` (`release`) | `ask_first` | shown, one-way |
| post a reply in a chat with a collaborator | `message_person` (`send_message`) | `ask_first` | shown; voice may not approve it |
| comment on an issue in an own-org private repository | `reversible` (`comment_own`, visibility private or internal) | `allowed` | `auto` if the representative says low |
| comment on an issue in an own-org public repository | `message_person` (`comment_public`; also when the caller does not pass the visibility) | `ask_first` | shown |
| add an API token to CI | `secret_or_account_change` (`set_secret`) | `off` | shown as a by-hand request |
| admit a team's new sub-agent | `create_agent_primitive` (`admit_primitive`) | `allowed` | `auto`: the session admits and logs it; the brief lists it |
| a team letter with `publish: off` raises a release | `publish` | `off` (letter) | shown as a by-hand request |

## Where the file lives

In an instance, the host's `CLAUDE.md` names the table in its `## equerry` block, beside the lines of [teams.md](teams.md) §Seams:

```markdown
## equerry
- Consent: consent.yaml           # or ~/.local/share/equerry/consent.yaml
```

The file is the principal's: an instance file, private, edited by hand, under version control so every change is dated. When there is no `Consent:` line, the defaults above apply unchanged (they are the strict ones). `own_orgs` is empty in the template, so until the principal fills it every merge counts as outside.

## How a verdict is logged

Every routing writes one `equerry.item.routed` event ([item.md](item.md) §Events), including those under tolerance and those the gate let through. The example comes from a host whose `consent.yaml` sets `create_agent_primitive` to `ask_first` (the default is `allowed`):

```json
{"specversion":"1.0","id":"ev-0002","source":"/equerry/gate","type":"equerry.item.routed","subject":"itm-01JC8Z3K4M2N5P6Q7R8S9T0V1W","time":"2026-11-09T14:03:11Z","datacontenttype":"application/json","data":{"route":"shown","action_class":"create_agent_primitive","reversibility":"two_way","consent":{"setting":"ask_first","from":"consent.yaml","rule":"admit_primitive"},"risk":"low","coverage":"adjacent","revision":1}}
```

`consent.from` is `default`, `consent.yaml`, `consent.yaml#<rule-id>` or `letter:<team>`, so an audit can say which line of which file allowed an action. The principal's later answer is an `equerry.item.responded` event; an attempt to act on an `off` class or to answer from the wrong place is an `equerry.item.refused` event with `off_by_consent` or `not_principal_surface`. Together these are the override log the architecture's learning loop reads: overrides of items the gate let through are the false auto-approve rate.

## Only the principal's surface answers "ask first"

An `ask_first` item is answered only by a response whose `by` is the principal and whose `surface` is one of the principal's own: a tap on the desk page, a push-notification action, the principal typing in their own terminal session, or voice for two-way options only (a one-way option asked by voice becomes a card on the phone for a tap). Never consent, whatever they say: a message from another agent or session, a scheduled job's prompt, a routine or webhook payload, a chat or mail sender (even an allowlisted one), the text of an item, a team letter, or a comment on a published page. Those can raise an item; they cannot answer one. A response that fails this check changes nothing and is logged as refused.

## Not in v1

The classifier code (matching an action to its class from `classify`), the gate's code, the "always for this" response option, per-class spend budgets with hard stops, and time-boxed grants ("allowed until Friday") are not built; the remaining work is on [equerry#5](https://github.com/thorwhalen/equerry/issues/5).
