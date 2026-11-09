# The architecture of an organisation layer over many autonomous agents: the desk, the bus, the schedule and the approval queue

Research report, deep tier, 2026-11-09. Design question only: no private material was used. Companion to [the phase-1 report on the principal model and the review organisation](principal-model-and-review-organisation.md) [1], whose "Suggested shape" (decision record, preference store, representative, gate, reviewers, metrics) this report takes as given and does not repeat.

## Summary and recommendation

**The question.** One principal, who reads on a phone, runs many long-lived Claude Code sessions on three machines (a Mac and two Linux servers). Phase 1 settled *who decides what* (a gate on reversibility and predicted disagreement, reviewers by concern, a representative). This report asks *what the machinery is*: where an item lives, how events move, what wakes whom and when, how an approval travels from a phone tap back into a running agent, and which of the field's frameworks, engines and protocols the layer should use.

**Verdict in one paragraph.** Build the organisation layer as **a desk (a task board that is the system of record) plus an append-only event stream (the bus) plus one deterministic tick per machine (the schedule), with the approval queue being nothing more than a view of desk items in one state**. Do not put a hierarchical orchestrator agent in the control path, and do not adopt a durable-execution engine for v1. The evidence: the systems that coordinate many *independent, long-running* coding agents converged on a shared task list plus mailboxes, not on a manager that routes every step (Claude Code's own agent teams [2], Gas Town on Beads [3][4], MCP Agent Mail [5], Paperclip's ticket checkout [6]); manager-routed hierarchies (CrewAI hierarchical [7], LangGraph supervisor [8], Magentic-One [9]) are in-process designs whose manager context becomes the bottleneck. Durable-execution engines (Temporal, Inngest, Trigger.dev, Restate, Hatchet, DBOS) solve "a function suspended for days must resume exactly once in the same code" [10][11][12]; here the long-running unit is a Claude Code session that already persists and resumes its own conversation [13], the wait is on a human for hours, and every resumption is a *fresh turn carrying the answer*, which is A2A's model (a new message with the same task id) [14], not a replayed stack frame. What the layer should borrow from those engines is the **waitpoint token**: an approval is a record with an id, a status (waiting, completed, timed out), a deadline and an idempotency key [11][15].

**The three strongest recommendations.**

1. **One writable home per record type, current state in first-class fields, events as provenance.** The desk item is the source of truth; the bus only says that something changed. This is the rule the owner's openloops and crowsnest code already enforces (versioned writes with `If-Match`, an immutable outbox where an identical retry is a no-op, state "never a fold over a log") [16][17]; extend it rather than introduce a broker or a database server.
2. **Never block an agent on the principal.** Convert every "needs you" into a desk item and end the turn; resume by sending the decision into the session as a new message. Blocking primitives (LangGraph `interrupt()`, OpenAI `needsApproval`, the Agent SDK's `canUseTool`, MCP elicitation) pause a live process [18][19][20][21]; a phone-reading principal answers in hours, during which a session held on a permission prompt does nothing, while other forwarded dialogs close after five minutes with their no-action default [22].
3. **Use the field's words in the schema, so later seams are free.** Item states named after A2A's task states (`submitted`, `working`, `input-required`, `completed`, `failed`, `canceled`, `rejected`) [14]; human responses named after LangChain's Agent Inbox (`accept`, `edit`, `respond`, `ignore`) plus the owner's existing `later` [23][17]; events shaped as CloudEvents (`id`, `source`, `type`, `subject`, `time`) so deduplication is `source + id` [24]. An A2A facade, an AG-UI front end or a Beads backend can then be added at a boundary that already exists.

**Biggest risks.** Approval spoofing (an agent, a routine payload or a chat bridge "approving" on the principal's behalf; Claude Code already treats all three as untrusted [25][26][27]); multi-writer conflicts across three machines; prompt injection travelling through item text into the representative and the secretary; dependence on Claude Code features that are research previews and change monthly; and token cost when every event wakes a model (agent teams scale cost linearly with teammates [2]; multi-agent research systems use about 15 times the tokens of a chat [28]).

## What already exists, and what Claude Code now ships

The owner's public packages already contain most of the plumbing this layer needs. crowsnest's courier is a once-a-minute tick that pulls a phone page's `attention` and `intents` collections into a local mirror, answers what it can from disk, hands the rest to a Claude session as an `intent` line on the event log that `crowsnest watch` streams, writes a heartbeat, and pushes back "never over a document the page wrote since the pull" [29]. crowsnest's attention store keeps what the person decided (seen, later, done, note) as one document per item, pinned to a revision fingerprint so an item returns only when what must be decided changes, and states "never a fold over a log" [17]. openloops' board store enforces roles and concurrency: `If-Match` versioned writes, `outbox` events that are immutable with identical retries as no-op successes, receipts written only by the receiving session [16]. openloops' `Task` already carries a `decision` (`question`, `options`, `recommendation`), a tier, minutes and a machine [30]. The design records behind openloops also adopted "a human gate as a type that only a person can resolve" from Beads; that record is not public, so this is stated without a link.

Since mid-2026 Claude Code itself ships primitives that overlap with an organisation layer, all fetched from the official documentation on 2026-11-09:

- **Cross-session messaging**: `ListAgents` and `SendMessage` between your sessions, over a per-session Unix socket on one machine and through Anthropic's servers to sessions on other machines that run Remote Control; plain text only; a message from another session "never counts as your consent" and cannot change configuration; `notify_when_idle` gives one notice when a local session next goes idle, dropped after 12 hours [25].
- **Agent view**: background sessions grouped as Working, Needs input, Idle, Completed, Failed, Stopped, with `claude agents --json` for scripts; machine shutdown stops running sessions, which restart from their saved conversation when replied to [13].
- **Channels** (research preview): an MCP server that pushes events into an already-running session, two-way, with a sender allowlist; a channel that declares permission relay lets "anyone who can reply through the channel" approve tool use [27].
- **Scheduling**: cloud scheduled tasks (formerly routines) with schedule, API and GitHub triggers, minimum interval one hour, a fresh clone with no local files, 100 scheduled runs per hour per account; the API's `text` arrives wrapped as untrusted data and a fired prompt "can't act as approval or consent" [26]. Desktop scheduled tasks run locally; `/loop` and `CronCreate` are session-scoped, jittered by up to 30 minutes, expire after seven days and do not catch up missed fires [31].
- **Agent teams** (experimental): a lead, teammates, a shared task list (pending, in progress, completed, with dependencies shown as blocked) claimed under file locks, and per-agent mailbox files; hooks `TaskCreated`, `TaskCompleted`, `TeammateIdle` can veto with exit code 2; one team per session, no nesting, in-process teammates are not restored on resume [2].
- **Remote Control**: drive a local session from the phone app; "Push when actions required" sends permission prompts and questions to the phone; the machine must stay on [22].

The consequence for the build: the *transport* (deliver a message into a session, push a notification to the phone, wake on a schedule) is increasingly a platform feature. What no platform feature provides is the *desk*: a durable, principal-owned record of items across sessions and machines, with a gate, a decision log and a brief. That is where the layer's own code should be.

## A. Coordination patterns: board, bus or orchestrator

Three reference shapes recur. In a **hierarchical orchestrator** a manager agent plans, delegates and validates each step: CrewAI's hierarchical process ("a manager agent coordinates the workflow, delegates tasks, and validates outcomes") [7], LangGraph's supervisor library (supervisors of supervisors) [8], Magentic-One's Orchestrator with an outer-loop *task ledger* (facts, guesses, plan) and an inner-loop *progress ledger* that re-plans when progress stalls [9], and OpenAI's agents-as-tools, where "a manager calls specialists and keeps control" [32]. Anthropic calls the same thing *orchestrator-workers* and advises starting with the simplest pattern that works [33]. In a **bus** (publish/subscribe), agents post to a shared stream and subscribe to what concerns them: MetaGPT's shared message pool, where roles subscribe to relevant structured outputs instead of waiting on each other [34]; LangChain's *ambient agents*, which "listen to an event stream and act on it" [35]. In a **board** (the classic *blackboard*), work items sit in a shared store and agents claim what is ready: two 2025 papers report a blackboard system competitive with the best static and dynamic multi-agent systems at lower token cost [36], and 13 to 57% relative gains in end-to-end success over the best baseline, which included a master-slave design [37]; Claude Code's agent teams use a task list with lock-protected self-claim [2]; Beads exposes `bd ready` (no open blockers) and an atomic `--claim` [4]; Paperclip tracks work as tickets with atomic checkout under a strict org-chart tree [6].

| Pattern | Who decides the next unit of work | Where state lives | Typical failure | Examples | Fit for many independent sessions under one slow principal |
|---|---|---|---|---|---|
| Hierarchical orchestrator | the manager agent, every step | the manager's context (plus checkpoints) | manager context saturates; single point of failure; paraphrase loss | CrewAI hierarchical, LangGraph supervisor, Magentic-One, OpenAI agents-as-tools | poor across sessions; fine *inside* one corpus (subagents) |
| Bus (pub/sub) | each subscriber | the stream; consumers keep watermarks | no single place to ask "what is open?"; replay and dedup needed | MetaGPT message pool, ambient agents, crowsnest watch | good for *notification*, poor as system of record |
| Board (blackboard / task list) | workers claim; a person or lead assigns | the board's records | stale claims; status lag ("teammates sometimes fail to mark tasks as completed" [2]) | agent teams, Beads/Gas Town, Paperclip, Agent Mail | best as system of record |
| Board + bus (hybrid) | board for state, bus for change, claims with leases | board; bus is provenance | needs one-writer discipline | Gas Town (beads + mail + hooks) [3], Agent Mail (inbox + file leases) [5] | **recommended** |

The recommended split is therefore: **orchestration stays local** (inside a corpus session, using subagents or an agent team, which the platform already provides), **coordination across corpora is a board**, and **change is announced on a bus**. The "managers by concern" of phase 1 are reviewers that annotate board items, not routers of control flow; giving them control would rebuild the in-process hierarchy at fleet scale, which is the context-bottleneck failure that the owner's own lookout rules (hold the roster and nothing else; hand a thread off early) were written against. Two ideas from the hierarchical systems are worth keeping as data rather than as an agent: Magentic-One's *task ledger* is what a per-corpus ledger already is, and its *progress ledger* is a **stall detector**, which Gas Town also has (a per-project Witness that detects stuck agents and reports "hooked work with no progress for an extended period") [3]. In the desk, a stall is a rule over item timestamps, and its output is a new item.

## B. Durable execution engines: does the organisation layer need one?

*Durable execution* means the engine records each step's result so a crashed or suspended workflow resumes where it stopped; waiting for a human costs nothing while suspended. Each engine has a primitive for "wait for an external decision".

| Engine | Licence (server) | Runs as | Wait-for-human primitive | Notes | Verdict for v1 |
|---|---|---|---|---|---|
| Temporal | MIT [38] | server cluster or Temporal Cloud | Signals and Updates into a running workflow; timers for reminders [39] | history capped at 51,200 events or 50 MB, warning at 10,240 / 10 MB [10]; agent loops need Continue-As-New | study; too heavy |
| Inngest | SSPL, converting to Apache 2.0 after three years (secondary source) [38] | server (self-host or cloud) | `step.waitForEvent()` with timeout; chained for sequential gates [40] | events sent before the wait starts are missed (community note, unverified) | avoid (licence, server) |
| Trigger.dev | Apache 2.0 (secondary) [38] | server (self-host or cloud) | waitpoint token: `wait.createToken` (with `idempotencyKey`), `wait.forToken`, complete by API or callback URL; status WAITING / COMPLETED / TIMED_OUT [11][15] | TypeScript | **borrow the token shape** |
| Restate | BSL 1.1, converting after four years (secondary) [38] | server | awakeables and durable promises, resolved by id over HTTP; timer and promise both persisted [12] | | study |
| Hatchet | MIT (README badge) [41] | server + Postgres | durable event waits; the waiting task is evicted and its slot freed, then replayed from checkpoint [41] | | study |
| DBOS Transact | MIT [42] | a library inside your process, state in Postgres (SQLite for Python not confirmed) | `DBOS.send()` / `DBOS.recv()` durable notifications [42] | no separate server | **first candidate if one is ever needed** |

**Verdict: no engine in v1.** Four reasons. (1) The durable unit here is a Claude Code session, which is not deterministic replayable code; engines replay workflow code and treat model calls as side-effecting activities, so wrapping a session would mean re-architecting it [39]. (2) The session already persists: background sessions restart "from their saved conversation" when replied to [13], and a resumed conversation is how A2A, LangGraph and the OpenAI SDK all resume: same thread or task id, new input [14][18][19]. (3) Every engine except DBOS adds a server, and two of six (Inngest, Restate) have source-available licences that open only after a delay. (4) The work that genuinely needs exactly-once side effects (a merge, a sent message, a spend) is already a single atomic act performed by one session; it needs an idempotency key, not a workflow history. This is a judgment from the cited properties, not a measured result.

**What to borrow now.** The waitpoint token as a record shape: `{id, item, status: waiting|completed|timed_out, timeout_at, default_on_timeout, idempotency_key, completed_by, output}`. **When to revisit** (stated as a kill criterion for the no-engine choice): the first multi-step flow whose intermediate side effects must not repeat after a crash and cannot be made idempotent per step, for example a purchase that reserves, confirms and records. Then try DBOS in-process before any server.

## C. Orchestration frameworks with manager roles: what each offers

| Framework | Manager / hierarchy mechanism | Human-in-the-loop primitive | How it resumes | What to borrow |
|---|---|---|---|---|
| LangGraph | supervisor graphs, nested supervisors [8] | `interrupt(payload)` in a node | checkpointer + same `thread_id` + `Command(resume=value)`; the value becomes `interrupt()`'s return [18] | the "same thread, new input" resume contract |
| LangChain Agent Inbox | (UI) | `HumanInterrupt{action_request{action,args}, config{allow_accept, allow_edit, allow_respond, allow_ignore}, description}` | `HumanResponse{type: accept/edit/response/ignore, args}` [23] | **the approval-card schema**; note the repo was archived on 2026-09-20 [23] |
| OpenAI Agents SDK | handoffs (`transfer_to_<agent>`, specialist takes over) vs agents-as-tools (manager keeps control) [32] | `needsApproval` on a tool, run-wide including handed-off and nested agents | serialise `RunState`, later `approve()`/`reject()` and re-run; store a version with the state [19] | version your serialised state |
| Claude Agent SDK | subagents (`AgentDefinition`) | `canUseTool` callback after hooks, deny, ask, mode and allow rules; `always_ask` tools; MCP `requiresUserInteraction` [20] | session resume | the evaluation order: deterministic rules first, a human last |
| Claude Code agent teams | lead (fixed), teammates, shared task list, mailbox [2] | plan-approval requests to the lead; permission prompts bubble to the lead | in-process teammates not restored on resume | hooks that veto task completion (`TaskCompleted` exit 2) as the reviewer seam |
| CrewAI | `Process.hierarchical` with `manager_llm` or a custom `manager_agent` [7] | (task-level human input) | in-process | nothing structural |
| AG2 / AutoGen lineage | `GroupChat` with `speaker_selection_method` auto, manual, random, round_robin or a callable; allowed transitions [43]; Microsoft folded AutoGen and Semantic Kernel into the Microsoft Agent Framework with Magentic-style orchestration [44] | `manual` speaker selection | in-process | explicit transition constraints |
| MetaGPT | SOPs encoded in prompts; roles subscribe to a message pool [34] | none central | in-process | **an SOP per item kind** (what a review, a merge, a reminder must contain) |
| Magentic-One | Orchestrator with task and progress ledgers [9] | none central | re-plan on stall | stall detection as a rule |
| Paperclip | strict org-chart tree, CEO agent, heartbeats, monthly budgets that hard-stop, "the human acts as the board" with approval workflows and audit log [6] | board approvals | heartbeat wake | per-agent budget as a field; details partly from secondary sources |
| Gas Town | Mayor coordinator, per-project Witness (stall), Refinery (Bors-style merge queue), Deacon (patrol) over Beads [3] | the operator via the Mayor | git-backed hooks survive restarts | the merge queue and stall roles as named jobs |

Every framework above except Paperclip, Gas Town and Agent Mail is an *in-process* orchestrator: one program owns all agents. None of them spans independent CLI sessions on three machines; for that the relevant prior art is the board-plus-mail systems and Claude Code's own cross-session messaging. The 12-factor-agents essay names the two principles this layer depends on: "Launch/Pause/Resume with simple APIs" and "Contact humans with tool calls" [45].

## D. Inter-agent protocols: what each is for

| Protocol | Connects | Shape | Status (late 2026) | Use here |
|---|---|---|---|---|
| MCP | an agent to tools, resources, prompts ("vertical") [46] | JSON-RPC; elicitation asks the user mid-call; *tasks* (call now, fetch later) added experimentally in 2025-11-25, where elicitation still blocked, and reworked into an extension with `tasks/get`, `tasks/update`, `tasks/cancel` in 2026-07-28 (secondary sources) [21][47] | governed by the Linux Foundation's Agentic AI Foundation since 2025-12-09 [48] | expose the desk's verbs to sessions as `propose_*` tools; do not use elicitation for the principal |
| A2A | opaque agents to each other ("horizontal") [46] | Agent Card; tasks with states SUBMITTED, WORKING, INPUT_REQUIRED, AUTH_REQUIRED, COMPLETED, FAILED, CANCELED, REJECTED; resume by a new message with the same task and context id; push notifications by webhook [14] | v1.0.0, Linux Foundation [14] | **adopt the task-state vocabulary now**; an A2A endpoint is a later seam if a non-Claude agent joins |
| ACP (IBM, Agent Communication Protocol) | agents to agents | REST | merged into A2A in 2025; development wound down [49] | none |
| ACP (Zed, Agent Client Protocol) | an editor to a coding agent | JSON-RPC over stdio; Claude Code via an adapter [50] | active | none (it is an editor protocol); beware the name clash |
| AG-UI | an agent to a front end | event stream: run lifecycle, text, tool calls, `StateSnapshot` / `StateDelta` (JSON Patch), activity, subagent events; a run can finish with an interrupt outcome and the client resumes with a `resume` array [51] | active (CopilotKit and partners) | the wire format if the desk gets a live zodal/acture front end |
| Claude Code cross-session messaging | your sessions to each other | plain text; local socket or via Anthropic servers [25] | shipped | the delivery path for decisions into sessions |
| Claude Code channels | outside events into a running session | an MCP server that pushes [27] | research preview | a webhook or phone-bridge into the secretary's session |

Two cautions. "ACP" names two unrelated protocols, so the code should never use the bare acronym. And the platform's cross-machine messages pass through Anthropic's servers [25]; for private content the desk record, not the message, should carry the substance, with `isolatePeerMachines` available to require approval before anything leaves a machine [25].

## E. Human-in-the-loop primitives and how they resume

The field's primitives fall into four kinds, which differ in what is suspended and for how long.

| Kind | Examples | What is suspended | Suited to |
|---|---|---|---|
| Blocking interrupt | LangGraph `interrupt()` [18], OpenAI `needsApproval` [19], Agent SDK `canUseTool` [20], MCP elicitation [21], a Claude Code permission prompt | a live process or turn | seconds to minutes, someone at the screen |
| Durable wait token | Trigger.dev waitpoints [11], Restate awakeables [12], Inngest `waitForEvent` [40], Temporal signals [39] | a workflow, at no compute cost | hours to days, inside an engine |
| Approval queue / inbox | Agent Inbox [23], HumanLayer `require_approval` routed to Slack or email [52], Paperclip board approvals [6], the openloops phone board [16] | nothing: the item waits, the agent has moved on | hours to days, a principal on a phone |
| Proposal then confirm | an assistant drafts the action as a card the principal confirms; the owner's existing voice manager works this way (actions only as proposals confirmed on a card) | nothing | conversational surfaces (chat, voice) |

**Resumption is uniform across the field**: keep the identifier of the waiting thing, deliver the human's answer as new input addressed to it. LangGraph requires the same `thread_id` [18]; OpenAI restores `RunState` and re-runs from the root agent [19]; A2A sends a new message with the same task id [14]; AG-UI starts a new run with a `resume` array covering every open interrupt [51]. For this fleet that means: the desk item records `session` and `item_id`; the decision is delivered as a message into that session (cross-session messaging or the courier's intent line) [25][29]; if the session has ended, a new session is dispatched with the item as its brief.

**Approval is a security boundary, not a pause.** An approval must come from the principal's authenticated surface. Claude Code encodes this: a message from another session "can't approve anything" [25], a fired scheduled-task prompt "can't act as approval or consent" [26], and a teammate "can't approve a permission prompt or supply consent on your behalf" [2]; conversely a channel's permission relay hands that authority to every allowlisted sender [27]. One vendor argues that `needsApproval` and `interrupt()` are holds, not policies, because they do not screen what started the run [53]; that is an opinion, but it agrees with the design: the gate's reversibility class is a rule, and only the principal role may write a decision. The openloops board store already enforces role-based writers [16].

**The approval card.** Merge the Agent Inbox schema [23] with phase 1's exception item [1]: `action_request {action, args}`, `description`, `recommendation`, `predicted_objection {risk, coverage, reason, sources}`, `reversibility` (rule class), `allowed_responses` (subset of `accept`, `edit`, `respond`, `ignore`, `later`), `expires_at`, `on_expiry` (default action, normally "stay open"), `idempotency_key`. The response is `{type, args, by, at, surface, item_revision}`.

## F. Scheduling, wakeups and idempotency

| Mechanism | Runs where | Needs machine on | Survives restart | Minimum interval | Missed runs | Use here |
|---|---|---|---|---|---|---|
| launchd / systemd timer / cron | each machine | yes | yes | seconds to a minute | none caught up by default | **the tick** (already used by the fleet's jobs) |
| Claude Code `/loop`, `CronCreate` | inside one session | yes | restored on resume, expires after 7 days | 1 minute, jitter up to 30 min | fires once when idle, no catch-up [31] | ad hoc polling inside a corpus only |
| Desktop scheduled tasks | local | yes | yes | 1 minute (1 hour in the forms) [31] | | optional |
| Cloud scheduled tasks | Anthropic cloud | no | yes | 1 hour; API and GitHub triggers [26] | | machine-independent briefs and digests over GitHub data |
| Channels | into a running session | yes | no | event-driven [27] | events only while the session is open | push a webhook into the secretary's session |
| `notify_when_idle` | local sessions | yes | no | one-shot, 12 h expiry [25] | | "tell me when this dispatch is done" |

**Shape: deterministic tick first, model second.** Each machine runs one tick that computes what is due from data (reminders, timeouts, stalls, new intents) and wakes a model session only when judgment is needed. crowsnest's courier is already this shape ("a session is woken only for an intent that needs judgment") [29]. It keeps cost proportional to decisions, not to events, and keeps the schedule correct when sessions are down.

**Catch-up is the tick's job.** Because cron does not catch up and `/loop` fires once after a gap [31], the tick must compute "what became due since my last watermark", not "what is due this minute". The watermark is stored per machine in the desk.

**Idempotency everywhere, because delivery is at-least-once.** An outbox relay that crashes after publishing republishes on restart, so consumers must be idempotent [54]; GitHub does not automatically redeliver failed webhooks and keeps `X-GitHub-Delivery` stable across manual redeliveries [55]; CloudEvents lets a consumer "assume that Events with identical `source` and `id` are duplicates" [24]; Trigger.dev deduplicates token creation by `idempotencyKey` [15]. For the desk: every intent carries `idempotency_key = hash(item_id, item_revision, action)`; the handler records the key in the same write as its effect (the board store's create-once outbox already returns success for an identical retry and 409 for a conflicting one [16]); and a decision made on an outdated revision is refused, not applied, so a slow phone cannot approve something that has since changed. Time zones: cloud cron hours are UTC unless prefixed `CRON_TZ=` [26]; store all times as UTC with the principal's zone as a setting.

## G. The state model: source of truth, writers, overrides

**Record types and their single writable home** (the rule already adopted for openloops: no record type writable in two places):

| Record | Source of truth | Created by | May change it | Never |
|---|---|---|---|---|
| Item (desk entry) | local document store; or the GitHub issue when the item belongs to one repo (a `manual-task` issue is the item) | a worker session, the scheduler, a reviewer, the secretary | its raiser (content), reviewers (the `review` annotation), the representative (the `risk` annotation), the gate (`route`), the tick (`state` on timeout or stall) | an agent writing `decision` |
| Decision | append-only decision log | the principal's surface only (or the gate, for auto-approved items, marked `by: gate`) | nobody (corrections are new decisions that supersede) | edited in place |
| Intent (an action to carry out) | the outbox | the principal's surface, or the gate | immutable; identical retry is a no-op [16] | |
| Receipt | the outbox's receipts | the session that acted | | written by the page |
| Event | per-machine append-only log | any component | nobody | used to derive current state |
| Wakeup / reminder | the desk | the scheduler, the secretary | the principal (snooze), the tick | |

The decision log is a separate record type, not a history folded into the item: the item's current state stays in first-class fields (so an export is one list), and the decision log is the labelled data phase 1 needs for recalibrating the representative [1]. Each decision row: `item_id`, `item_revision`, `route` at the time, `recommendation`, `risk`, `response.type`, `response.args`, `by`, `surface`, `at`, `idempotency_key`, `supersedes`.

**GitHub issues, a local store, or a database.**

| Store | Strengths | Weaknesses | Role |
|---|---|---|---|
| GitHub issues | durable, phone-readable, linkable, already where repo work lives | no `If-Match` on issue body writes, so concurrent writers clobber; search API capped (both recorded in openloops' design work, not independently re-verified here); public repos leak private context | items with a natural repo home only |
| Local JSON document store with versions (the board store) | already built; `If-Match`; role-checked writers; mirrors to a phone page [16][29] | one writer per collection needed across machines; no query language | **v1 desk** |
| SQLite | transactions, queries | per-machine; replication needed | an index over the document store if queries grow |
| Beads (Dolt) | hash ids that avoid merge collisions, dependency graph, `ready` query, atomic claim, cross-machine sync via a git remote [4] | a new dependency; "single writer" in embedded mode [4] | **the named upgrade** if several machines must write the same collection |
| Postgres (or a durable-execution engine's store) | multi-writer, transactions | a server to run and back up | not justified at one principal's scale (estimate) |

Across three machines, give each collection **one writer of record** (for example, the machine that runs the lookout session), and let other machines contribute by pushing intents and events into it, which the courier pattern already does [29]. Last-write-wins by wall-clock time is unsafe across machines with skewed clocks; use document versions, as the board store does [16].

## H. Recommended reference shape

```
   phone (desk page, push, voice adapter)         brief (cadence)
            │ intents (idempotent)                    ▲ reads
            ▼                                         │
   ┌────────────────────── DESK (data) ──────────────────────────┐
   │ items · decisions · intents/receipts · wakeups · watermarks  │
   └──────▲───────────────▲───────────────▲──────────────▲───────┘
          │ propose_*     │ annotate      │ route        │ due / timeout / stall
   worker sessions   reviewers +      GATE (rule +     TICK (deterministic,
   (corpus agents)   representative   one risk call)   one per machine)
          ▲                                                    │
          └──── decision delivered as a message (same item id) ┘
                       EVENT STREAM (data): CloudEvents-shaped, provenance only
```

| Component | Data or agent | Contract (sketch) | Home today |
|---|---|---|---|
| Desk | data | `put(collection, id, doc, *, if_match=version) -> version`; `get`; `list(collection, *, where)`; roles enforced | openloops board store [16] |
| Item | data | `{id, kind, title, ask, state (A2A names), raised_by, session, machine, links, decision_request (card), review, risk, route, revision, created, updated, due, remind_at, budget}` | openloops `Task` plus fields [30] |
| Event stream | data | append `{id, source, type, subject, time, data}`; consumers keep a watermark; never used to derive state | crowsnest event log and `crowsnest watch` [29] |
| Intent / receipt | data | intent `{id, item_id, item_revision, action, args, by, idempotency_key}`; receipt `{intent_id, outcome, at, by_session}` | crowsnest courier intents, board outbox [29][16] |
| Tick (scheduler) | deterministic code | `tick(now, *, since=watermark) -> [Wakeup]`: reminders due, waits timed out, stalls, intents to dispatch | launchd jobs, courier `tick` [29] |
| Gate | code, with one model call as input | `route(item, risk) -> auto/shown/logged`; reversibility from a rule table, never a model | phase 1 [1] |
| Representative | agent | `assess(item) -> {risk, coverage, reason, sources}` | acquaint (phase 1) |
| Reviewers | agents, fresh context, read-only | `review(item, concern) -> finding`; aggregated to one exception item | phase 1 [1] |
| Dispatcher | code | `deliver(intent)`: message the item's session if live, otherwise spawn a session with the item as brief | crowsnest spawn and dispatch, cross-session messaging [25] |
| Secretary (lookout) | agent, **not in the control path** | reads the desk, composes briefs and proposals, relays; holds no state of its own | crowsnest lookout |
| Surfaces | adapters | phone page, push (Remote Control push or an ntfy-style channel), voice, brief; all write only intents | crowsnest report, openloops board, annals, correspond |

The item lifecycle uses A2A's names, so an external A2A agent would need no translation: `submitted` (raised) → `working` (being reviewed or acted on) → `input-required` (needs the principal: the one list that matters on the phone) → `completed` | `failed` | `canceled` | `rejected`. The principal's response types (`accept`, `edit`, `respond`, `ignore`, `later`) are recorded in the decision log, not as states; `later` sets `remind_at` and leaves the state as it was.

## I. Minimum viable version, given what the fleet already has

Each step is a small addition at a seam that already exists; none needs a new server or a new package (consistent with the phase-1 verdict of no new package).

1. **Item schema.** Extend openloops' `Task` with `state` (A2A names), `session`, `revision`, `review`, `risk`, `route`, `due`, `remind_at`; keep `decision` as the card's `decision_request` [30].
2. **Desk collections.** In the existing versioned board store add `items`, `decisions` (create-once, like `outbox`), `wakeups`, `watermarks` [16].
3. **Bus.** Add `id`, `source`, `type`, `subject` and `time` to the lines crowsnest already writes to its event log, so consumers deduplicate by `source + id` [24][29].
4. **Approval path.** A tap on the phone writes an intent with an idempotency key; the courier tick carries it; the dispatcher delivers it as a message to the item's session (or spawns one); the session acts and writes a receipt. All of this exists except the idempotency key and the revision check [29].
5. **Schedule.** Teach the existing per-machine tick to evaluate `wakeups` and `due` against its watermark, and to raise a stall item for any `working` item with no event for a set period (setting, not a constant).
6. **Gate.** A rule table for reversibility and the representative's call; every routed item writes a decision row, including auto-approvals [1].
7. **Notification.** Push only `input-required` items, under the daily cap from phase 1; route through Remote Control's "actions required" push where a session is the asker, and through a channel facade otherwise.
8. **Brief.** The existing overview pipeline reads the desk, not transcripts.

**Do not build in v1:** an orchestrator agent, a message broker (NATS, Redis streams), a durable-execution engine, an A2A server, a database server, or a bespoke front end before the phone page has been used. **Kill criteria** for the MVP itself: if after a month the principal still decides most items outside the desk (in a terminal, in chat), the desk is the wrong surface; if fewer than a handful of items per week reach `input-required`, the gate or the raisers are not using it.

## J. Standard terminology (so the code uses the field's words)

| Term | Meaning | Where it comes from | Use in the code as |
|---|---|---|---|
| principal | the person on whose behalf agents act | agency theory; phase 1 [1] | `principal` (never "user" for this role) |
| human-in-the-loop (HITL) | a person must act before the agent proceeds | LangGraph, OpenAI, HumanLayer [18][19][52] | the `input-required` state |
| interrupt / resume | pause for input; continue with the answer | LangGraph, AG-UI [18][51] | `decision_request` / delivering a decision |
| thread, checkpoint | the conversation id; saved state to resume from | LangGraph [18] | `session` |
| task, task state | a unit of agent work and its lifecycle | A2A [14] | `item.state` with A2A values |
| handoff vs agent-as-tool | specialist takes over vs manager keeps control | OpenAI Agents SDK [32] | dispatch vs review |
| orchestrator-workers, supervisor | a central model splits and delegates | Anthropic, LangGraph [33][8] | not used across sessions |
| blackboard | a shared store agents read and write | classic AI; [36][37] | "desk" is the house name for it |
| task ledger / progress ledger | plan and facts / per-step progress and stall check | Magentic-One [9] | ledger (exists) / stall rule |
| ambient agent | an agent driven by an event stream rather than a chat | LangChain [35] | workers raising items unprompted |
| agent inbox | a UI listing open human-agent exchanges | LangChain [23] | the desk's `input-required` view |
| waitpoint, awakeable, durable promise, signal | a durable handle that a human or system completes | Trigger.dev, Restate, Temporal [11][12][39] | `wait` record (id, status, timeout) |
| durable execution | replayable, crash-safe workflow execution | Temporal and peers [10] | not used in v1 |
| at-least-once, idempotent consumer, idempotency key | duplicates happen; handling twice equals once | microservices patterns [54] | `idempotency_key` on every intent |
| transactional outbox | write the effect and the message together, relay later | [54] | the board's `outbox` |
| event (CloudEvents) | `id`, `source`, `type`, `subject`, `time`, `data` | CNCF [24] | the event-stream line |
| watermark | the last position a consumer processed | stream processing | per-machine tick watermark |
| claim, lease | exclusive right to work an item, with expiry | Beads, Agent Mail [4][5] | `claimed_by`, `lease_until` |
| manage by exception, tolerance | show only what breaches a stated limit | PRINCE2, via phase 1 [1] | `route = shown` |
| elicitation | an MCP server asking the user for input | MCP [21] | avoided for the principal |
| Agent Card | an A2A agent's public self-description | A2A [14] | later seam |

## K. Things you did not ask that matter

1. **The platform is moving under the design.** The Claude Code features above carry version requirements from v2.1.224 to v2.1.317 and several are research previews or experimental [25][2][27][26]. Treat each as an adapter behind one keyword argument (delivery, push, schedule) with the courier and the per-machine tick as the strongest dependency-free default.
2. **The desk is an injection surface.** Item text written by a worker that read a web page or an email becomes input to the representative, the reviewers and the secretary. Apply a read-then-act rule: components that read item text get no mutating tools, and anything they want done is a new proposal through the gate. The owner's voice manager already applies such a taint rule.
3. **Agent-team state is ephemeral.** Claude Code removes a team's config when the session ends and cleans task lists by `cleanupPeriodDays` [2]; transcripts are also purged on a schedule. The desk must copy what it needs (the ask, the evidence links) rather than point into `~/.claude/`.
4. **Liveness is not progress.** A session can be `Working` for hours without advancing an item. Measure progress on the item (events per item), as Magentic-One's progress ledger and Gas Town's Witness do [9][3].
5. **Fresh sessions need a bearings file.** Anthropic's guidance for long-running agents is a progress file, a JSON feature list agents may only tick, and git history read at the start of every session [56]. A dispatched item's brief should follow that shape: what the item is, its state, what has been tried, the acceptance line.
6. **UI products churn; schemas last.** LangChain archived Agent Inbox in September 2026 [23]. Own the item and card schema; treat any inbox UI as replaceable.
7. **Budgets are a field, not a feeling.** Paperclip gives each agent a monthly budget that hard-stops it [6]; Claude Code scheduled tasks have hourly run caps that drop excess GitHub events [26]. Put `budget` on items and on raisers so a runaway raiser is itself an exception item.

## What this means for the build

- The organisation layer is a **desk + event stream + per-machine tick**; the approval queue is the desk's `input-required` view. No orchestrator agent, broker, engine or database server in v1.
- Extend openloops' `Task` and board store (states in A2A names, revision, review, risk, route, due, remind_at; `decisions` as a create-once collection).
- Give crowsnest's event lines CloudEvents fields and every intent an idempotency key plus the item revision it was decided on.
- Never block a session on the principal: raise an item, end the turn, deliver the decision later as a message to the same session (or spawn one).
- Approvals are written only by the principal's surface or by the rule-based gate; messages, routine payloads and channel senders never approve.
- Borrow the Agent Inbox response types and the waitpoint-token shape; keep Beads (Dolt) as the upgrade if several machines must write one collection, and DBOS as the first engine if exactly-once multi-step flows appear.
- Wrap Claude Code's delivery, push and scheduling features as adapters with the existing tick and courier as defaults.

## REFERENCES

1. [A principal model and a review organisation for a solo operator of many coding agents (equerry research, 2026-11-09)](principal-model-and-review-organisation.md)
2. [Claude Code docs: Orchestrate teams of Claude Code sessions (agent teams)](https://code.claude.com/docs/en/agent-teams)
3. [Gas Town: multi-agent workspace manager (GitHub README)](https://github.com/gastownhall/gastown)
4. [Beads: a distributed graph issue tracker for coding agents (GitHub README)](https://github.com/gastownhall/beads)
5. [MCP Agent Mail (GitHub README)](https://github.com/Dicklesworthstone/mcp_agent_mail)
6. [Paperclip: open-source orchestration for agent companies (GitHub; details partly from its docs and secondary write-ups)](https://github.com/paperclipai/paperclip)
7. [CrewAI docs: Hierarchical process](https://docs.crewai.com/en/learn/hierarchical-process)
8. [langgraph-supervisor (PyPI)](https://pypi.org/project/langgraph-supervisor)
9. [Microsoft Research: Magentic-One, a generalist multi-agent system for solving complex tasks](https://www.microsoft.com/en-us/research/articles/magentic-one-a-generalist-multi-agent-system-for-solving-complex-tasks/)
10. [Temporal docs: Workflow Execution limits](https://docs.temporal.io/workflow-execution/limits)
11. [Trigger.dev docs: Wait for token](https://trigger.dev/docs/wait-for-token)
12. [Restate docs: Human-in-the-loop pattern for agents](https://docs.restate.dev/ai/patterns/human-in-the-loop)
13. [Claude Code docs: Agent view and background sessions](https://code.claude.com/docs/en/agent-view)
14. [A2A Protocol specification (v1.0.0)](https://a2a-protocol.org/latest/specification/)
15. [Trigger.dev docs: Create a waitpoint token (idempotencyKey)](https://trigger.dev/docs/management/waitpoints/create)
16. [openloops source: board_store.py (board store rules: roles, If-Match, immutable outbox)](https://github.com/thorwhalen/openloops/blob/main/openloops/board_store.py)
17. [crowsnest source: attention.py (seen/later/done per item, revision fingerprint)](https://github.com/thorwhalen/crowsnest/blob/main/crowsnest/attention.py)
18. [LangGraph docs: Interrupts](https://docs.langchain.com/oss/python/langgraph/interrupts)
19. [OpenAI Agents SDK (Python): Human in the loop](https://openai.github.io/openai-agents-python/human_in_the_loop/)
20. [Claude Agent SDK docs: Configure permissions](https://code.claude.com/docs/en/agent-sdk/permissions)
21. [WorkOS: MCP 2025-11-25 spec update (tasks, elicitation) — secondary](https://workos.com/blog/mcp-2025-11-25-spec-update)
22. [Claude Code docs: Remote Control](https://code.claude.com/docs/en/remote-control)
23. [LangChain: agent-inbox repository (archived 2026-09-20)](https://github.com/langchain-ai/agent-inbox)
24. [CNCF CloudEvents specification](https://github.com/cloudevents/spec/blob/main/cloudevents/spec.md)
25. [Claude Code docs: Message your other Claude Code sessions](https://code.claude.com/docs/en/cross-session-messaging)
26. [Claude Code docs: Automate work with scheduled tasks (formerly routines)](https://code.claude.com/docs/en/routines)
27. [Claude Code docs: Push events into a running session with channels](https://code.claude.com/docs/en/channels)
28. [Anthropic Engineering: How we built our multi-agent research system](https://www.anthropic.com/engineering/multi-agent-research-system)
29. [crowsnest source: courier.py (the console's courier tick, intents, heartbeat)](https://github.com/thorwhalen/crowsnest/blob/main/crowsnest/courier.py)
30. [openloops source: tasks.py (the Task record)](https://github.com/thorwhalen/openloops/blob/main/openloops/tasks.py)
31. [Claude Code docs: Run prompts on a schedule (/loop, CronCreate, comparison of scheduling options)](https://code.claude.com/docs/en/scheduled-tasks)
32. [OpenAI API docs: Orchestration and handoffs](https://developers.openai.com/api/docs/guides/agents/orchestration)
33. [Anthropic: Building effective agents](https://www.anthropic.com/engineering/building-effective-agents)
34. [Hong S. et al. MetaGPT: Meta Programming for a Multi-Agent Collaborative Framework. ICLR 2024 (arXiv:2308.00352)](https://arxiv.org/abs/2308.00352)
35. [LangChain blog: Introducing ambient agents](https://blog.langchain.com/introducing-ambient-agents)
36. [Han B., Zhang S. Exploring Advanced LLM Multi-Agent Systems Based on Blackboard Architecture (arXiv:2507.01701, 2025)](https://arxiv.org/abs/2507.01701)
37. [Salemi A. et al. LLM-based Multi-Agent Blackboard System for Information Discovery in Data Science (arXiv:2510.01285, 2025)](https://arxiv.org/abs/2510.01285)
38. [Temporal vs Inngest vs Restate vs DBOS: durable execution for agents (secondary comparison; licences not checked against each LICENSE file)](https://www.beri.net/article/temporal-vs-inngest-vs-restate-vs-dbos-durable-execution-agent-cost)
39. [Temporal blog: Durable, flexible multi-agent systems](https://temporal.io/blog/durable-flexible-multi-agent-systems)
40. [Inngest docs: Human in the loop (step.waitForEvent)](https://www.inngest.com/docs/ai-patterns/human-in-the-loop)
41. [Hatchet docs: Durable event waits (and README licence badge)](https://docs.hatchet.run/v1/durable-event-waits)
42. [DBOS Transact for Python (GitHub)](https://github.com/dbos-inc/dbos-transact-py)
43. [AG2 docs: GroupChat](https://docs.ag2.ai/latest/docs/user-guide/advanced-concepts/groupchat/groupchat/)
44. [microsoft/autogen discussion #7066: AutoGen update (move to Microsoft Agent Framework)](https://github.com/microsoft/autogen/discussions/7066)
45. [HumanLayer: 12-factor agents](https://github.com/humanlayer/12-factor-agents)
46. [A2A Protocol: A2A and MCP](https://a2a-protocol.org/latest/topics/a2a-and-mcp/)
47. [Agentic AI Foundation blog: Handle long-running MCP tool calls with the MCP tasks extension — secondary](https://aaif.io/blog/handle-long-running-mcp-tool-calls-with-the-mcp-tasks-extension)
48. [Linux Foundation: Announcing the formation of the Agentic AI Foundation (2025-12-09)](https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation)
49. [IBM Research: Agent Communication Protocol (now part of A2A)](https://research.ibm.com/projects/agent-communication-protocol)
50. [Zed: Agent Client Protocol](https://zed.dev/acp)
51. [AG-UI docs: Events](https://docs.ag-ui.com/concepts/events)
52. [HumanLayer docs: require_approval](https://humanlayer.dev/docs/core/require-approval)
53. [Arcjet: needsApproval and interrupt are not a policy (vendor opinion)](https://arcjet.com/learn/needsapproval-and-interrupt-are-not-a-policy)
54. [microservices.io: Pattern: Transactional outbox](https://microservices.io/patterns/data/transactional-outbox)
55. [GitHub docs: Best practices for using webhooks](https://docs.github.com/en/webhooks/using-webhooks/best-practices-for-using-webhooks)
56. [Anthropic Engineering: Effective harnesses for long-running agents (2025-11-26)](https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents)

All URLs accessed 2026-11-09. Items marked secondary were not checked against a primary source; licence entries in section B for Inngest, Trigger.dev and Restate rest on a secondary source.
