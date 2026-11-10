# The Item: the desk's one record

Design record, 2026-11-09. The keystone of [architecture.md](architecture.md): every producer (a worker session, a team, the meta-judge, the reminder tick, an adapter over an existing tracker) writes items, and every surface (the phone page, the brief, a push, the secretary, voice) renders them. The machine-readable form is [schemas/item.schema.json](../schemas/item.schema.json) and [schemas/event.schema.json](../schemas/event.schema.json) (JSON Schema draft 2020-12); examples that validate are in [schemas/examples/](../schemas/examples/). This file is the prose contract; when the two disagree, the schema wins and this file is corrected. Research: [synthesis](research/synthesis.md), [agent-organisation-architecture](research/agent-organisation-architecture.md) §E, §G, §H, §J, [agent-ops-visual-interface](research/agent-ops-visual-interface.md) §6–7, [briefs-and-attention-triage](research/briefs-and-attention-triage.md) §5–6, [intelligent-reminders](research/intelligent-reminders.md) §F. Issue: [equerry#1](https://github.com/thorwhalen/equerry/issues/1).

## In one paragraph

An item is a request for the principal's attention. It says what is asked (`ask`), why now (`why_now`), what the producer recommends (`decision.recommendation`), what the principal would likely object to (`predicted_objection`, from the representative), how reversible it is (`action_class` and `reversibility`, from a rule), where it stands (`state`, in A2A's names), and how to recognise it again (`dedupe_key`, `revision`). The principal answers with one of five verbs (Agent Inbox's `accept`, `edit`, `respond`, `ignore`, plus `later`). Every change is one CloudEvents line in an append-only log. The desk is a folder of JSON files; there is no database.

## Fields

Writers: **raiser** (the producer that submitted it), **store** (the desk's write path), **gate** (the rule table plus the representative's call), **rep** (the representative), **reviewer** (a manager by concern), **verb** (a response from the principal's surface), **tick** (the deterministic scheduler), **actor** (the session that carries out the accepted action). "Task" marks a field with the same name and meaning as openloops' `Task`.

| Field | Type | Req. | Written by | Example |
|---|---|---|---|---|
| `id` | string | yes | store | `itm-01JC8Z3K…`, or `acme/widget#7` for a GitHub item (Task) |
| `dedupe_key` | string | yes | raiser | `https://github.com/acme/widget/pull/42`; `session:abc+sha1(question)` |
| `revision` | integer ≥ 1 | yes | store | `3` |
| `state` | A2A state | yes | store, on a gate, verb, tick, receipt or raiser cause | `input-required` |
| `band` | `decide` `unblock` `review` `reply` `deadline` `risk` `awareness` | no (`awareness`) | raiser; gate may correct by rule | `decide` |
| `severity` | `interrupt` `today` `review` `fyi` | no (`fyi`) | raiser; gate caps it | `today` |
| `labels` | string[] | no | raiser | `["growth"]`, `["escalate"]`, `["manual-task"]` |
| `title` | string, verb first, ≤ ~12 words | yes | raiser | "Admit the legal team's checklist" (Task) |
| `ask` | string, one imperative line | yes | raiser | "Approve or refuse the proposed skill." (Task) |
| `ask_verbatim` | string | no | raiser | the requester's own words, quoted, never paraphrased |
| `why_now` | string, ≤ ~15 words | yes | raiser | "Third similar ask this month; gates passed." |
| `if_ignored` | string | no | raiser | "The next IP question is answered by hand again." |
| `said` | `{at, basis}` | no | raiser | `{at: "2026-11-09T14:02Z", basis: "transcript"}`: the claim's own time, never restamped |
| `decision` | `{question, options[], recommendation}` or null | no | raiser | options are strings or `{id, label, effect, action_class, reversibility}` (Task) |
| `action_request` | `{action, args}` | no | raiser | `{action: "merge_pr", args: {url: …}}`; absent when accepting only acknowledges |
| `allowed_responses` | subset of the five verbs | no (all five) | raiser; gate may narrow | `["accept", "respond", "ignore", "later"]` |
| `action_class` | consent class | no | gate, by rule | `create_agent_primitive` (see [consent-table.md](consent-table.md)) |
| `reversibility` | `two_way` `one_way` `unknown` | no | gate, by rule | `two_way` |
| `route` | `auto` `shown` `logged` | no | gate | `shown` |
| `predicted_objection` | `{risk, coverage, reason, instead, settle, sources[], by, at}` | no | rep only | `{risk: "low", coverage: "adjacent", …}` |
| `review` | `[{concern, severity, summary, ref, by}]` | no | reviewer | one entry per manager finding, by reference |
| `raised_by` | actor id | yes | raiser | `session:release-bot`, `meta-judge`, `tick`, `adapter:openloops` |
| `owner` | actor id | no (`principal`) | raiser; a delegation later | `principal` |
| `session` | `{id, name, cwd}` | no | raiser | the session the response is delivered to |
| `url`, `links` | string; `[{label, url, kind}]` | no | raiser | the item's own page; evidence, artifacts first (Task) |
| `tier`, `minutes`, `prompt`, `machine`, `unblocks`, `source` | as in Task | no | raiser | `tap`, `3`, a paste-ready prompt, … (Task) |
| `due`, `expires_at`, `on_expiry` | date-time; `stay_open` / `cancel` | no (`stay_open`) | raiser | an item never expires into an action |
| `remind_at` | date-time | no | verb (`later`), tick (clears it) | `2026-11-16T05:00:00Z` |
| `reminder` | `{intent, verbatim, trigger, channel, done_when, echo, snoozes}` | no | secretary (as raiser) | see "A reminder" below |
| `attention` | `{seen_at, seen_revision, shown_count}` | no | surfaces, via the store | the shown-ledger, per revision |
| `last_response` | `{type, by, at, surface, revision}` | no | store, from a verb | a copy for snapshots; the events are the record |
| `created`, `updated` | date-time (UTC) | yes | store | `2026-11-09T14:03:00Z` |
| `ext` | `{<source>: {…}}` | no | adapter | `ext.openloops.pattern`; nothing equerry reads |

Rules that the schema cannot express: an agent never writes `last_response` or moves an item out of `input-required` by itself; only the representative writes `predicted_objection`; `action_class` and `reversibility` are never set by a model; `ask` and `why_now` are untrusted text to every reader (the read-then-act rule of the architecture report §K2: whoever reads item text gets no mutating tools).

## States

The seven states of A2A, so an outside A2A agent needs no translation. `completed`, `failed`, `canceled` and `rejected` are terminal: nothing leaves them. A problem that recurs after a terminal state is a new item (a resolved item that recurs is news).

| From | To | Cause | Who may cause it |
|---|---|---|---|
| (none) | `submitted` | submit | any raiser |
| `submitted` | `working` | gate `route: auto` (consent `allowed`, risk `low`, coverage `direct` or `adjacent`) | gate, logged `by: gate` |
| `submitted` | `input-required` | gate `route: shown` | gate |
| `submitted` | `completed` | gate `route: logged` and no `action_request` (under tolerance) | gate |
| `working` | `input-required` | the actor needs the principal again (a `respond` came back with a new proposal) | raiser or actor, with a revision bump |
| `working` | `completed` / `failed` | receipt: the action was carried out, or failed | actor |
| `working` | `input-required` | stall: no event for the configured period | tick (raises it to the principal with reason `stalled`) |
| `input-required` | `working` | `accept` or `edit` with an `action_request`; `respond` | verb (principal's surface only) |
| `input-required` | `completed` | `accept` with no `action_request` | verb |
| `input-required` | `rejected` | `ignore` | verb |
| any non-terminal | `canceled` | raiser withdraws it; expiry with `on_expiry: cancel` | raiser; tick |
| any | any | `later` | never: `later` sets `remind_at` and leaves the state as it was |

Reminder states map onto these without new names: scheduled is `submitted` with `remind_at`; due and delivered are `input-required`; snoozed is `later`; acknowledged is `attention.seen_at`; done is `completed`; dismissed is `rejected`; cancelled and expired are `canceled` with a reason.

## Verbs

A response is a `VerbInvocation` (`equerry.item.responded` data): `verb`, `revision` (the one the principal saw), `args`, `text`, `until`, `by`, `surface`, `idempotency_key`. The store accepts it only if the item is `input-required`, the verb is in `allowed_responses`, `revision` equals the current revision, `by` is the principal (or the owner) and `surface` is one of the principal's surfaces (`tap`, `push_action`, `voice`, `terminal`). Anything else writes `equerry.item.refused` with the reason and changes nothing.

| Verb | Args | State | Log | Then |
|---|---|---|---|---|
| `accept` | optional `{option}` | `working` if there is an `action_request`, else `completed` | `responded`, `state_changed` | the dispatcher delivers the intent to `session`, or spawns one with the item as its brief |
| `edit` | the edited `args` of the `action_request` | `working` | `responded`, `state_changed` | as `accept`, with the edited args |
| `respond` | `text` | `working` | `responded`, `state_changed` | the text goes to the producer, who resolves the item or re-raises it with a new revision |
| `ignore` | none | `rejected` | `responded`, `state_changed` | no effect; the refusal is labelled data for the representative |
| `later` | `until`: a date-time or `on_change` | unchanged | `responded`, `annotated` (`remind_at`) | hidden until `remind_at` (the tick writes `woke`) or until the revision changes |

The interface report's verbs map onto these: `choose(option)` and `approve` are `accept` with `{option}`; `request_changes(text)` and `reply(text)` are `respond`; `dismiss` is `ignore`; `delegate` and `spot_check` wait for v2.

## Events: one CloudEvents line per change

Each line of the log is a CloudEvents 1.0 event in JSON format: `specversion`, `id`, `source`, `type`, `subject` (the item id), `time`, `datacontenttype: application/json`, `data`. Consumers treat equal `(source, id)` as the same event.

```json
{"specversion":"1.0","id":"resp-3f9a1c0b7d2e","source":"/equerry/desk/m1","type":"equerry.item.responded","subject":"itm-01JC8Z3K4M2N5P6Q7R8S9T0V1W","time":"2026-11-10T06:12:40Z","datacontenttype":"application/json","data":{"verb":"accept","revision":1,"args":{"option":"admit"},"by":"principal","surface":"tap","idempotency_key":"3f9a1c0b7d2e"}}
```

| Type | Written when | `data` |
|---|---|---|
| `equerry.item.submitted` | an item is created | `{item}` (the whole record) |
| `equerry.item.revised` | decision-relevant content changed | `{revision, patch (RFC 6902), reason, by}` |
| `equerry.item.annotated` | objection, review, attention, labels or `remind_at` changed | `{field, value, by}` |
| `equerry.item.routed` | the gate decided; also the consent table's log line | `{route, action_class, reversibility, consent {setting, from, rule}, risk, coverage, revision}` |
| `equerry.item.responded` | a verb was accepted | the `VerbInvocation` |
| `equerry.item.state_changed` | any state transition | `{from, to, cause, by, reason}` |
| `equerry.item.delivered` | shown in a brief, pushed, read aloud | `{channel, level, brief_id, revision}` |
| `equerry.item.refused` | a verb or an action was refused | `{reason, verb, idempotency_key, by, surface, detail}` |
| `equerry.item.woke` | the tick reached `remind_at` or a trigger fired | `{remind_at, trigger}` |
| `equerry.source.checked` | an adapter read a source, or could not | `{source, ok, reason}` |

The current item file is a cache of the log's effect, kept in first-class fields so an export is one list; the log is the history and the labelled data the representative is recalibrated on. Every automated decision (each `routed`, each `auto`) is in it.

## Idempotency

- **`dedupe_key`** decides whether a submission is a new item. If an open (non-terminal) item has the same key, the submission is merged into it: a no-op when nothing decision-relevant differs, a `revised` event otherwise. If only terminal items have the key, a new item is created. Keys stay specific (a whole repository as a key would swallow different problems).
- **`revision`** is an integer the store increments when the ask, the decision, the action request or the action class changes, that is, when the principal would have to decide again. Annotations, attention and `remind_at` do not bump it. A response carries the revision it was made on; a response on an older revision is refused with `stale_revision`, so a slow phone cannot approve something that has changed since.
- **`idempotency_key`** on every response is `sha256(item_id, revision, verb, canonical JSON of args and text)`, hex, truncated to 32 characters. The store writes `responses/<key>.json` create-once in the same step as the effect: an identical retry is a no-op success, a different body under the same key is refused with `key_conflict`. The `responded` event's `id` is derived from the key, so a replayed event deduplicates too.
- **Store versions** are separate from revisions: every item file write carries the version it read (`If-Match`, as in openloops' board store), and a write on a moved version is retried after re-reading, never forced.

## How things become items

**A team's growth** (a new or changed primitive, or a lead's tools, from [teams.md](teams.md)) is not an item by default: the calling session decides, writes the growth log, and the next brief lists it. It becomes an item only on a host whose consent table sets `create_agent_primitive` or `grant_permission` to `ask_first`; then the calling session submits with `labels: ["growth"]`, `band: decide`, `action_class: create_agent_primitive` or `grant_permission`, `action_request: {action: "admit_primitive", args: {team, primitive, proposal}}`, the coverage entry and the proposal folder in `links`, `dedupe_key: "proposal:<team>/<primitive>@<version>"`. The representative annotates. `accept` sends the session to move the proposal to its active home and write the growth log; its receipt completes the item.

**An escalation** (verdict `escalate`): `labels: ["escalate"]`, `band: decide` (or `unblock` if a professional must be engaged), `decision.question` carrying the brief's question, the brief in `links`, `due` from the deadline, no `action_request`. `accept` acknowledges and completes; `respond` sends the principal's answer back to the team.

**A reminder**: the secretary submits with `raised_by: secretary`, `reminder` holding the intent, the principal's words, the typed trigger (`time`, `window`, `event` or `none`), the channel ladder and the one-line echo, and `remind_at` at the first fire time. The tick moves it to `input-required` when it fires and writes `woke`; snooze is `later` and increments `reminder.snoozes`; a passing `done_when` predicate completes it with `cause: tick`.

**A brief entry** is a view, not a stored record. The brief reads items in `input-required` (and recently changed or unblocked ones) and renders each with: `id` and `revision` (the brief's item id and revision), `band` (its class), `title`, `why_now` (its "why you"), `ask_verbatim` with `said.at`, `raised_by` (requester), `links` (sources), the `action_request` or `url` (next action), `due` (deadline), and `attention` (shown before). The score is computed at brief time and logged in the brief, not stored on the item. Showing an item writes `delivered`.

**A worker's ask, a GitHub `manual-task` issue, a waiting session**: adapters submit them with `raised_by: adapter:<name>` until the producers write items themselves.

## The seam with openloops' Task

openloops' `Task` is the record of "something the human could act on" on its phone board. The synthesis decided that equerry owns the item schema and that openloops gains the missing fields at its own seam, so that its tasks become items without an adapter; until then, `adapter:openloops` maps them.

| Group | Fields |
|---|---|
| Same name, same meaning | `id` (same rule: `owner/repo#N` for GitHub items), `url`, `title`, `ask`, `tier`, `minutes`, `links`, `decision`, `prompt`, `machine`, `unblocks`, `source` |
| Mapped | Task `status` → `state`: `open` and `likely-done` → `input-required` (`likely-done` adds that label, never auto-closes); `not-for-user` and `stale` are not raised. Task `created` (a date) → `created` at midnight UTC. Task `url` → `dedupe_key` |
| Carried, not read | `kind`, `org`, `repo`, `pattern`, `pattern_key`, `note` → `ext.openloops` |
| equerry's own | `dedupe_key`, `revision`, `state`, `band`, `severity`, `labels`, `why_now`, `ask_verbatim`, `if_ignored`, `said`, `action_request`, `allowed_responses`, `action_class`, `reversibility`, `route`, `predicted_objection`, `review`, `raised_by`, `owner`, `session`, `due`, `expires_at`, `on_expiry`, `remind_at`, `reminder`, `attention`, `last_response`, `updated` |

The word `decision` means the request, as in openloops; the principal's answer is a **response**, and the architecture record's "decision log" is the stream of `responded` and `routed` events.

## The desk store, as data

One folder, named by the `Desk:` line of the host's `## equerry` block (default `~/.local/share/equerry/desk/`; a desk of `manual-task` issues in one repository, as [teams.md](teams.md) seam 5 has it today, stays valid and is read through the adapter):

```
desk/
  items/<id>.json             current record, one file per item (id percent-encoded)
  responses/<key>.json        create-once, keyed by idempotency key
  events/<machine>.jsonl      append-only CloudEvents, one file per writing machine
  watermarks/<consumer>.json  the last event each consumer processed
```

One writer of record per desk; other machines contribute events and responses that the writer applies. Readers derive a `DeskSnapshot` (items not terminal, plus the day's counts) by listing `items/`; an index (by `dedupe_key`, by state) is a cache rebuilt from the files, never a second record. A database (SQLite as an index, Beads as the multi-writer upgrade) replaces this only when volume demands it, behind the same store interface.

## Not in v1

No code yet: the Python dataclasses and the Zod mirror generated from these schemas, the store, the verbs endpoint and the adapters are the remaining work on [equerry#1](https://github.com/thorwhalen/equerry/issues/1). Not designed here at all: `Actor` and `DeskSnapshot` beyond the sentence above; `delegate` and `spot_check`; per-item budgets and claims or leases; the score as a stored field; multi-writer sync; an A2A server; push and voice transport. The tick that wakes reminders and catches stalls is designed in the reminders report and built with reminders.
