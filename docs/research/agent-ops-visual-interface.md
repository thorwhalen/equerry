# The desk: a phone-first visual interface for one principal operating a fleet of agents

Research report, deep tier, 2026-11-09. Theme: the visual interface ("mission control") through which one person supervises many coding and organisation agents, designed for a phone and independent of any voice layer. No private material was used; products are described from their public documentation, and where only secondary coverage was available the text says so.

## Summary and recommendation

**The main finding.** Every product that shows many agents to one person has converged on the same first screen: a list of agent sessions grouped by state, with a "needs input" group near the top, a generated one-line summary per row, a tap that drills into the transcript, and a push notification when a session needs input or finishes. Claude Code's agent view [1], Devin's "Group by status" [9], GitHub's mission control [11], Linear's agent sessions [14] and the OpenClaw Control UI [16] all have this shape. The products that go one step further stop listing *agents* and list *decisions*: Paperclip's Decisions page is "a ranked queue of work that needs a human call", where every row says "why it needs you now", most rows can be completed in place, and the empty state reads "You're all caught up" [20]. That second shape is the one the human-factors literature supports: a supervisor of many automated workers should be shown exceptions, not the workers [42][44][49], under an alarm budget [44], with the automation's failures made obvious [38].

**Recommendation.** Build the desk around one object, the **item** (a request for the principal's attention, produced by any agent or manager), and one list, **Needs you**, ranked and budgeted. Sessions and agents are a secondary screen (the roster), reached by drilling down from an item, never the front page. Concretely:

1. **One list that matters: Needs you.** Every row is an exception report in miniature: the ask in one line, why now, the recommendation and the predicted objection, whether the action is reversible, how old the claim is, and two or three verbs that complete it in place. Nothing that does not need the principal is on it; the empty state says so and says when the desk last checked its sources.
2. **Three screens, not ten.** *Desk* (the list), *Fleet* (the roster as a grey-unless-abnormal overview, ISA-101 style [49]), *Log* (what was decided, by whom, including what the representative auto-approved). Item detail and the transcript are drill-downs, not tabs.
3. **The organisation layer emits items, verbs and events as data, never pages.** A Zod-describable `Item`, a closed set of `Verb`s, and an append-only event log are the whole contract. The phone page, the existing crowsnest report and openloops board, a push notification with action buttons, and later a voice adapter are all renderers of the same data and senders of the same verbs.
4. **Notifications are a budgeted channel with severity tiers mapped to the phone's own levels.** Only one-way-door or time-bound items interrupt (iOS `timeSensitive` [32], ntfy priority 4-5 [35]); everything else is passive and batched into the list and the briefs.
5. **Voice attaches as an adapter over the same verbs.** Speech in becomes a `VerbInvocation`; speech out is a template over an `Item`. Anything irreversible is confirmed on a card on screen, as the owner's existing voice manager already does.

**Biggest risks.** The desk becomes a feed that the principal stops reading (alarm fatigue: alarm overrides of 49-96% are what an unfiltered stream produces, per the phase-1 report [51]); the principal rubber-stamps annotated items (automation bias, errors of commission [45]); the desk looks calm because a producer silently died (Bainbridge's "automatic systems should fail obviously" [38]); and the same obligation appears three times because three producers saw it (no deduplication key).

## How the desk relates to what already exists

Three phone surfaces already exist in the fleet and are not redesigned here.

- **crowsnest `report`** is the live roster: one phone-readable page, organised as "Needs you first, then Safe to close, then what is working and what is quiet", with every item stamped with *when its words were said* and marked stale past a threshold, attention verbs (`seen`, `later`, `done`, `note`) keyed by session id, and links to the session on claude.ai where Remote Control is on [50]. It is about *sessions that are running*.
- **openloops' phone board** is a task board built from open items that ask something of the owner (`manual-task` issues, "Decide…" issues, PRs awaiting review): each record has the ask in one line, where it is done (tap, browser, session, physical, mac-only), minutes, a `decision` of `{question, options, recommendation}`, and a paste-ready prompt; taps become GitHub actions only through a receiving session [50]. It is about *obligations that outlive sessions*, and its rule that `unknown` is never collapsed into "fine" or "alarming" is the right rule for the desk too [50].
- **annals** is the in-tray of published documents (reports, audio), read on the phone with comments.

**How a desk relates to them.** The desk is not a fourth page beside these; it is the *index above them*. The organisation layer turns a crowsnest "waiting" row, an openloops `Task`, a manager's review verdict or a reminder into the same `Item` record, deduplicated by a key (the GitHub URL, the session id plus question). The desk lists items; tapping through lands on the existing roster row, board record or annals document, which remain the drill-down screens and keep their own verbs. The data shapes in section 7 are deliberately supersets of openloops' `Task` and crowsnest's row (`said_at`, `said_at_basis`, attention record), so both packages become *producers* of items with a thin adapter rather than being rewritten. The desk adds three things neither has: ranking across sources under one budget, the representative's predicted objection and reversibility on every item, and a log of what was decided automatically.

## 1. How existing products present many agents to one operator

### Claude Code: agent view, Remote Control, cloud sessions

- **Agent view** (research preview, reported launched 11 May 2026; launch date from secondary coverage [1]) is a terminal dashboard of background sessions grouped as **Pinned, Ready for review, Needs input, Working, Completed**. "Ready for review" means a session has an open pull request awaiting review or failing checks; "Completed" collects finished, failed and stopped sessions. Each row has a state icon, the name, a **one-line summary written by a Haiku-class model** saying "what the session is doing, what it needs, or what it produced" (a blocked row shows the question it is asking), the age, and a PR label coloured by state. **Peek** opens the full question and a "waiting 3m" line and lets you reply without attaching; **attach** opens the full session and posts a recap of what happened while you were away. A permission prompt cannot be answered from a peek; you must attach. While open, it notifies when a background session starts needing input, finishes or fails. `claude agents --json --all` exposes `state` in `working | blocked | done | failed | stopped` and `waitingFor` such as `permission prompt` [1].
- **Remote Control** makes a local session reachable from claude.ai/code and the Claude app; sessions are listed by name in the Code tab with a green dot when the local process is online. **Push notifications** arrive "when a long-running task finishes or when Claude needs a decision from you", with two switches only ("Push when Claude decides", "Push when actions required") and "no per-event configuration". Pushes are suppressed while you are typing in the terminal, and a presence file can extend that to "any time you are at the machine". Permission prompts and questions stay open until answered; other forwarded dialogs expire after five minutes by default [2].
- **Cloud sessions** live in a sidebar at claude.ai/code with archive, delete and a Changes card for diff review and inline comments; a session that asks a question waits for the answer until its environment expires [3].
- **What to take.** The state vocabulary, the generated one-line summary per row, peek-and-reply without attaching, the recap on attach, and the presence-aware push suppression. **What it lacks for this project:** it lists sessions, not decisions; it has no cross-session ranking, no notion of reversibility, and no organisation layer between the sessions and the person.

### OpenAI Codex (cloud and mobile)

- From the ChatGPT app you can "approve commands and other actions", "get notified when ChatGPT completes a task or needs your attention", "review outputs, diffs, test results, terminal output, and screenshots", "switch between connected hosts and chats" and start or continue chats on the host [4]. The mobile control arrived in preview on 14 May 2026 and requires the macOS Codex app on an awake host (date and plan details from secondary coverage [6]).
- Cloud tasks each get their own workspace; you inspect changed files and check results, request follow-ups, and commit or open a PR [5]. The public docs fetched for this report do not describe a task list or its statuses; a community feature request for a "Codex Tasks" panel suggests a cross-task list was not in the mobile app at launch (unverified).
- **What to take.** Approval of a specific command as a first-class phone action; review of *artifacts* (diff, test output, screenshot) rather than transcript. **Verdict:** study.

### Cursor web and mobile agents

- Agents run in the cloud and appear in a **mobile inbox** ("Agents you start anywhere appear in the mobile inbox automatically"); a push arrives when an agent finishes a turn; **Live Activities** on the lock screen and Dynamic Island "can track up to eight agents at once"; on phone you read full diffs, commits, deployments, approvals, comments and checks [7]. Launched 30 June 2025, installable as a PWA [8].
- **What to take.** The lock-screen Live Activity as the ambient "what is working" view, so the list itself need not show working sessions at all. **Verdict:** borrow pattern.

### Devin

- The web app's session list was regrouped on 25 Sept 2026 as **Working, Ready, Blocked, Inactive**, "so sessions that need your input are listed separately from ones awaiting PR review"; on 30 Sept the sidebar began naming *the approval a session is waiting on* ("Approve skill") instead of "Approval required", and hovering a row shows a preview card with status, latest response, PRs and working directory. A notification bell (7 Oct 2026) collects session notifications, opening a session marks them read, and Settings decides which types go to the inbox and which are pushed to the phone; on 9 Oct each type got its own switch and the inbox got All/Unread tabs and per-type mute. Sidebar filters include *origin* (Slack, Web, API, Jira, Linear), and child sessions can be hidden under their parent [9]. The API's status vocabulary includes working, blocked, expired, finished, suspended [10].
- **What to take.** Separate "needs your input" from "ready for your review": they are different kinds of attention with different urgency. Name the specific ask on the row. Per-type push routing. Parent/child collapse. **Verdict:** borrow pattern.

### GitHub (agents panel, mission control, Projects)

- The agents panel is "your mission control center for agentic workflows", available on every github.com page [13]; the October 2025 mission control adds a task view to "see task status at a glance", session logs beside the PR's Overview and Files changed tabs, real-time steering through chat or a comment in Files changed, and a flag "when Copilot needs your input" [11]. Since March 2026 agent sessions appear in issues (nested under the assignee) and in **Projects table and board views**, with statuses **queued, working, waiting for review, completed**, after enabling "Show agent sessions" [12].
- **What to take.** A Projects board is a credible *read* surface for agent work that already lives in GitHub, at no build cost. **Limits:** it is per-repository or per-project, its statuses are coarse, and it has no notion of a principal's ranked attention. **Verdict:** borrow pattern (use it as a producer and a drill-down, not as the desk).

### Linear (agents as delegates)

- Assigning an issue to an agent makes it the **delegate**, not the assignee, "so humans maintain ownership while agents act on their behalf" [15]. An **agent session** is created on mention or delegation, with states **pending, active, error, awaitingInput, complete, stale**, derived by Linear from the agent's last emitted activity; activities are typed **thought, elicitation, action, response, error** (plus user-only `prompt`), an agent must acknowledge within 10 seconds or be marked unresponsive, and a session goes `stale` 30 minutes after its last activity. A session carries a `plan` of steps with statuses, and `externalUrls` give an "Open" button into the agent's own UI [14].
- **What to take.** The *delegate versus owner* distinction, the typed activity stream (an `elicitation` is exactly an item), state derived from the last activity rather than set by the agent, and a liveness contract (acknowledge in N seconds, stale after M minutes). **Verdict:** study and borrow the vocabulary.

### OpenClaw Control UI and the community "mission control" dashboards

- The official Control UI is a browser app served by the gateway, with a chat transcript (completed runs collapse under "Worked for …"), a Details panel, Changes (PRs, checks, diffs), Files, **Subagents** grouped into running and finished, Processes with a per-process Stop, a command palette, pages for Sessions, Automations, Dashboards, Usage, a sidebar with **Pinned** sessions, personal Sections and a **Snoozed** filter, presence dots (green active, amber idle), and a "Team mode" grouping sessions under agent headers. It has approval queues and approval blocks in chat, a mobile layout, PWA install and web push (iOS Safari 26.2 or later) [16]. Its licence is not stated on the page fetched (not verified here).
- "OpenClaw Mission Control" is not one product: one guide notes it "is not one canonical official product", and several unrelated community dashboards share the name (agent panels, kanban boards, cost trackers, session viewers), with licences ranging from MIT to BSL 1.1 [17]. The most-starred is an "agent orchestration and governance dashboard" (star counts from aggregators, unverified) [17].
- **What to take.** Snooze as a first-class filter; subagents grouped under their parent; approval blocks inline in the transcript. **What to avoid.** The community dashboards are mostly desktop-wide grids of panels (agents, kanban, costs, logs) built for watching, which is the opposite of an exception list; several are young, single-maintainer, and ask for broad credentials. **Verdict:** study the official UI; avoid the community dashboards as dependencies.

### Paperclip (org chart, board approvals, Decisions)

- An open-source (MIT) Node server and React UI that arranges agents in an org chart ("Your agents have a boss, a title, and a job description"), wakes them on **heartbeats**, enforces budgets and gates work behind approvals [18]. The human is "the board": docs speak of "a board user" and agents that "escalate to a board operator" [22].
- **Dashboard**: a live Agents panel, four cards (Agents Enabled, Tasks In Progress, Month Spend, Pending Approvals), three 14-day charts, Recent Activity and Recent Tasks, a red banner on budget breach; agent states Active, Running (mid-heartbeat), Error, Paused [19].
- **Org chart**: a second layout of the Agents page, "a hierarchical tree that mirrors the reporting chain", with an amber warning where a reporting chain is broken; each agent page has Assign Task, Run now, Pause/Resume and sections for governance and audit [22].
- **Approvals**: cards with a type badge, "Requested by", age, status, summary, Approve/Reject and "View details"; a third action **Request revision** sends it back without rejecting, comments under the card are read by the agent on its next wake, and the lifecycle is pending → approved / rejected / revision_requested → resubmitted [21].
- **Decisions** (`/decisions`) is the closest thing in any product to the desk recommended here: "a ranked queue of work that needs a human call" that replaces checking approvals, blocked issues, failed runs and budget warnings separately; each row says "why it needs you now"; approvals, questions and proposed decisions can be completed in place; a proposed decision carries a question, an explanation and up to eight options each describing the change it would make; cancelling an issue tree requires typing its identifier; "Dismiss — no effects" records a refusal; proposals expire after seven days; items can be given a decide-by ("Today", "This week", "Whenever") or snoozed (1 h, 4 h, tomorrow morning, next week) with an eight-second Undo; named queues (PRs, Plans, Questions) act as lanes; Decided and Expired sections sit at the bottom; the empty state is "You're all caught up" [20].
- **Verdict:** study closely and borrow the Decisions pattern (ranked, why-now, complete-in-place, request-revision, expiry, typed confirmation for destructive options). The org chart view is useful as a Fleet drill-down, not as a front page; the dashboard of charts is a desktop pattern to avoid on the phone.

### OpenHands (Agent Canvas)

- OpenHands' core is MIT except an `enterprise/` directory with its own licence [23]. **Agent Canvas** is "the open-source browser client and control center for agent conversations and automations"; it never runs agents itself, it talks to Agent Server backends (local, Docker, VM, cloud) and an Automation Server that "stores schedules and event triggers, tracks runs, and dispatches conversations", and it can drive third-party agents such as Claude Code and Codex [23]. Interface details for multiple conversations and statuses were not documented on the pages fetched.
- **What to take.** The architectural split (a UI that only renders and sends, separate servers that run and schedule) is the same split recommended here between the desk and the organisation layer. **Verdict:** study.

### Factory (Mission Control for multi-agent missions)

- Factory's web and desktop app has a **Mission Control** dashboard for multi-agent "Missions" (research preview): overall time and credits on top, a live progress log of every worker and milestone with model selectors for orchestrator, worker and validator, a main view summarising the active milestone with drill-down to a feature's validation criteria and commits, and a list of workers whose selection shows terminal output and thought process; the human control is pausing the orchestrator and asking it to re-plan [24].
- **What to take.** Milestone-level summary first, worker-level transcript only on drill-down. **Verdict:** study; it is a desktop, single-mission view, not a multi-project desk.

### Kanban boards for agents

- **Vibe Kanban** (Apache-2.0) puts tasks as cards on a board, each assigned to a coding agent running in its own git worktree, with diff review and merge in the same UI [25]. One review reports the company behind it shut down on 10 April 2026 (single source, unverified) [25].
- **Conductor** (closed source, Mac only) runs parallel Claude Code or Codex agents in isolated worktrees: "See at a glance what they're working on, then review and merge their changes" [26]. **Paseo** (AGPL-3.0) is a daemon with desktop, mobile, web and CLI clients, per-agent worktrees and scheduling (claims from its own site) [27].
- **Assessment.** A board answers *where is each piece of work in a pipeline*, which suits a team moving cards through stages. For one principal on a phone it fails twice: columns do not fit a narrow screen without horizontal scrolling, and most columns ("in progress", "queued") hold things that do not need the principal, so the eye must search for the one column that does. The board is a good **Fleet** drill-down on a wide screen. **Verdict:** borrow the per-task worktree and review-merge pattern for producers; avoid the board as the desk's front page.

### Third-party mobile clients for coding agents

- **Happy** (MIT) is an open-source mobile and web client for Claude Code and Codex, end-to-end encrypted, that pushes "when Claude Code and Codex needs permission or encounters errors" [28]. **Omnara** offers mobile monitoring, steering and approval of live sessions with notifications "when agents need you"; its licence and maintenance status are reported inconsistently (Apache-2.0 per PyPI, an archived v1 per a competitor) [29].
- **What to take.** The permission prompt as a push with an action, end-to-end encryption between the machine and the phone. **Verdict:** study; first-party Remote Control now covers most of this for Claude Code [2].

### Human-in-the-loop interfaces and protocols

- **LangChain Agent Inbox** is a UI for LangGraph interrupts in which each interrupt declares which responses it allows (`allow_accept`, `allow_edit`, `allow_respond`, `allow_ignore`), and the human's reply is typed `accept | edit | response | ignore` [30]. This is the cleanest published form of "the item declares its own verbs".
- **AG-UI** (an agent-to-UI event protocol) ends a run with `RunFinished` carrying `outcome: {type: "interrupt", interrupts: [...]}` when the run pauses for a human, and the client resumes by starting a new run with a `resume` array answering every open interrupt; state flows as a `StateSnapshot` that replaces client state and `StateDelta` JSON Patch operations (RFC 6902) [31].
- **What to take.** Both validate the data design in section 7: an item is an interrupt with a declared set of allowed responses, and the client keeps state by snapshot plus patch. **Verdict:** borrow the shapes; do not adopt either runtime.

## 2. Comparison table

| Product | What the operator sees first | Phone | Licence | Verdict |
|---|---|---|---|---|
| Claude Code agent view [1] | Sessions grouped Pinned / Ready for review / Needs input / Working / Completed, LLM one-line summary per row | Terminal only; phone via Remote Control and app [2] | Proprietary | **Borrow pattern**: state groups, one-line summary, peek-and-reply, recap on attach |
| Claude Code Remote Control / cloud [2][3] | Session list by name, green dot when online; diff card | Claude app, push on "decides" / "actions required" | Proprietary | **Study**: presence-aware push suppression |
| OpenAI Codex mobile [4][5] | Chats per host; approvals; artifacts (diff, tests, screenshots) | ChatGPT app | Proprietary service | **Study** |
| Cursor web and mobile [7] | Mobile inbox of agents; Live Activities for up to 8 | PWA and iOS app | Proprietary | **Borrow pattern**: lock-screen Live Activity for "working" |
| Devin [9] | Sessions grouped Working / Ready / Blocked / Inactive; named ask; bell inbox | Mobile web | Proprietary | **Borrow pattern**: needs-input vs ready-for-review split, named ask, per-type push |
| GitHub mission control / Projects [11][12] | Task view; sessions on Projects boards (queued / working / waiting for review / completed) | GitHub Mobile (assign, tasks) | Proprietary | **Borrow pattern** as producer and drill-down |
| Linear agents [14][15] | Issue with delegate and agent session state | Linear app | Proprietary (public API) | **Study**: delegate vs owner, typed activities, liveness contract |
| OpenClaw Control UI [16] | Chat-centred sessions, pinned, snoozed, team mode, approval blocks | PWA, web push | Not verified here | **Study** |
| OpenClaw community "mission control" dashboards [17] | Panels: agents, kanban, costs, logs | Mostly desktop | Mixed (MIT to BSL 1.1) | **Avoid** as dependency; desktop-wall pattern |
| Paperclip [18]-[22] | Dashboard cards; **Decisions** ranked queue; approvals; org chart | Claims "Mobile Ready" (unverified) | MIT | **Borrow pattern**: Decisions page is the closest prior art |
| OpenHands Agent Canvas [23] | Conversations and automations | Not documented | MIT (core) | **Study**: render-only UI over run and schedule servers |
| Factory Mission Control [24] | Mission milestone summary, worker log | Web and desktop | Proprietary | **Study** |
| Vibe Kanban [25] | Kanban of tasks, each an agent in a worktree | Desktop web | Apache-2.0 | **Avoid** as desk; borrow worktree-per-task |
| Conductor [26] | Workspaces at a glance, review and merge | Mac only | Closed | **Avoid** (closed, Mac only) |
| Paseo [27] | Agents across clients | Native mobile (claimed) | AGPL-3.0 | **Study** |
| Happy [28] | Mirrored sessions with permission pushes | iOS, Android, web | MIT | **Study** |
| Omnara [29] | Live sessions, approvals | iOS, web | Apache-2.0 (conflicting reports) | **Study** |
| LangChain Agent Inbox [30] | Inbox of interrupts with allowed responses | Web | Open source (licence not checked) | **Borrow pattern**: item declares its verbs |
| AG-UI protocol [31] | n/a (protocol) | n/a | Open source (licence not checked) | **Borrow pattern**: interrupt outcome, snapshot plus JSON Patch |

## 3. Information architecture patterns

**Feed** (reverse-chronological activity). Every dashboard has one (Paperclip's Recent Activity [19], Factory's progress log [24]). It answers "what happened", is easy to build and is the worst first screen for a supervisor: it grows with the fleet, not with the principal's obligations, and the one item that needs action scrolls away. Use it only inside the Log and inside an item's evidence.

**Board** (columns by stage). Answers "where is each piece of work"; fits a team and a wide screen (section 1, Kanban). On a phone it becomes a horizontal scroller whose relevant column is one of five. Use it as an optional Fleet view on a wide screen.

**Roster** (one row per agent or session, grouped by state). Claude Code agent view, Devin, crowsnest. Answers "who is doing what"; the state groups put "needs input" near the top, which is a partial exception list. Its unit is the *agent*, so one agent with three questions is one row, and a question that belongs to no live session (a PR waiting a week) is absent. Use it as the Fleet screen.

**Inbox** (one row per thing addressed to you, read/unread). Devin's bell [9], Cursor's mobile inbox [7], LangChain Agent Inbox [30]. Answers "what was sent to me"; carries the email failure modes (everything is addressed to you, unread counts become anxiety). Its strength is per-type routing and mute.

**Exception list / decision queue** (one row per *decision the principal must make*, ranked, completed in place, disappearing when resolved). Paperclip Decisions [20], openloops' board [50], PRINCE2's exception report as summarised in the phase-1 report [51]. Answers the only question a principal on a phone asks: *what needs me, in what order, and can I clear it from here?* **This is the desk's one list.**

**Chat** (one conversation with an assistant). Every product has a chat per session; OpenClaw and Codex mobile put chat at the centre [4][16]. Chat is the right *input* surface for things that do not fit a verb ("ask the parser session what is left") and the right home for the secretary, but a poor overview: state is buried in prose. On the desk, chat is the composer reached from an item ("Reply") or from a global "Ask" button, never the list.

**Approval card.** The common anatomy across Paperclip [21], Claude Code [1], Codex [4] and Agent Inbox [30]: type badge, requester, age, one-line subject, summary, two or three primary actions, a link to details. Paperclip's third action, **Request revision**, is worth copying because "no" and "not like this" are different answers and agents treat a rejection as final [21]. Agent Inbox's per-item allowed responses keep the card honest: an item that cannot be edited does not show Edit [30].

**A "needs you" lane.** Every roster product has one (Needs input [1], Blocked [9], awaitingInput [14], waiting for review [12]); crowsnest's report leads with Needs you [50]. The lesson from Devin's regrouping [9] is to split it: *needs your input* (an agent is blocked until you answer) is more urgent than *ready for your review* (work is done and will wait). The desk ranks the first above the second.

**Drill-down from a line to the transcript.** The pattern everywhere is three levels: the row's one-line summary, a peek or preview card (Claude Code's Peek [1], Devin's hover card [9]), then the full session with a recap of what happened while you were away [1]. ISA-101 formalises the same idea for control rooms as a display hierarchy from a Level 1 overview of the whole span of control down to detail displays [49]. On a phone: list row → item detail (evidence, options, the representative's note) → session recap → transcript, each one tap, with Back always returning to the list position.

## 4. Notification design on phones

**The platform tiers.** iOS has four interruption levels: `passive` ("adds the notification to the notification list without lighting up the screen or playing a sound"), `active` (immediate, lights the screen, can play a sound), `timeSensitive` (as active, and "breaks through system notification controls" such as Focus), and `critical` (bypasses the mute switch; requires an Apple entitlement) [32][33]. Android has per-app **notification channels** whose importance the developer sets once and the user can change at any time [34]. ntfy, already among correspond's channels, has priorities 1 (min, under the fold) to 5 (max, long vibration bursts and a pop-over), up to three action buttons per notification, and an `http` action that sends a request and can clear the notification only if the request succeeds [35].

**What existing agent products do.** Claude Code pushes on two triggers only (a decision is needed, a long task finished), with a presence signal that suppresses pushes while you are at the machine [2]; Cursor pushes on every finished turn and moves "working" to Live Activities [7]; Devin routes per notification type to inbox or phone with per-type mute [9]; agent view notifies on starts-needing-input, finished and failed [1]. Nobody publishes a rate limit.

**What people do with notifications.** In a one-week field study participants received on average 63.5 notifications a day, mostly from messengers and email [36]; in another, the sender relationship, the notification's priority and the type and complexity of the ongoing task affected response time, and important content still caused disruption [37]. The desk competes with that stream; one more chatty source is muted within a week (an inference, not a measurement).

**The alarm-management benchmark.** Process-industry guidance (EEMUA 191, ISA-18.2, IEC 62682) treats "up to 12 alarms per hour" as the maximum manageable for an operator, defines a flood as more than 10 new alarms in 10 minutes, and tracks chattering, stale and top-ten-source alarms as nuisance indicators [44]. That operator is paid to sit at the console; a principal reading on a phone between other things should get far fewer. **Estimate:** a budget of a handful of interrupting pushes a day (order of 3-6), with everything else passive and collected into the list or the next brief, is a reasonable starting point to tune from the principal's response data; this is a design estimate, not a sourced number.

**Recommended mapping (estimate, to be tuned).**

| Desk severity | Meaning | iOS level | ntfy priority | Batching |
|---|---|---|---|---|
| `interrupt` | A one-way door is waiting, or a deadline is within hours, or work is blocked and costing money | `timeSensitive` | 4-5 | Immediate, deduplicated per item, counted against the daily budget |
| `today` | An agent is blocked on the principal, two-way door | `active` | 3 | Immediate only outside quiet hours and only if the principal is not at a machine (presence, as [2]); otherwise next digest |
| `review` | Work is ready for review, nothing is blocked | `passive` | 2 | Digest |
| `fyi` | Finished, informational | none (list and Log only) | 1 | Brief |

Design rules that follow from the sources: one push per item, updated in place rather than re-sent (thread or collapse id); the push carries the ask and at most three actions, and every action is idempotent so a double tap or a retry from a flaky connection is harmless (ntfy's clear-only-on-success is the right model [35]); a destructive action never runs from the notification shade (it opens the card); the badge counts only `Needs you` items, never activity; and the desk publishes its own heartbeat so the principal can tell "nothing needs you" from "the desk stopped".

## 5. What the human-factors literature says about supervising many automated workers

**Levels and stages of automation.** Parasuraman, Sheridan and Wickens split automation into four stages (information acquisition, information analysis, decision and action selection, action implementation), each automatable to a different level, and make the effect on human performance the primary design criterion [39]. For the desk: the organisation layer may automate *acquisition and analysis* heavily (gathering, summarising, predicting objections), *decision selection* only for reversible items under a calibrated gate (the phase-1 recommendation [51]), and *action implementation* only after the decision. The desk shows which stage the automation acted at on every item ("summarised by", "recommended by", "decided by").

**Situation awareness.** Endsley's three levels are perception of what is happening, comprehension of what it means for the goals, and projection of what happens next [40]. A row that says only "waiting" serves level 1; "waiting on your choice between two schemas; the session is idle until then; the release slips a day if not answered by Friday" serves all three. Each item therefore carries `why_now` (comprehension) and `if_ignored` or `decide_by` (projection), not just a status.

**The automation conundrum.** Endsley's later review argues that as autonomy becomes more reliable and robust, operators' situation awareness falls and successful takeover becomes less likely, so the conundrum persists wherever human oversight is still required [41]. The desk must expect the principal to know *less* about each project as the fleet improves, and so every item must be self-contained: it cannot assume the principal remembers the context.

**Bainbridge's ironies.** Humans cannot keep effective visual attention on rare events for more than about half an hour, so alarms must do the watching; skills that are not used decay, so the operator is least practised exactly when takeover is needed; automation can "camouflage" failure by compensating until the situation is already out of control, so "automatic systems should fail obviously"; and operators "should not have to page between displays" to check abnormal states. Bainbridge's remedy is periodic hands-on control [38]. For the desk: (a) the desk never asks the principal to *watch* anything; (b) a dead producer, a stale ledger or an unreachable machine is itself an item, shown as `unknown`, never silence; (c) a small daily sample of what the representative auto-approved is put on the list for review, which keeps the principal's judgement exercised and gives the calibration data the phase-1 gate needs [51].

**Ironies of generative AI.** Simkute et al. apply the ironies to generative AI and find productivity loss from a shift from production to evaluation, unhelpful restructuring of workflows, interruptions, and automation making easy tasks easier and hard ones harder; they propose continuous feedback, personalisation, ecological interface design, task stabilisation and clear task allocation [48]. The desk turns the principal's role into evaluation by design, which is the first irony; it should therefore make evaluation cheap (artifacts, not transcripts; a recommendation with its reason) and keep interruptions to the budget.

**Span of control and fan-out.** In supervisory control of several unmanned vehicles, fan-out is estimated from *neglect time* (how long a vehicle stays acceptable without attention) and *interaction time* (how long a servicing interaction takes) [43]. Cummings and Mitchell showed that including wait times, mainly from lost situation awareness, cut predicted capacity by up to 67%, and by 36% "even under a highly automated management-by-exception system" [42]. **Estimate for agents:** if a session typically runs 30 minutes unattended and needs 3 minutes of the principal's attention, the naive fan-out is about 10-11 sessions; with the reported 36% loss it is about 7. The desk can measure both quantities from its own log (time between items per session; time from opening an item to deciding it) and should show the principal when the fleet is running above what they can service, rather than letting the queue silently age.

**Automation bias and trust.** Mosier and Skitka distinguish errors of *commission* (following an automated directive that is wrong) from errors of *omission* (missing a problem the aid did not flag); pilots who felt accountable for their use of automation cross-checked more and made fewer such errors [45]. Lee and See define the goal as *calibrated* trust, with resolution and specificity, and treat both misuse (over-reliance) and disuse as failures [46]. Diagnostic automation below roughly 70% reliability may not be worth its cost, per a synthesis by Wickens and Dixon as cited in later work (the threshold is from a secondary citation; the original was not retrieved) [47]. For the desk: the representative's recommendation is shown with its *reason* and the *opposite case*, not only a verdict; the Log shows the representative's hit rate per kind of item; and items where the representative is uncalibrated are shown without a recommendation rather than with a confident one.

**High-performance HMI.** ISA-101 practice keeps normal states grey and reserves saturated colour for abnormal ones, and starts from a Level 1 overview of the operator's whole span of control that shows deviations from normal [49]. On the desk: Fleet is grey, a row gets colour only when something is wrong (blocked, failed, stale, over budget); red is reserved for one-way doors and failures, never for "running".

## 6. Recommended information architecture for a phone-first desk

**Principles (each traceable above).** Exceptions, not workers [20][42][49]. One list, ranked, budgeted [44]. Every item self-contained [40][41]. Fail obviously: unknown is a state, the desk has a heartbeat [38][50]. Show what the automation did and at which stage [39]. Keep the principal's judgement exercised [38][45]. The interface renders data and sends verbs; it owns no logic [23][31].

### Screens

**1. Desk (home): the one list, "Needs you".**
- A single header line, the **pulse**: "4 need you · 11 working · 1 unknown · checked 14:02". Tapping a count jumps to the Fleet filtered to it. The time is when the sources were last read, and it turns amber past a staleness threshold.
- The list, ranked by: one-way-door and deadline first, then blocked-on-you (an agent is idle until you answer), then ready-for-review, then the daily review sample. Within a rank, older first. Items are grouped only by these four bands, labelled in plain words (*Decide*, *Unblock*, *Review*, *Spot-check*), so the list is still one list.
- Each row: the ask in one line (imperative, written by the producer, e.g. "Choose the storage backend for the cache"), a second line with the source and age as said ("parser-tests · 2 h ago"), and badges only when they change the decision: `one-way`, `due Fri`, `unknown`. The representative's verdict is a small mark ("likely fine" / "you may object: cost") on the row.
- Swipe right: the item's primary verb if it is reversible (Approve, or the recommended option). Swipe left: Later (snooze menu). Tap: item detail.
- Empty state: "Nothing needs you. Last checked 14:02 from 5 sources." plus the next scheduled brief. If any source is unknown, the empty state is not shown; the unknown item is.

**2. Item detail (one tap from a row).**
- Top: the ask, why now, and what happens if ignored (or the decide-by).
- The recommendation and its reason, then **the predicted objection** and its reason (the phase-1 representative [51]), and the reversibility of each option.
- Options as large buttons, each saying the effect it will have ("Merge PR #42 and close the issue"), at most a handful; a free-text **Reply**; **Request changes** (the request-revision verb [21]); **Delegate** ("ask the reviewer to decide", "hand to the session that raised it"); **Later**; **Dismiss, no effect**.
- Evidence: artifacts first (diff stats, test result, the paragraph of the document in question, a screenshot), then links (issue, PR, ledger, annals document), then **Open session** (recap, then transcript; the claude.ai Remote Control link when there is one, else the command that reaches it, as crowsnest already does [50]).
- A one-way-door option requires a typed confirmation (Paperclip types the issue identifier [20]); every completed verb shows an Undo for a few seconds where the effect is not yet sent.

**3. Fleet (tab).**
- The roster, grey by default, colour only for abnormal: grouped as *Needs you · Working · Ready for review · Quiet · Unknown*, one row per session or agent with the generated one-line summary [1], collapsible children under parents [9], and a toggle to group by project or by manager (the org chart as a tree is a sub-view here, not a front page [22]).
- This is where crowsnest's report already is; the desk's Fleet can be that page embedded, or a renderer over the same rows.
- A capacity line: sessions running versus the principal's measured servicing rate (section 5, fan-out), shown only when it is exceeded.

**4. Log (tab).**
- Reverse-chronological record of decisions: who decided (principal, representative, a manager under delegated rights), which option, from which surface (tap, push action, voice, terminal), and the outcome when known.
- A filter for **decided by the representative**, with its running agreement rate with the principal's later corrections; tapping one reopens it as a spot-check item. This is the calibration loop from phase 1 made visible [51].
- Expired and dismissed items, so nothing disappears without a trace (Paperclip keeps Decided and Expired sections for the same reason [20]).

**5. Ask (a floating button, not a tab).** A composer that sends a free-text request to the secretary or, with a target chosen, to a session or manager. It produces a dispatch item in the Log so the principal can see it was received and by whom.

### What each tap does (the verb set)

| Verb | Where | Effect sent to the organisation layer | Reversible from the desk |
|---|---|---|---|
| `open` | row tap | none (read); marks the item seen | n/a |
| `choose(option)` | detail, swipe-right if recommended and two-way | the option's declared effect | Undo window, then no |
| `approve` | detail, push action | `choose` of the single approve option | as above |
| `reply(text)` | detail | text routed to the producing agent; item stays open until the producer resolves it | n/a |
| `request_changes(text)` | detail | returns the proposal to its author with the text; item goes to `waiting_on_agent` | n/a |
| `delegate(to)` | detail | reassigns the decision to a manager or session with stated rights; item moves to Log with the delegation | yes |
| `later(until)` | swipe-left, push action | snooze: hides until the time or until the item changes (crowsnest's `later` semantics [50]) | yes |
| `dismiss` | detail | records a refusal with no effect [20] | yes, from Log |
| `spot_check(verdict)` | sample items | records agree / disagree with an automated decision, feeding calibration | n/a |

Every verb carries an idempotency key, the surface it came from and the principal's identity, so a push-notification tap, a page tap and a spoken command are indistinguishable downstream.

### How voice attaches later

Voice is an adapter at the edge, with no knowledge the visual desk does not have:

- **Output.** Reading the desk is a template over the same ranked list: the pulse line, then each item's ask, why now, recommendation and objection, then "say approve, later, or tell me more". The list order *is* the reading order, so the voice and the screen never disagree about what matters.
- **Input.** An utterance is parsed into exactly one `VerbInvocation` against the item being read (or named), with the same idempotency and identity fields; free speech becomes `reply(text)` or an Ask.
- **Safety.** Voice may invoke only verbs that are reversible or have an Undo window; any one-way option is turned into a card pushed to the screen for a tap. This matches the owner's existing voice manager, whose actions are proposals confirmed on a card.
- **Consequence for the build.** If the data contract in section 7 holds, the voice adapter needs no new endpoint; it is another client of `list items`, `get item`, and `invoke verb`.

## 7. The shape of the data the desk needs from the organisation layer

The organisation layer should expose four things, transport-agnostic: **items** (what needs or needed the principal), **actors** (who produced or decided them), **verbs** (what the principal can do), and an **event log** (what happened). A client keeps state as a snapshot plus patches, as AG-UI does [31], or simply re-reads a JSON snapshot on a timer (enough for v1, as the crowsnest page already does every 60 s).

The schema below is written as Zod for the frontend; the SSOT should be one JSON Schema (or a Python dataclass module that emits it) shared by the Python producers and the TypeScript renderer, so neither side redefines the vocabulary. Field names are proposals.

```ts
import { z } from "zod";

// Closed vocabularies, shared with the Python side.
export const Reversibility = z.enum(["two_way", "one_way", "unknown"]);
export const Band = z.enum(["decide", "unblock", "review", "spot_check", "fyi"]);
export const Severity = z.enum(["interrupt", "today", "review", "fyi"]);
export const ItemState = z.enum([
  "open", "snoozed", "waiting_on_agent", "decided", "dismissed", "expired", "withdrawn", "unknown",
]);
export const AgentState = z.enum([   // superset of Claude agent view, Linear, GitHub, Devin
  "working", "needs_input", "ready_for_review", "idle", "done", "failed", "stopped", "stale", "unknown",
]);
export const Stage = z.enum(["acquired", "analysed", "recommended", "decided", "acted"]);  // Parasuraman stages
export const Surface = z.enum(["tap", "push_action", "voice", "terminal", "auto"]);

export const Link = z.object({ label: z.string(), url: z.string().url(), kind: z.string().optional() });

export const Said = z.object({        // crowsnest's rule: the claim's own time, never restamped
  at: z.string().datetime().nullable(),
  basis: z.string(),                    // "transcript", "registry", "ledger heading", "upper bound"...
});

export const Option = z.object({
  id: z.string(),
  label: z.string(),                    // "Merge PR #42"
  effect: z.string(),                   // what will happen, in words
  reversibility: Reversibility,
  confirm: z.enum(["none", "tap", "typed"]).default("none"),
  inputs: z.array(z.object({ name: z.string(), label: z.string(), kind: z.enum(["text", "choice"]) })).default([]),
});

export const Assessment = z.object({  // the representative's note (phase 1)
  recommendation: z.string().nullable(),          // option id, or null when uncalibrated
  rationale: z.string().optional(),
  objection: z.object({ risk: z.enum(["low", "medium", "high", "uncalibrated"]), reason: z.string() }).optional(),
  by: z.string(),                                  // actor id
});

export const Item = z.object({
  id: z.string(),
  dedupe_key: z.string(),               // e.g. the GitHub URL, or session id + question hash
  band: Band,
  severity: Severity,
  state: ItemState,
  ask: z.string(),                      // one imperative line
  why_now: z.string(),
  if_ignored: z.string().optional(),
  decide_by: z.string().datetime().optional(),
  expires_at: z.string().datetime().optional(),
  said: Said,
  created_at: z.string().datetime(),
  updated_at: z.string().datetime(),
  producer: z.string(),                 // actor id: a session, a manager, crowsnest, openloops...
  owner: z.string(),                    // who holds the decision right (the principal, or a delegate)
  stage: Stage,                         // how far automation went before asking
  options: z.array(Option).default([]),
  allowed_verbs: z.array(z.enum([
    "choose", "approve", "reply", "request_changes", "delegate", "later", "dismiss", "spot_check",
  ])),
  assessment: Assessment.optional(),
  evidence: z.array(Link).default([]),  // artifacts first, then context
  session: z.object({ id: z.string(), name: z.string(), open_url: z.string().url().optional(), open_cmd: z.string().optional() }).optional(),
  attention: z.object({                 // crowsnest's attention record, per principal
    seen_at: z.string().datetime().optional(),
    snoozed_until: z.string().datetime().optional(),
  }).default({}),
  where: z.enum(["tap", "browser", "session", "physical", "mac_only"]).optional(),  // openloops tier
  minutes: z.number().int().optional(),                                              // openloops estimate
});

export const Actor = z.object({
  id: z.string(),
  kind: z.enum(["principal", "secretary", "manager", "representative", "session", "producer"]),
  name: z.string(),
  state: AgentState.optional(),
  summary: z.string().optional(),       // the one-line "what it is doing / needs / produced"
  parent: z.string().optional(),        // org chart or spawn lineage
  machine: z.string().optional(),
  last_seen: z.string().datetime().optional(),
});

export const VerbInvocation = z.object({
  item_id: z.string(),
  verb: z.string(),
  option_id: z.string().optional(),
  text: z.string().optional(),
  until: z.string().datetime().optional(),
  to: z.string().optional(),            // delegate target
  by: z.string(),                       // actor id; the principal for a tap, the representative for auto
  via: Surface,
  idempotency_key: z.string(),
  at: z.string().datetime(),
});

export const Event = z.discriminatedUnion("type", [
  z.object({ type: z.literal("item_opened"), item: Item }),
  z.object({ type: z.literal("item_updated"), id: z.string(), patch: z.array(z.any()) }),  // RFC 6902
  z.object({ type: z.literal("verb_invoked"), invocation: VerbInvocation }),
  z.object({ type: z.literal("item_resolved"), id: z.string(), state: ItemState, outcome: z.string().optional() }),
  z.object({ type: z.literal("source_health"), source: z.string(), ok: z.boolean(), checked_at: z.string().datetime(), reason: z.string().optional() }),
]);

export const DeskSnapshot = z.object({
  generated_at: z.string().datetime(),
  sources: z.array(z.object({ name: z.string(), ok: z.boolean().nullable(), checked_at: z.string().datetime().nullable() })),
  items: z.array(Item),
  actors: z.array(Actor),
  budget: z.object({ interrupts_today: z.number().int(), interrupt_limit: z.number().int() }),
});
```

**Mapping from what exists (adapters, not rewrites).**
- openloops `Task` → `Item`: `title`/`ask` → `ask`; `decision.question`/`options`/`recommendation` → `options` and `assessment.recommendation`; `tier` → `where`; `minutes` → `minutes`; `links` → `evidence`; `url` → `dedupe_key`; `status` → `state` [50].
- crowsnest row → `Actor` (every session) and → `Item` (a waiting session, band `unblock`); `said_at`/`said_at_basis` → `said`; seen/later/done → `attention` and verbs [50].
- Claude Code `claude agents --json` `state`/`waitingFor` → `AgentState` and an `unblock` item [1]; Linear `elicitation` activities and `awaitingInput` sessions → items, `stale` → `AgentState.stale` [14]; GitHub "waiting for review" → `review` items [12]; AG-UI interrupts → items whose `allowed_verbs` come from the interrupt [31]; Agent Inbox's `allow_*` flags map directly onto `allowed_verbs` [30].

**Why this shape keeps the layer interface-agnostic.** Ranking, budgeting, deduplication and the representative's assessment happen in the organisation layer and arrive as fields (`band`, `severity`, `assessment`), so every renderer (the phone page, a push, the crowsnest page, a voice adapter, a terminal) shows the same order and the same reasons. The renderer's only logic is layout. For the owner's stack, the Zod schemas give zodal its forms and tables directly, and a zustand store holding a `DeskSnapshot` and applying `item_updated` patches with immer is the whole client state.

## 8. Standard terminology

| Term | Meaning here | Source |
|---|---|---|
| Supervisory control | A human sets goals for and monitors automated agents that do the work, intervening by exception | [39][42] |
| Principal | The person on whose behalf the agents act and who holds the final decision rights | phase-1 report [51] |
| Item (attention item) | One request for the principal's attention; the unit of the desk | this report |
| Exception report / manage by exception | Only deviations beyond a tolerance reach the principal; silence means on track | [51] |
| Interrupt / elicitation | An agent pausing for human input (AG-UI interrupt [31], Linear elicitation [14]) | [14][31] |
| Delegate vs owner (assignee) | The agent acts; the human keeps ownership | [15] |
| Needs input / blocked | An agent cannot proceed until the principal answers | [1][9] |
| Ready for review | Work is done and waiting; nothing is blocked | [1][9] |
| Roster | One row per session or agent with its state | crowsnest [50] |
| Span of control | Everything one supervisor is responsible for; the scope of a Level 1 overview | [49] |
| Fan-out | How many automated workers one supervisor can service, from neglect time and interaction time | [42][43] |
| Neglect time / interaction time | How long a worker stays acceptable unattended / how long servicing it takes | [43] |
| Levels and stages of automation | Acquisition, analysis, decision selection, action implementation, each at its own level | [39] |
| Situation awareness (SA) | Perception, comprehension, projection | [40] |
| Out-of-the-loop problem / automation conundrum | Better automation, less awareness, harder takeover | [41] |
| Automation bias (commission, omission) | Following a wrong directive / missing what the aid did not flag | [45] |
| Calibrated trust; misuse and disuse | Trust that matches reliability; over- and under-reliance | [46] |
| Alarm flood, chattering alarm, stale alarm | More than 10 new alarms in 10 minutes; an alarm that repeats rapidly; an alarm active for a long time | [44] |
| Interruption level | Passive, active, time-sensitive, critical | [32] |
| Notification channel / importance | Android's per-category, user-adjustable delivery settings | [34] |
| Snooze (later) | Hide until a time or until the item changes | [20][50] |
| Drill-down / display hierarchy | Overview to detail, one level at a time | [49] |
| Peek, attach, recap | Read a session's state without entering it; enter it; summary of what happened while away | [1] |
| Two-way door / one-way door | Reversible / irreversible decision | [51] |
| Heartbeat | The periodic wake of an agent (Paperclip) and, here, the desk's own liveness signal | [19] |

## 9. Important things not asked

- **The desk itself must fail obviously.** Every source the desk reads (crowsnest registry, openloops cache, a manager's queue, a machine) reports `ok`, `failed` or `unknown` with a checked-at time, and an unknown source is an item. A calm empty list built on a dead producer is the worst outcome, which openloops' README argues for its own board [50] and Bainbridge argues in general [38].
- **Deduplication is a first-class field.** The same pull request can reach the desk from a waiting session, an openloops task and a manager's review. Without a `dedupe_key` the principal sees it three times and learns to skim.
- **Item text is untrusted input.** Asks, summaries and evidence are written by agents that read the internet and repositories; render them as text, never as HTML, never execute links automatically, and never let an item's text set its own severity. crowsnest already sanitises its page [50]; the desk inherits that rule.
- **A tap is a remote action.** Verbs from a phone can merge code or spend money, so the verb endpoint needs authentication independent of the page's URL, idempotency keys, and a short-lived signed link for push actions; destructive options never run from the notification shade.
- **Measure the desk like an alarm system.** Items per day, interrupting pushes per day against the budget, median time from item to decision, share of items decided versus snoozed versus expired, override rate of the representative, and top-ten producers by item count [44]. A producer that dominates the count is a nuisance source to fix at the producer, not at the desk.
- **Expiry is a decision too.** Paperclip expires unanswered proposals after seven days [20]; items here should carry `expires_at` and, on expiry, apply a declared default (usually "do nothing") and say so in the Log.
- **The "working" view belongs on the lock screen, not in the list.** Live Activities (Cursor tracks up to eight agents [7]) or a single persistent low-priority notification can show what is running ambiently, which removes the temptation to put working sessions on the desk.
- **Web push on iOS has requirements.** OpenClaw documents iOS Safari 26.2 or later for its PWA and web push [16]; a phone-first web desk should confirm the owner's devices support web push before relying on it, and keep ntfy (already a correspond channel) as the push path otherwise [35].
- **Keep the principal in practice.** The daily spot-check sample is not overhead; it is Bainbridge's "hands-on control for a short period in each shift" [38] translated to this setting, and it is the only source of labels for the representative's calibration [51].

## What this means for the build

1. The organisation layer's public contract is `Item`, `Actor`, `VerbInvocation`, `Event` and `DeskSnapshot` (section 7), defined once as JSON Schema and mirrored in Python and Zod; no surface computes rank, severity or assessment.
2. v1 surface: one phone page with three screens (Desk, Fleet, Log), rendered from a `DeskSnapshot` JSON re-read on a timer; Fleet can embed the existing crowsnest report rather than rebuild it.
3. Producers first: thin adapters from openloops `Task` and crowsnest rows to `Item`, with `dedupe_key`; then managers and the representative add `assessment`.
4. Verbs go through one endpoint with idempotency keys; the existing openloops receiver pattern (taps become actions through a session) is the default seam until a direct executor exists.
5. Notifications: one channel adapter (ntfy via correspond) mapping `severity` to priority, a daily interrupt budget, one push per item updated in place, presence-aware suppression.
6. Unknown is a state everywhere; every source reports health; the desk shows its own last-checked time.
7. Log shows every automated decision and a daily spot-check sample, closing the phase-1 calibration loop.
8. Voice is a later client of the same three calls (list, get, invoke) and may only invoke reversible verbs.
9. Borrow Paperclip's Decisions mechanics (why-now, request-revision, expiry, typed confirmation) and Claude Code's one-line summaries and peek; avoid kanban and panel-wall dashboards as the front page.

## REFERENCES

1. [Claude Code docs: Manage multiple agents with agent view](https://code.claude.com/docs/en/agent-view); launch date from secondary coverage: [Anthropic ships Agent View (NYU Shanghai RITS)](https://rits.shanghai.nyu.edu/ai/anthropic-ships-agent-view-a-multi-session-dashboard-for-claude-code/)
2. [Claude Code docs: Continue local sessions from any device with Remote Control](https://code.claude.com/docs/en/remote-control)
3. [Claude Code docs: Use Claude Code in the cloud](https://code.claude.com/docs/en/claude-code-on-the-web)
4. [OpenAI Codex docs: Remote connections](https://learn.chatgpt.com/docs/remote-connections.md)
5. [OpenAI Codex docs: Cloud](https://learn.chatgpt.com/docs/cloud)
6. [Appwrite: OpenAI just shipped Codex to the ChatGPT mobile app (secondary)](https://appwrite.io/blog/post/openai-just-shipped-codex-to-the-chatgpt-mobile-app)
7. [Cursor docs: Web and mobile agents](https://cursor.com/docs/background-agent/web-and-mobile)
8. [Cursor blog: Cursor agents on the web and mobile](https://cursor.com/blog/agent-web)
9. [Devin release notes](https://docs.devin.ai/release-notes/overview)
10. [Devin API: List sessions](https://docs.devin.ai/api-reference/v1/sessions/list-sessions.md)
11. [GitHub changelog: A mission control to assign, steer, and track Copilot coding agent tasks (2025-10-28)](https://github.blog/changelog/2025-10-28-a-mission-control-to-assign-steer-and-track-copilot-coding-agent-tasks/)
12. [GitHub changelog: Agent activity in GitHub Issues and Projects (2026-03-26)](https://github.blog/changelog/2026-03-26-agent-activity-in-github-issues-and-projects/)
13. [GitHub blog: Agents panel, launch Copilot coding agent tasks anywhere on GitHub](https://github.blog/news-insights/product-news/agents-panel-launch-copilot-coding-agent-tasks-anywhere-on-github/)
14. [Linear developers: Agent interaction](https://linear.app/developers/agent-interaction)
15. [Linear developers: Agents](https://linear.app/developers/agents)
16. [OpenClaw docs: Control UI](https://docs.openclaw.ai/web/control-ui.md)
17. [Verdent guide: OpenClaw Mission Control](https://www.verdent.ai/guides/openclaw/mission-control); [SourcePulse: abhi1693/openclaw-mission-control](https://www.sourcepulse.org/projects/24936332); [PromptQuorum: builderz-labs Mission Control review](https://www.promptquorum.com/power-local-llm/mission-control-review); [ecosyste.ms: jontsai/openclaw-command-center](https://awesome.ecosyste.ms/projects/github.com%2Fjontsai%2Fopenclaw-command-center)
18. [Paperclip on GitHub (paperclipai/paperclip)](https://github.com/paperclipai/paperclip)
19. [Paperclip docs: Dashboard](https://docs.paperclip.ing/guides/day-to-day/dashboard/)
20. [Paperclip docs: Decisions](https://docs.paperclip.ing/guides/day-to-day/decisions/)
21. [Paperclip docs: Approvals](https://docs.paperclip.ing/guides/day-to-day/approvals.md)
22. [Paperclip docs: Agents and org chart](https://docs.paperclip.ing/guides/org/agents/)
23. [OpenHands docs: Introduction](https://docs.openhands.dev/overview/introduction.md); [Agent Canvas architecture](https://docs.openhands.dev/openhands/usage/agent-canvas/architecture.md); [OpenHands repository (licence)](https://github.com/OpenHands/OpenHands)
24. [Factory docs: Running missions in the Factory app](https://docs.factory.com/missions/running-app.md); [Factory app overview](https://docs.factory.ai/factory-app/overview.md)
25. [Vibe Kanban on GitHub (BloopAI/vibe-kanban)](https://github.com/BloopAI/vibe-kanban); shutdown claim: [AI IDE List: Vibe Kanban](https://aiidelist.com/ide/vibe-kanban)
26. [Conductor docs](https://docs.conductor.build/); closed-source and platform notes: [Ry Walker: Conductor](https://rywalker.com/research/conductor)
27. [Paseo vs Conductor (Paseo's own site)](https://paseo.sh/alternatives/conductor)
28. [Happy on GitHub (slopus/happy)](https://github.com/slopus/happy)
29. [Omnara on PyPI](https://pypi.org/project/omnara/)
30. [LangChain Agent Inbox on GitHub](https://github.com/langchain-ai/agent-inbox); [LangChain docs: Human-in-the-loop](https://docs.langchain.com/oss/python/langchain/human-in-the-loop)
31. [AG-UI docs: Events](https://docs.ag-ui.com/concepts/events)
32. [Apple developer documentation: UNNotificationInterruptionLevel](https://developer.apple.com/documentation/usernotifications/unnotificationinterruptionlevel)
33. [Apple WWDC21 session 10091: Send communication and Time Sensitive notifications](https://developer.apple.com/videos/play/wwdc2021/10091/)
34. [Android developers: Create and manage notification channels](https://developer.android.com/training/notify-user/channels)
35. [ntfy docs: Publishing (priorities, action buttons)](https://docs.ntfy.sh/publish/)
36. [Pielot, Church, de Oliveira. An in-situ study of mobile phone notifications. MobileHCI 2014](https://ic.unicamp.br/~oliveira/doc/MHCI2014_An-in-situ-study-of-mobile-phone-notifications.pdf)
37. [Mehrotra et al. My Phone and Me: Understanding People's Receptivity to Mobile Notifications. CHI 2016](https://discovery.ucl.ac.uk/1502364/1/CHI16.pdf)
38. [Bainbridge L. Ironies of automation. Automatica 1983;19(6):775-779](https://gwern.net/doc/sociology/technology/1983-bainbridge.pdf); summary: [Colyer, The Morning Paper: Ironies of automation](https://blog.acolyer.org/2020/01/08/ironies-of-automation/)
39. [Parasuraman, Sheridan, Wickens. A model for types and levels of human interaction with automation. IEEE Trans SMC-A 2000;30(3):286-297](https://doi.org/10.1109/3468.844354)
40. [Endsley MR. Toward a theory of situation awareness in dynamic systems. Human Factors 1995;37(1):32-64](https://doi.org/10.1518/001872095779049543)
41. [Endsley MR. From here to autonomy: lessons learned from human-automation research. Human Factors 2017;59(1):5-27](https://doi.org/10.1177/0018720816681350)
42. [Cummings ML, Mitchell PJ. Predicting controller capacity in supervisory control of multiple UAVs. IEEE Trans SMC-A 2008;38(2):451-460 (MIT DSpace)](https://dspace.mit.edu/handle/1721.1/90280)
43. [Crandall JW, Cummings ML. Identifying predictive metrics for supervisory control of multiple robots. IEEE Trans Robotics 2007](https://students.cs.byu.edu/~crandall/papers/CrandallCummingsTRO2007_Final.pdf)
44. [Chemical Engineering: Alarm management by the numbers (EEMUA 191, ISA-18.2, IEC 62682 benchmarks)](https://www.chemengonline.com/alarm-management-numbers/)
45. [Mosier, Skitka et al. Automation bias and errors (NASA technical report)](https://ntrs.nasa.gov/citations/20020043049)
46. [Lee JD, See KA. Trust in automation: designing for appropriate reliance. Human Factors 2004;46(1):50-80](https://pubmed.ncbi.nlm.nih.gov/15151155/)
47. [Secondary citation of Wickens and Dixon (2007) on the ~70% reliability threshold (NSF PAR)](https://par.nsf.gov/servlets/purl/10517143)
48. [Simkute A, Tankelevitch L, Kewenig V, Scott AE, Sellen A, Rintel S. Ironies of Generative AI. Int J Human-Computer Interaction 2024 (arXiv)](https://arxiv.org/abs/2402.11364)
49. [ISA Interchange: Modern DCS graphics, Level 1 overview displays](https://blog.isa.org/modern-dcs-graphics-level-1-overview-displays); [Control.com: Going gray, a new HMI standard](https://control.com/technical-articles/going-gray/)
50. [crowsnest README](https://github.com/thorwhalen/crowsnest); [openloops README](https://github.com/thorwhalen/openloops)
51. [A principal model and a review organisation for a solo operator of many coding agents (phase-1 research, this repository)](principal-model-and-review-organisation.md)

Note on coverage: the web-search budget for this session ran out before two planned checks (field studies of notification batching, and interruption timing at task breakpoints); those topics are therefore not cited here and should not be assumed either way.
