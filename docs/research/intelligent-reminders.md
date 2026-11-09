# Reminders with inferred timing and channel

Research report, deep tier, 2026-11-09. Design question only: no private material was used. Theme prompt: `docs/research/prompts/intelligent-reminders.md`.

## Summary and recommendation

**The question.** A principal says "remind me of X", with or without a time, and a secretary agent decides when and how to remind. The principal reads on a phone; the outputs are push notifications, chat, voice and a morning brief.

**Verdict in one paragraph.** Treat "remind me" as two decisions, not one parse. First, *interpret*: turn what was said into a trigger that keeps its uncertainty (an exact time, a window, an event, or nothing), because people rarely mean a minute when they name a time [19] and literal resolution is what sends a "this afternoon" reminder at 12:00 while the person is at lunch [19]. Second, *deliver*: inside the window, wait for a natural break and deliver then, with a hard upper bound. This is Horvitz's *bounded deferral* [10], and it is cheap: in the original studies most busy spells ended within one to two minutes, and a four-minute bound cut alerts during busy time from 20 to 4 for one person and from 80 to 0 for another [10]. Delivering at breakpoints lowers frustration and reaction time [12]. A reminder with **no time and no condition goes to the next brief, silently, never to a push**; a reminder with a **condition** ("when the PR merges", "when I next talk to X") becomes an event trigger with a fallback date, because event cues are remembered more reliably than clock times [1] and if-then plans are the best-evidenced memory aid there is [4][5]. Escalation (brief → quiet push → push → insistent) is reserved for items that carry a deadline and stay unacknowledged.

**The five strongest recommendations.**

1. **Model a reminder as intent + trigger policy + channel policy + escalation + a `done_when` check, with an append-only log.** Keep it exportable to the calendar standards that already encode most of this: VALARM with `ACKNOWLEDGED`, snooze as a related alarm, and `PROXIMITY` triggers for arriving, leaving and car connection (RFC 9074) [44]; JSCalendar alerts with offset or absolute triggers and `acknowledged` (RFC 8984) [45].
2. **"No time given" defaults to the next brief, at least about two hours away.** A reminder captured in the evening lands in tomorrow's morning brief: Cortana's logs show people plan in the evening and want the reminder the next day, with a visible bump at 8 to 9 hours after creation [18]. Date-only reminders get a fixed morning slot in every shipped product checked: Apple Reminders fires at 9:00 by default [20], Slack's day-based reminders at 9 a.m. [28], Gmail's snooze presets use 8 a.m. [32]. Use the same slot table crowsnest's `later` presets already use (18:00 evening, 09:00 morning) [60] so there is one source of truth.
3. **Echo every capture in one line with the resolved trigger and channel** ("Tue 10 Nov, 10:00, push" or "tomorrow's morning brief"). The echo catches misparses, and making a concrete plan is itself what removes the nagging cost of an unfinished goal [8].
4. **Do not make a date parser the front door.** On a 20-phrase test (section E), no Python library handled bare imprecise expressions ("later", "this weekend", "in a couple of days") or recurrence; two of the most-used libraries resolved "tomorrow at 10" to the wrong hour. Let the LLM that already runs the secretary extract a structured trigger, and check it deterministically: `dateparser` (BSD-3, maintained) for explicit dates, a configurable slot table for imprecise ones, `dateutil.rrule` for recurrence. Duckling, the only rule-based parser that returns intervals and grains, is the optional upgrade behind a seam.
5. **Run the fire path as deterministic code on an always-on machine**: one store, a stateless `tick` every 60 s from launchd or a systemd timer, plus event hooks that call the same evaluator, with an idempotency key per (reminder, occurrence, level). Claude Code's cloud scheduled tasks (minimum hourly cadence, a fresh clone with no local files) [38] and session-scoped `CronCreate` tasks (fire only while that session is open, recurring ones expire after 7 days, no catch-up) [37] are the wrong grain for durable reminders; use them only for "remind me in 20 minutes" inside a live session.

## What already exists in the fleet

- **crowsnest** has a snooze, not a reminder: `crowsnest later <item>` with the presets `1h`, `evening` (today at 18:00, and from 18:00 on it means tomorrow morning), `tomorrow` (09:00) and `change` (no time; wakes when the item changes), with the hours configurable [60]. The `change` preset is already an event-based trigger.
- **openloops** tracks obligations; its `manual-task` issues carry a shell predicate that `ol owed` re-checks [61]. That is a working `done_when` mechanism. Its `Task` schema has no due date or remind-at field.
- **correspond** is a channel facade with the same verbs over GitHub, Gmail, Slack, Telegram, Signal, ntfy, Apprise and macOS notifications [62]. It is the delivery layer; it has no scheduling.
- **annals** publishes documents to a phone-first in-tray with a Listen button [63]: the natural carrier for the brief.
- Scheduling today is launchd interval jobs only. No reminder component exists.

## A. The cognitive basis

### A1. Prospective memory: event cues beat clock times

*Prospective memory* is remembering to carry out an intention at the right future moment [1][3]. The field splits tasks into **event-based** (act when a cue appears: a person, a place, an object) and **time-based** (act at a time, or after a delay, with no external cue) [1][18]. People remember time-based intentions less reliably than event-based ones [1], because time-based tasks require self-initiated clock monitoring [1].

The **multiprocess framework** holds that retrieval runs either through strategic monitoring, which costs attention, or through spontaneous retrieval when a salient cue closely associated with the intention appears [2][3]. Distinctive, salient cues that are strongly associated with the intention work best [1].

Field work adds failure modes the lab split misses: interruptions, the absence of the cues that normally prompt a habitual task, habit capture, and multitasking [1]. People forget to resume interrupted tasks; a short pause or an explicit cue at the start or end of the interruption improves resumption significantly [1].

*Implication.* A secretary should prefer attaching a reminder to an event the system can observe (a session finishing, a PR merging, the principal opening the phone page, a message from a named person) over inventing a clock time. A time is the fallback, not the default.

### A2. Implementation intentions: the if-then plan

An *implementation intention* is an if-then plan: "when situation Y arises, I will do X" [4]. The 2006 meta-analysis (94 tests) reported a medium-to-large effect, d = 0.65 [4]. A 2024 meta-analysis of 642 tests is less favourable once publication bias is corrected; secondary summaries report d = 0.35 after trim-and-fill and d = 0.15 under robust Bayesian correction [5] (these figures come from a secondary summary; the abstract could not be retrieved here). Dismukes reports that, in tasks such as exercise and medication adherence, implementation intentions improved prospective-memory performance by as much as two to four times [1].

*Implication.* The honest claim is "the best-evidenced cheap aid, with a modest true effect". The design consequence holds either way: when the principal gives no time, help them phrase an if-then ("when you next open the board, …") rather than pick an arbitrary hour.

### A3. Offloading intentions, and why capture itself helps

People set external reminders strategically, more when memory load is high and distraction likely, and setting them improves fulfilment (four online experiments, N = 1,196) [6]. *Cognitive offloading* is the general name for using the environment or a device to reduce cognitive demand [7].

Unfulfilled goals intrude on thought; making a specific plan for them removes that cost, even before the goal is acted on [8]. This is the "consider it done" effect.

*Implication.* Capture has value at the moment of capture, provided the principal trusts it. The one-line echo ("tomorrow's morning brief") is the moment the offload completes. A reminder system that silently drops items destroys this value, so a missed fire must be visible (section F4).

## B. Interruption and opportune moments (HCI)

### B1. Horvitz: attention models, bounded deferral, and reminding by expected value

Horvitz's programme treats alerting as a decision under uncertainty: weigh the expected value of delivering information now against the expected cost of interrupting, given inferred attention [9]. The email triage system *Priorities* scored urgency and fed this decision [10].

**Bounded deferral**: if the user is busy when an alert arrives, hold it until they become free, but never beyond a preset maximum for that urgency class [10]. Across 113 users and 4,803 busy spells, the mean busy spell lasted 43 s (SD 52 s) and the great majority ended within one to two minutes [10]. In an email case study, a four-minute deferral cut alerts during busy time from 20 to 4 for one participant and from 80 to 0 for another; for the second, a little over a minute of deferral suppressed all high-urgency interruptions during busy time [10]. The resulting tool let users set a deferral per urgency class and showed a forecast of interruptions per hour [10].

**Jogger** applied the same reasoning to reminding: learned models of the probability that a person will forget something relevant in a context, combined with the cost of forgetting and the cost of interruption, decide whether and when to remind [11].

*Implication.* "Defer to a natural break" has a precise, tested form: a per-class maximum deferral, a breakpoint detector, and delivery at the earlier of the two. Every reminder needs a `max_deferral`.

### B2. Breakpoints

Iqbal and Bailey showed that models can detect breakpoints in a task reasonably well, though they struggle to tell breakpoint types apart, and that scheduling notifications at breakpoints reduces frustration and reaction time relative to immediate delivery [12]. Which breakpoint type suits a notification depends on its content [12].

*Breakpoints the fleet can observe without sensors:* the end of an agent turn the principal was watching, the principal opening the phone page or the brief, the end of a voice session, a calendar event ending, the principal replying to a message. These are coarse breakpoints and good delivery points.

### B3. The cost of interruption

In Mark, Gudith and Klocke's experiment, interrupted participants finished tasks *faster*, with no loss of quality, but reported more stress, frustration, time pressure and effort [13]. The often-quoted "23 minutes to resume" comes from a separate field study by Mark's group, not from that experiment [14] (not verified against the primary source here).

### B4. Notifications on phones

In a one-week in-situ study, 15 participants received 63.5 notifications a day on average, mostly from messengers and email, and viewed them within minutes whether or not the phone was in silent mode [15]; silent mode did not slow responses [15]. More notifications were associated with more negative emotion [15]. An experience-sampling study of 20 users and 10,372 notifications examined how alert modality, sender, presentation and the ongoing task shape perceived disruption and response time [16]. A field study of 337 users and 78,930 notifications built a model that predicts, before delivery, whether a person will engage; it achieved a 66.6% higher success rate than a naive baseline, and five-fold with the user's past behaviour [17].

*Implications.* A secretary's push competes with dozens of others a day [15], so it needs a budget. Silencing the phone does not protect the principal [15]; the sender has to be the one that holds back. Personal history is the strongest predictor of receptivity [17], which argues for learning the principal's own slot times from the log.

## C. How people actually phrase reminders

### C1. The Cortana logs

The first large-scale study of assistant reminders analysed 576,080 confirmed time-based reminders from 92,264 users of Cortana in the US (January–February 2015) [18]. Findings that matter here [18]:

- **Short horizons.** About 25% were set to fire within the same hour and about 80% within 24 hours; there are spikes at five-minute multiples, and a bump at 8 to 9 hours (overnight, or a working day).
- **Plan in the evening, act through the day.** Creation peaks later in the day; firing is spread from morning to evening. The later in the day a reminder is created, the more likely it fires the next day; Friday-evening reminders often fire on Monday morning.
- **Task types.** Go somewhere 33.0%, chores 23.8%, communicate 21.1%, manage an ongoing external process (oven, laundry) 12.9%, manage one's own activity 6.3%, eat or take medicine 2.8%. "Communicate/coordinate" (book an appointment) is planned furthest ahead; managing an external process has lead times of minutes.
- **Prediction.** Creation time alone predicted the day of firing with 0.63 accuracy against 0.51 for "same day"; adding the reminder text raised it to 0.68.
- **Dialogue.** When no time was given, Cortana asked "When would you like to be reminded?" and then confirmed the full reminder before saving.

### C2. Imprecise temporal expressions

Rong et al. call phrases like "later this afternoon" *imprecise temporal expressions* (ITEs) [19]. Respondents were rarely comfortable expressing minute-level precision (2% to 16% of answers for common tasks) [19]; they preferred imprecise expressions when not fully committed (75% agreement), when the task is far off (74%), and when it is low priority (65%) [19]. The long tail is long: 60.4% of the expressions in their email corpus occurred only once [19]. Their five design implications are, in short: respect uncertainty by delaying resolution; support the long tail; resolve uncertainty later, during down-time; use delivery strategies suited to the uncertainty; and use calendars and world knowledge [19].

*Implication.* Imprecision is information. "Later this week" from a principal who is not committed should become a window and a low channel level, not "Friday 17:00, push".

## D. How shipped products handle it

| Product | Time given | No time / date only | Event or context triggers | Snooze, repeat, completion |
|---|---|---|---|---|
| Apple Reminders | Date, time, repeat | Date-only fires at **9:00** by default, configurable [20] | Arrive/leave a place [20]; **When Messaging** a contact (Messages app only, one contact) [20][21]; getting in/out of the car (third-party guide) [22] | **Urgent** (iOS 26.2+) schedules an alarm that sounds through silent mode and Focus [20] |
| Siri | Spoken time | Asks | "When I get home" needs the home address on the user's contact card [22] | as Reminders |
| Google (Gemini) | Scheduled actions: run a prompt at a time or on a schedule, e.g. a morning briefing (June 2025, paid tiers) [23] | — | — | Reminders created in Gemini are reported to land in Google Tasks [25]; Assistant and Calendar reminders moving to Tasks in the second half of 2025 [24] (completion not verified) |
| Todoist | Natural-language dates; automatic reminder N minutes before a timed task [26] | **No automatic reminder for date-only tasks**, since defaults are relative to a time [26] | Location reminders (not checked) | — |
| Things 3 | Reminder needs a specific time [27] | "This Evening" is a section of Today, not a time [27] | — | — |
| Slack `/remind` | Natural language, custom time [28] | Day-based reminders arrive at **9 a.m.** local [28] | — | Snooze 20 min, 1 h, or next day; channel reminders cannot be snoozed; recurring reminders cannot be completed, only deleted [29] |
| Microsoft To Do | Later today, tomorrow, next week, custom [30] | "Next week" defaults to the start of the week at 9 AM (forum report) [31] | — | — |
| Gmail snooze and nudges | Snooze presets | Presets Morning 8 a.m., Afternoon 1 p.m., Evening 6 p.m. (third-party; mapping unverified) [32] | **Nudges** resurface received mail left unanswered and sent mail without reply "after a few days" [33] | — |
| Superhuman | "Remind me" with a time | A default reminder time set to when the user is most likely in the inbox (third-party summary of help pages) [35] | **"If no reply"**: the reminder is cancelled when a reply arrives [34]; Auto Reminders on emails still awaiting replies [35] | — |
| ChatGPT Tasks | Natural-language one-off or recurring | — | — | Push and/or email; active-task limits 3 (Free/Go), 5 (Plus), 10 (Business/Edu), 15 (Pro/Enterprise) [36] |
| Claude Code | "remind me at 3pm to …" creates a one-shot session task [37]; `/schedule tomorrow at 9am, …` creates a one-off cloud run [38] | — | GitHub events and API triggers for cloud runs [38] | — |

**What the products converge on.** (1) Date-only means a fixed morning slot, configurable: 9:00 (Apple, Slack), 8:00 (Gmail). (2) Imprecise times are offered as a small set of named slots (later today, this evening, tomorrow, this weekend, next week), not parsed freely. (3) The most useful event trigger for a secretary is the *conditional cancellation* pattern: "remind me unless X happens" (Superhuman's "if no reply", Gmail nudges). (4) "Urgent" is a separate, explicit, opt-in class that may break through silence (Apple's Urgent; iOS critical alerts). (5) None of them infers *when* from the reminder's content; Cortana's research shows that it is possible [18], but no product checked ships it.

## E. Natural-language time parsing in Python

### E1. The libraries

| Library | Licence | Latest release (PyPI) | Status | What it returns |
|---|---|---|---|---|
| `dateparser` [46] | BSD-3-Clause | 1.5.0, 2026-11-04 | Active | A `datetime`; `DateDataParser.get_date_data` also returns a *period* (`day`, `week`, `month`, `time`), which reveals date-only input; `search_dates` finds dates inside sentences |
| `parsedatetime` [47] | Apache-2.0 | 2.6, 2020-05-31 | Repository active, no release since 2020 | A `datetime` plus flags (`hasDate`, `hasTime`, accuracy); fills missing times with 09:00 |
| `ctparse` [48] | MIT | 0.3.6, 2022-11-28 | **Archived** | A partial `Time` with unknown fields (e.g. "tomorrow, evening, hour unknown"), or an `Interval` |
| Duckling [49] | BSD | Haskell server; Python wrapper `duckling` 1.8.0, 2018 [50] | Core repository maintained lightly (last commit 2026-03); the commonly used Docker image dates from 2022 [55] | Values with **grain** (`hour`, `day`, `week`) and **intervals** (`this weekend` → Fri 18:00 to Mon 00:00) |
| `timefhuman` [51] | Apache-2.0 | 0.1.5, 2026-04-05 | Active, small | `datetime`s from free sentences |
| `python-dateutil` [52] | Apache-2.0 / BSD-3 (dual, by contributor) | 4.0.0, 2026-11-01 | Active; 3.0 rewrote `tz.tzfile`, changed offsets for nonexistent times, and now uses `tzdata` [52] | `parser` is a format parser, not an NL one; **`rrule`** implements RFC 5545 recurrence; `tz` |
| Recognizers-Text [53] | MIT | Python package 1.0.2a2, 2019 | Python port stale (C# is the main line) | Typed date-time resolutions, many languages |
| chrono [54] | MIT | (JavaScript) | Active | For a browser or TypeScript surface |

### E2. A small test

Twenty reminder phrases, reference time Monday 2026-11-09 21:30 (late evening, chosen so that "this afternoon" and "tonight" are already past or ongoing), English, Python 3.12. Versions: dateparser 1.5.0 (`PREFER_DATES_FROM=future`, `languages=['en']`), parsedatetime 2.6, ctparse 0.3.6, timefhuman 0.1.5, dateutil 2.9 (`fuzzy=True`), Duckling via the `rasa/duckling` Docker image. The script is the author's own; marking is the author's judgement, not a benchmark.

| Phrase | dateparser | parsedatetime | ctparse | timefhuman | dateutil | Duckling |
|---|---|---|---|---|---|---|
| in 20 minutes | 21:50 | 21:50 | +20 min | 21:50 | 21:20 ✗ | 21:50 |
| tomorrow | Tue **21:30** (copies now) | Tue 09:00 | Tue, time unknown | Tue 00:00 | – | Tue, grain day |
| tonight (at 21:30) | Mon 20:00 (past) | Mon 21:00 (past) | Tue evening ✗ | Mon 20:00 (past) | – | Mon 18:00–24:00 |
| this afternoon (already over) | Mon 15:00 (past) | Mon 13:00 (past) | Tue afternoon | Tue 15:00 | – | Mon 12:00–19:00 (past) |
| later | – | – | Tue evening | – | – | – |
| later this week | Mon 21:30 ✗ | Fri 17:00 | Tue evening ✗ | – | – | this week, grain week |
| this weekend | – | – | – | – | – | Fri 18:00–Mon 00:00 |
| end of day | – | Mon 17:00 | – | – | – | Tue 00:00 |
| in a couple of days | – | – | – | – | – | Wed 21:00 |
| first thing tomorrow | Tue 21:30 ✗ | Tue 09:00 | Tue | Tue 00:00 ✗ | – | Tue, grain day |
| every monday at 9 | one date, wrong | one date | one date | Mon 09:00 (past) | one date | Mon 09:00 (past) |
| remind me to call the bank tomorrow at 10 | Tue **21:30** ✗ | Tue **09:00** ✗ | Tue 10:00 | Tue 10:00 | Tue 21:30 ✗ | Tue 10:00 |
| next tuesday | Tue 10 | Tue 17 | Tue 17 | Tue 10 | Tue 10 | Tue 17 |

Totals over the 18 unambiguous phrases (excluding "next tuesday", which people split on, and "before my 2pm meeting", which needs a calendar): Duckling 11 right and 3 partly right; ctparse 7 and 2; parsedatetime 6 and 4; dateparser 5 and 2; timefhuman 5 and 2; dateutil 0 and 1. Per phrase, all ran in under 3 ms once warm (Duckling was not timed, since it runs as a separate HTTP service).

**What the test shows.**

- **No library returns a recurrence.** "Every Monday at 9" comes back as a single date everywhere. Recurrence must be extracted separately and stored as an RRULE.
- **No library flags a window that has already passed.** At 21:30, "this afternoon" resolves to a past time or silently to tomorrow. The secretary must detect this and ask or roll forward with an echo.
- **Date-only handling differs and is a trap.** dateparser copies the current time of day into "tomorrow" (Tue 21:30); parsedatetime substitutes 09:00; Duckling and ctparse keep "day" as a grain. dateparser's `period` field (`day`) lets the caller detect date-only input and apply the slot table instead.
- **"tomorrow at 10" fails in two of the most-used libraries** (dateparser returns 21:30, parsedatetime 09:00), because a bare "10" has no am/pm. This belongs in a regression set.
- **`search_dates` without `languages=['en']` produced false positives:** in "remind me to call the bank…" it read "me" as a date. Always pin the language.
- **Only Duckling has the right output shape** for imprecise expressions: intervals and grains. ctparse's partial times are the right idea, but the project is archived.
- **"next tuesday" is genuinely ambiguous**, and the libraries split three against three. Ask whenever the two readings differ.

### E3. Verdict

Use the LLM the secretary already runs to extract a typed trigger (schema in section F1): the verbatim phrase, the kind (time, window, event, none), the window bounds, a grain, any RRULE, any condition, and a confidence. Check it deterministically: **`dateparser`** for explicit absolute and relative times (with `RELATIVE_BASE`, `PREFER_DATES_FROM='future'`, `languages=['en']`, and the period check), a **slot table** for the closed set of named imprecise expressions (shared with crowsnest's `later` presets [60]), and **`dateutil.rrule`** for recurrence and `zoneinfo` for time zones. Disagreement between the LLM and the check, a past window, or an ambiguous weekday triggers one clarifying question with a pre-filled default. Keep **Duckling** behind a `time_resolver` seam as the upgrade if the long tail of imprecise expressions turns out to matter; it costs a Haskell service or a Docker container. Do not depend on ctparse (archived) or on the stale `duckling` and Recognizers-Text Python packages.

## F. The recommended model

### F1. Data model

A reminder is an obligation with a trigger. The core fields, as a schema sketch (field names illustrative):

```yaml
Reminder:
  id: stable id                      # also the idempotency root
  intent:
    text: "renew the domain"         # what to do, in the principal's words
    verbatim: "remind me to renew the domain next week"
    refs: [issue url, session name, person]   # what it is about
  created: {at: UTC instant, tz: Europe/Paris, via: voice|chat|board}
  trigger:                           # exactly one kind, plus a fallback
    kind: time | window | event | none
    at: UTC instant                  # kind=time
    window: {start, end, grain}      # kind=window ("this afternoon")
    event: {source: crowsnest|github|correspond|calendar|presence, match: ...}  # kind=event
    floating: true|false             # "9am wherever I am" vs a fixed instant
    rrule: "FREQ=WEEKLY;BYDAY=MO;BYHOUR=9"   # optional recurrence (RFC 5545)
    fallback: {after: P7D, to: brief}       # when an event never happens
  timing: {max_deferral: PT20M, breakpoints: [turn_end, page_open, voice_end, meeting_end]}
  channel: {start: L1, max: L3, respect_quiet_hours: true, preview: full|redacted}
  escalation: [{after: PT30M, to: L3}]      # only while unacknowledged
  done_when: {predicate: "gh issue view … --json state -q .state == CLOSED"}  # optional
  interpretation: {by: llm+dateparser, confidence: 0.8, echo: "Mon 16 Nov, morning brief"}
  state: scheduled | due | delivered | seen | acknowledged | snoozed | done | dismissed | expired | cancelled
  log: append-only events (created, interpreted, fired, delivered(level, channel, key), seen, snoozed(child id), escalated, done(evidence), corrected(by principal))
```

**Mapping to standards**, so reminders can later be exported to a calendar: an iCalendar `VTODO` with `VALARM`s [43]; snooze as a sibling `VALARM` with `RELATED-TO;RELTYPE=SNOOZE` and the original marked `ACKNOWLEDGED` (RFC 9074) [44]; `PROXIMITY` values `ARRIVE`, `DEPART`, `CONNECT` and `DISCONNECT` (Bluetooth car) for context triggers [44]; JSCalendar `Alert` with an `OffsetTrigger` relative to start or end, or an `AbsoluteTrigger`, and an `acknowledged` timestamp [45].

### F2. Decision rules for timing

Apply in order. Slot hours, gaps and lead times below are **design defaults (estimates)**, configurable, and meant to be re-fitted to the principal's own log.

- **R1, precise time given** ("Friday at 3pm", "in 20 minutes"): fire at that time, level L3. Honour it even inside quiet hours, because the principal chose it. Echo the absolute day and date.
- **R2, imprecise time given** ("this afternoon", "later", "next week"): map it to a window with the slot table, then fire at the first breakpoint inside the window, with `max_deferral` putting the deadline at the window's end. Start at L2 and rise to L3 at the window's end if unseen. Default windows: morning 09:00–11:00; afternoon 14:00–17:00; evening and "tonight" 18:00–20:00; end of day 16:30–17:30; "later" (bare) the next breakpoint at least 2 h away; "later this week" Wednesday to Friday in the brief; "this weekend" Saturday's morning brief; "next week" Monday's morning brief. If the window has already passed (the "this afternoon" at 21:30 case), roll forward to the next occurrence *and say so*, or ask.
- **R3, condition given** ("when the PR merges", "when I next talk to X", "when session Y finishes"): event trigger, delivered in context where possible (inside the item, the thread, or the session view), otherwise L2. Always attach a fallback: if the event has not happened within a default of 7 days, the item appears in the brief as "still waiting for …".
- **R4, no time, but the content implies a deadline** (a date in the text, a linked issue's due date, a calendar event): deliver at a lead time before the deadline. Choose the lead by task type, following Cortana's finding that coordination is planned furthest ahead [18]: a quick action, 1 h before (L3); a preparation task, the brief one working day before (L1); anything that depends on other people, the brief two to three working days before (L1).
- **R5, no time, no condition, no deadline** ("remind me to look at X"): capture silently to the desk, and include it in the **next brief at least about 2 h away**. A reminder captured in the evening lands in tomorrow's morning brief, matching the overnight bump in Cortana's logs [18]. Never push. If it has been carried through several briefs without action (default 3), ask once: "keep, schedule, or drop?"
- **R6, clarify only when it pays:** a past window; an ambiguous weekday ("next Tuesday" when the two readings differ); disagreement between the LLM and the deterministic check; or high stakes with low confidence. Ask one question, with the default pre-filled ("Tuesday the 17th?"). Otherwise default and echo; Cortana's ask-then-confirm dialogue [18] costs a turn the principal may not have on a phone.
- **R7, always echo** the resolved trigger and channel in one line at capture.

### F3. Channels, escalation, quiet hours

**The ladder.** L0 desk (a silent list); L1 brief (a morning or evening digest page, readable or listened to); L2 quiet push (ntfy priority 2 "no vibration or sound" [41], iOS `passive` "without lighting up the screen or playing a sound" [39], Pushover −1 [42]); L3 push (ntfy 3–4, iOS `active` or `timeSensitive`, which "breaks through system notification controls" [39], Pushover 0–1); L4 insistent (ntfy 5, Pushover emergency priority 2, which repeats until acknowledged with `retry` ≥ 30 s and `expire` ≤ 3 h and returns a receipt [42], or a voice call; iOS `critical` "bypasses the mute switch" [39] and needs an Apple entitlement). On Android the user, not the sender, has final control over a channel's importance once created [40], so map each level to its own channel.

**Starting levels.** R1 starts at L3; R2 at L2; R3 in context or at L2; R4 at L1 or L3 depending on the lead time; R5 at L1.

**Escalation rules.**

- Escalate only reminders that have a deadline, and only while they are unacknowledged.
- Move one level per step, and never above the `max` level declared at capture.
- Use L4 only when the principal said "make sure", "urgent" or similar, mirroring Apple's explicit Urgent switch [20].
- During quiet hours, hold L2 and L3 and re-route them to the next brief or to the end of quiet hours. This is Pushover's rule, where normal priority is downgraded during quiet hours [42]. Two exceptions: R1 times the principal chose inside quiet hours, and L4.
- Keep a **daily push budget** (default 5 reminder pushes a day, an estimate) and send the overflow to the brief. The baseline is already 63.5 notifications a day [15], and alert streams that are never filtered end in overrides: 49–96% of drug-safety alerts were overridden in one review [59].
- **Batch.** Reminders due within the same breakpoint go out as one push listing them.

### F4. Snooze, repeat, acknowledgement and "was it acted on"

- **Snooze** acknowledges the occurrence and creates a child occurrence with an absolute trigger, linked to its parent (RFC 9074) [44]. Presets: 20 min, 1 h (Slack's) [29], this evening, tomorrow morning, "at my next break", and "when it changes" (crowsnest's) [60]. Count snoozes. A third snooze offers "move to the brief, or drop?": repeated snoozing signals bad timing or an intent that is not real (a design heuristic, not sourced).
- **Repeat** is an RRULE on the reminder. Each occurrence has its own state. Completing an occurrence ("done for today") is distinct from ending the series ("stop"). Slack's rule that a recurring reminder can only be deleted, never completed [29], is the confusion to avoid.
- **A nag is escalation, not recurrence.** "Repeat until I acknowledge" belongs in `escalation`, bounded by `expire` (as in Pushover [42]).
- **Acknowledged versus done.** Acknowledged means the principal saw the reminder and took it in; done means the intent was carried out. Evidence of *done* comes, strongest first, from: a `done_when` predicate that passes (an issue closed, a PR merged, an email sent), as openloops already does for manual tasks [61]; an explicit tap; or inferred evidence, which is logged as "done (inferred)" and shown in the brief for confirmation. A predicate that passes before the fire cancels the reminder, in the pattern of Superhuman's "if no reply" [34].
- **A missed fire must be visible.** The morning brief lists the reminders that were due in the last 24 hours and their states, so a silent failure surfaces within a day.

## G. Running the scheduler

| Option | Strengths | Limits | Use for |
|---|---|---|---|
| **launchd** `StartCalendarInterval` / a systemd timer running a stateless `tick` | Already the fleet's pattern; launchd runs a calendar job missed during sleep once on wake, coalescing the misses [56] | launchd cannot wake a sleeping Mac [57]; `StartInterval` misses during sleep are skipped [57] | **The fire path**, on an always-on machine; a Mac copy only as a fallback |
| Claude Code cloud scheduled tasks ("routines") | Run with the laptop closed; one-off runs at a timestamp; GitHub and API triggers; connectors [38] | Cadence of at most once an hour; a fresh clone with no local files; runs on the hour may start several minutes late; 100 scheduled runs an hour per account; each run is a full session [38] | Composing a brief on a server-less day; not per-reminder firing |
| `CronCreate` / `/loop` (session-scoped) | Natural language in-session ("remind me at 3pm to …") [37] | Fire only while the session is running and idle; recurring tasks expire after 7 days; no catch-up for missed fires; at most 50 per session; one-shots at :00 or :30 may run up to 90 s early [37] | "Remind me in 20 minutes" inside a live session |
| A long-running event loop (e.g. APScheduler with a persistent store) | Precise timing; `misfire_grace_time` and `coalesce` handle missed runs; `max_instances` caps concurrency [58] | A daemon to keep alive; each persistent job needs an explicit id and `replace_existing=True`, or restarts duplicate it [58] | Only if a 60 s tick proves too coarse |

**Recommendation.** One reminder store (rows plus the append-only log) and a pure function `evaluate(reminder, now, events) -> actions`, called by (a) a `tick` every 60 s from a timer on an always-on machine and (b) event hooks: crowsnest session events, GitHub polling, correspond's inbound messages, and the principal opening the page. No LLM runs on the fire path; the LLM runs at capture time and when the brief is composed.

**Idempotency.**

- Each delivery has a key `hash(reminder_id, occurrence_start, level)`.
- Delivery follows the *outbox* pattern: write `delivering(key)`, send, then write `delivered(key)`. A tick that finds `delivering` without `delivered` after a timeout retries with the same key. At-least-once delivery plus the key gives effectively-once behaviour on any channel that de-duplicates, and at worst one duplicate on channels that do not (not verified per channel).
- **Misfire policy:** if a tick ran late (the machine slept or restarted), deliver late with "late by N min" when within a grace period (default 15 min); beyond it, downgrade to the brief. This is the APScheduler `misfire_grace_time` idea [58] applied to people.
- **Time:** store instants in UTC with the IANA zone name; mark *floating* reminders ("9am wherever I am") explicitly; use `zoneinfo` and `dateutil` 3+, whose rewrite changed the offsets of nonexistent (DST-gap) times [52].
- A heartbeat row written by every tick; the brief and the phone page show "scheduler last ran N min ago".

## H. Evaluation

| Question | Metric | Source of truth |
|---|---|---|
| Was it understood? | Correction rate ("no, I meant …") per phrase class; agreement between the LLM and the deterministic check; a regression set of phrases (start from section E2) | `interpretation` and `corrected` log events |
| Did it fire at a good moment? | Delivery-to-seen latency; share delivered at a breakpoint versus at the deferral bound; pushes per day against the budget; deliveries inside quiet hours | `delivered` and `seen` events |
| Was it acted on? | Done within the window after firing (by predicate, tap or confirmed inference); snooze rate and snooze-chain length; dismissed without action; expired without acknowledgement | `done`, `snoozed`, `expired` events |
| Did it fire at all, and only when needed? | Missed fires (due but not delivered); false fires (`done_when` already true at fire time); "you didn't remind me" reports | heartbeat; predicate re-check |
| Is the principal receptive? | Optional one-tap "good moment?" on 1 in N deliveries (experience sampling, as in [16]), kept rare | ESM answers |

**Learning loop.** Re-fit the slot hours to the moments the principal actually opens items (creation time and personal history are the strongest predictors [17][18]); calibrate the default lead times per task type from completion data; when a phrase class keeps being corrected, either add it to the slot table or make it a clarifying question.

## I. Standard terminology

| Term | Meaning | Source |
|---|---|---|
| Prospective memory | Remembering to perform an intended action at the right future moment | [1][3] |
| Event-based / time-based / activity-based | Cue is an external event / a clock time or delay / finishing an activity | [1][18] |
| Multiprocess framework | Retrieval by strategic monitoring or by spontaneous cue-driven retrieval | [2][3] |
| Implementation intention | An if-then plan linking a situation to an action | [4] |
| Cognitive (intention) offloading | Using external aids to reduce internal memory demands | [6][7] |
| Interruptibility, cost of interruption | How disruptive delivering now would be | [9][11] |
| Breakpoint | A boundary between task units, a good moment to interrupt | [12] |
| Opportune moment, receptivity | When the person is open to engage with a notification | [16][17] |
| Bounded deferral | Hold an alert until the person is free, up to a maximum delay per urgency class | [10] |
| Attention-sensitive alerting | Alerting decided by expected value given inferred attention | [9][10] |
| Imprecise temporal expression (ITE) | "later this week", "this afternoon" | [19] |
| Grain | The resolution of a time value (hour, day, week) | [49] |
| Floating time | Local time without a fixed zone ("9am wherever I am") | [43] |
| Alarm / alert / trigger (absolute, offset) | The firing part of a reminder; relative to start/end or at an instant | [43][44][45] |
| Lead time | How long before the deadline a reminder fires | [18] |
| Snooze | Postpone a fired alarm; in RFC 9074, a related alarm | [44] |
| Acknowledged | The principal has registered the alarm (≠ done) | [44][45] |
| Interruption level | passive, active, time-sensitive, critical (iOS); importance (Android) | [39][40] |
| Quiet hours / Focus / DND | Periods when non-urgent delivery is held | [20][42] |
| Escalation / nag | Raise the level, or repeat, while unacknowledged | [42] |
| Recurrence rule (RRULE) | RFC 5545 rule for repeating occurrences | [43] |
| Misfire, coalesce | A run missed while the scheduler was down; merging missed runs into one | [56][58] |
| Idempotency key, outbox | Make retried deliveries safe | (standard practice) |

## J. Things worth knowing that were not asked

1. **Capture is half the product.** Plan-making removes the cognitive cost of an open goal [8], and reminders are set adaptively under memory load [6]. The echo line and the visible "missed fires" list are what make the principal trust the offload. Without trust, they keep the intention in their head and the system adds nothing.
2. **Reminders and openloops' obligations are the same object.** A reminder is an obligation with a trigger. Put it on the same desk and in the same brief as openloops items rather than in a second inbox, and reuse openloops' predicate re-check for `done_when` [61].
3. **Implicit reminders are the industry direction**: Gmail nudges [33], Superhuman Auto Reminders [35]. In the fleet the analogue is detecting "I'll look at that tomorrow" in a session or a message. Make these *proposals* on the desk, never silent pushes, because a false implicit reminder costs trust at the same rate as a missed explicit one.
4. **"Remind me when session X finishes" is the cheapest high-value case.** crowsnest already observes the event, and its `later … change` preset is already an event trigger [60]. Start there before any time parsing.
5. **The past-window bug is universal.** Every parser tested resolved "this afternoon" at 21:30 to the past or silently to tomorrow (section E2). The fix is a rule (R2/R6), not a better parser.
6. **Privacy on the lock screen and in voice.** A reminder shown on a lock screen or read aloud can expose its content. Give each reminder a `preview: redacted` option (title only, "1 reminder") and default it on for anything about people or money (design advice, not sourced).
7. **Time zones and travel.** Decide per reminder whether it is floating or fixed [43]. "Take medication at 9" travels with the principal; "call at 3pm New York time" does not.
8. **Keep the LLM off the fire path.** Cost and latency are part of it, but the main reason is determinism: the same store and clock must always produce the same deliveries, which the log can then explain.
9. **Do not over-learn.** Personal models of receptivity work [17], but a solo principal produces a small log. Fit a handful of parameters (slot hours, lead times, a budget) before anything richer.

## What this means for the build

- A `reminders` component: a store plus an append-only log, with the schema in F1. It is exportable to VTODO/VALARM and RFC 9074, and shares the desk with openloops items.
- Capture runs inside the secretary: the LLM extracts a typed trigger, `dateparser`, the slot table and `rrule` check it, and the secretary echoes one line. Rules R1–R7 decide timing; "no time" means the next brief at least about 2 h away, never a push.
- One slot table (morning 09:00, evening 18:00, …), shared with crowsnest's `later` presets, configurable and later re-fitted from the log.
- The fire path is a deterministic `tick` every 60 s on an always-on machine, plus event hooks (crowsnest, GitHub, correspond, page opens), with an outbox, an idempotency key per delivery, a misfire grace period and a heartbeat.
- Delivery goes through correspond: L1 is the brief on annals, L2/L3 are ntfy priorities, L4 is opt-in only. It applies bounded deferral to breakpoints, quiet hours and a daily push budget.
- `done_when` predicates reuse openloops' mechanism; snooze creates a child occurrence; every state change is logged.
- The first slice is "remind me when session X finishes / in N minutes / tomorrow"; imprecise phrases, implicit reminders and Duckling come later, each behind a seam.
- Evaluation comes from the log from day one: correction rate, delivery-to-seen latency, acted-on rate, snooze chains and missed fires.

## REFERENCES

1. Dismukes RK. Prospective Memory in Workplace and Everyday Situations. Current Directions in Psychological Science. 2012;21(4):215-220. [doi:10.1177/0963721412447621](https://doi.org/10.1177/0963721412447621); [PDF](https://www.interruptions.net/literature/Dismukes-CurrDirPsycholSci12.pdf)
2. McDaniel MA, Einstein GO. Strategic and automatic processes in prospective memory retrieval: a multiprocess framework. Applied Cognitive Psychology. 2000;14:S127-S144. [doi:10.1002/acp.775](https://doi.org/10.1002/acp.775)
3. Einstein GO, McDaniel MA. Prospective Memory: Multiple Retrieval Processes. Current Directions in Psychological Science. 2005. [doi:10.1111/j.0963-7214.2005.00382.x](https://doi.org/10.1111/j.0963-7214.2005.00382.x)
4. Gollwitzer PM, Sheeran P. Implementation Intentions and Goal Achievement: A Meta-analysis of Effects and Processes. Advances in Experimental Social Psychology. 2006;38:69-119. [doi:10.1016/S0065-2601(06)38002-1](https://doi.org/10.1016/S0065-2601(06)38002-1)
5. Sheeran P, Listrom O, Gollwitzer PM. The when and how of planning: Meta-analysis of the scope and components of implementation intentions in 642 tests. European Review of Social Psychology. 2024. [doi:10.1080/10463283.2024.2334563](https://doi.org/10.1080/10463283.2024.2334563) (bias-corrected effect sizes taken from a secondary summary; abstract not retrieved)
6. Gilbert SJ. Strategic offloading of delayed intentions into the external environment. Quarterly Journal of Experimental Psychology. 2015. [doi:10.1080/17470218.2014.972963](https://doi.org/10.1080/17470218.2014.972963)
7. Risko EF, Gilbert SJ. Cognitive Offloading. Trends in Cognitive Sciences. 2016. [doi:10.1016/j.tics.2016.07.002](https://doi.org/10.1016/j.tics.2016.07.002)
8. Masicampo EJ, Baumeister RF. Consider it done! Plan making can eliminate the cognitive effects of unfulfilled goals. Journal of Personality and Social Psychology. 2011. [doi:10.1037/a0024192](https://doi.org/10.1037/a0024192)
9. Horvitz E, Kadie CM, Paek T, Hovel D. Models of attention in computing and communication: from principles to applications. Communications of the ACM. 2003;46(3):52-59. [doi:10.1145/636772.636798](https://doi.org/10.1145/636772.636798)
10. Horvitz E, Apacible J, Subramani M. Balancing Awareness and Interruption: Investigation of Notification Deferral Policies. User Modeling 2005, LNAI 3538:433-437. [PDF](https://www.erichorvitz.com/bdef_studies.pdf)
11. Kamar E, Horvitz E. Jogger: Models for Context-Sensitive Reminding. AAMAS 2011. [Microsoft Research](https://www.microsoft.com/en-us/research/publication/jogger-models-for-context-sensitive-reminding/); [PDF](https://www.erichorvitz.com/jogger_aamas.pdf)
12. Iqbal ST, Bailey BP. Effects of intelligent notification management on users and their tasks. CHI 2008:93-102. [doi:10.1145/1357054.1357070](https://doi.org/10.1145/1357054.1357070)
13. Mark G, Gudith D, Klocke U. The cost of interrupted work: more speed and stress. CHI 2008:107-110. [doi:10.1145/1357054.1357072](https://doi.org/10.1145/1357054.1357072); [PDF](https://interruptions.net/literature/Mark-CHI08.pdf)
14. Mark G. Presentation at the UC Irvine KM Forum 2008 (source of the "23 min 15 s" resumption figure; not retrieved). [isr.uci.edu](https://isr.uci.edu/events/KM-Forum-2008/presentations/Mark-KM-Forum08.pdf)
15. Pielot M, Church K, de Oliveira R. An in-situ study of mobile phone notifications. MobileHCI 2014:233-242. [doi:10.1145/2628363.2628364](https://doi.org/10.1145/2628363.2628364); [PDF](https://ic.unicamp.br/~oliveira/doc/MHCI2014_An-in-situ-study-of-mobile-phone-notifications.pdf)
16. Mehrotra A, Pejovic V, Vermeulen J, Hendley R, Musolesi M. My Phone and Me: Understanding People's Receptivity to Mobile Notifications. CHI 2016:1021-1032. [doi:10.1145/2858036.2858566](https://doi.org/10.1145/2858036.2858566); [PDF](https://discovery.ucl.ac.uk/1502364/1/CHI16.pdf)
17. Pielot M, Cardoso B, Katevas K, Serrà J, Matic A, Oliver N. Beyond Interruptibility: Predicting Opportune Moments to Engage Mobile Phone Users. Proc. ACM IMWUT. 2017. [doi:10.1145/3130956](https://doi.org/10.1145/3130956)
18. Graus D, Bennett PN, White RW, Horvitz E. Analyzing and Predicting Task Reminders. UMAP 2016:7-15. [doi:10.1145/2930238.2930239](https://doi.org/10.1145/2930238.2930239); [PDF](https://www.erichorvitz.com/Cortana-reminders_UM_2016.pdf)
19. Rong X, Fourney A, Brewer RN, Morris MR, Bennett PN. Managing Uncertainty in Time Expressions for Virtual Assistants. CHI 2017:568-579. [doi:10.1145/3025453.3025674](https://doi.org/10.1145/3025453.3025674); [PDF](https://www.adamfourney.com/papers/rong_chi2017.pdf)
20. Apple. Use Reminders on your iPhone or iPad. [support.apple.com/102484](https://support.apple.com/102484)
21. iDownloadBlog. How to get task reminder when you message a person. 2020. [idownloadblog.com](https://www.idownloadblog.com/2020/11/05/remind-when-messaging-tutorial/)
22. iPhone Life. How to Use Location-Based Reminders on iPhone. [iphonelife.com](https://iphonelife.com/blog/32671/tip-day-how-set-and-use-location-based-reminders)
23. Google. Scheduled actions in the Gemini app. 2025-06-06. [blog.google](https://blog.google/products/gemini/scheduled-actions-gemini-app/)
24. Android Authority. Google Calendar reminders migration to Tasks. 2025-07. [androidauthority.com](https://www.androidauthority.com/google-calendar-reminders-migration-to-tasks-3580106)
25. 9to5Google. Gemini reminders via Google Tasks. [9to5google.com](https://9to5google.com/?p=607012)
26. Todoist. Introduction to reminders. [todoist.com/help](https://www.todoist.com/help/articles/introduction-to-reminders-9PezfU)
27. Cultured Code. Things support: reminders. [culturedcode.com](https://culturedcode.com/things/support/articles/2803579/)
28. Slack. Set a reminder. [slack.com/help](https://slack.com/help/articles/208423427-Set-a-reminder)
29. Slack (older help article). Set a reminder: snooze and completion. [slack.zendesk.com](https://slack.zendesk.com/hc/en-us/articles/208423427)
30. Microsoft. Add due dates and reminders in Microsoft To Do. [support.microsoft.com](https://support.microsoft.com/en-us/todo/add-due-dates-and-reminders-in-microsoft-to-do)
31. Microsoft Tech Community. Default "Remind me" options (forum). [techcommunity.microsoft.com](https://techcommunity.microsoft.com/discussions/to-do/default-remind-me-options/3057588)
32. Droid Life. Change default Gmail snooze times in Google Keep. 2020. [droid-life.com](https://www.droid-life.com/2020/09/09/random-tip-change-default-gmail-snooze-times-in-google-keep/)
33. 9to5Google. New Gmail web: Nudge AI reminders. 2018. [9to5google.com](https://9to5google.com/2018/05/14/new-gmail-web-nudge-ai-reminders/); Google. Gmail AI features. [blog.google](https://blog.google/products-and-platforms/products/gmail/gmail-ai-features/)
34. Superhuman. Remind Me (help center). [help.superhuman.com](https://help.superhuman.com/hc/en-us/articles/43822934607251-Remind-Me) (content via search snippet; page refused direct fetch)
35. Superhuman. Reminders on Autopilot; Auto Reminders & Auto Drafts (help center). [Reminders on Autopilot](https://help.superhuman.com/hc/en-us/articles/45270478397203-Reminders-on-Autopilot); [Auto Reminders](https://help.superhuman.com/hc/en-us/articles/40144492186515) (content via search snippets)
36. OpenAI. Tasks in ChatGPT (help center). [help.openai.com](https://help.openai.com/en/articles/10291617-tasks-in-chatgpt) (content via search snippet; page refused direct fetch)
37. Anthropic. Claude Code: Run prompts on a schedule (`/loop`, `CronCreate`). [code.claude.com](https://code.claude.com/docs/en/scheduled-tasks)
38. Anthropic. Claude Code: Automate work with scheduled tasks (formerly routines). [code.claude.com](https://code.claude.com/docs/en/routines)
39. Apple Developer. UNNotificationInterruptionLevel (passive, active, timeSensitive, critical). [developer.apple.com](https://developer.apple.com/documentation/usernotifications/unnotificationinterruptionlevel)
40. Android Developers. Create and manage notification channels. [developer.android.com](https://developer.android.com/develop/ui/views/notifications/channels)
41. ntfy. Publishing: message priority, scheduled delivery, actions. [docs.ntfy.sh](https://docs.ntfy.sh/publish/)
42. Pushover. Message API: priorities, quiet hours, emergency retry/expire/receipts. [pushover.net/api](https://pushover.net/api)
43. Desruisseaux B (ed.). RFC 5545: Internet Calendaring and Scheduling Core Object Specification (iCalendar). 2009. [rfc-editor.org](https://www.rfc-editor.org/rfc/rfc5545)
44. Daboo C, Murchison K. RFC 9074: VALARM Extensions for iCalendar. 2021. [rfc-editor.org](https://www.rfc-editor.org/rfc/rfc9074.html)
45. Jenkins N, Stepanek R. RFC 8984: JSCalendar: A JSON Representation of Calendar Data. 2021. [rfc-editor.org](https://www.rfc-editor.org/rfc/rfc8984.html)
46. dateparser (scrapinghub). [github.com/scrapinghub/dateparser](https://github.com/scrapinghub/dateparser); [PyPI](https://pypi.org/project/dateparser/)
47. parsedatetime. [github.com/bear/parsedatetime](https://github.com/bear/parsedatetime); [PyPI](https://pypi.org/project/parsedatetime/)
48. ctparse (comtravo; archived). [github.com/comtravo/ctparse](https://github.com/comtravo/ctparse)
49. Duckling. [github.com/facebook/duckling](https://github.com/facebook/duckling) (now under `meta/duckling`)
50. python-duckling (wrapper). [github.com/FraBle/python-duckling](https://github.com/FraBle/python-duckling)
51. timefhuman. [github.com/alvinwan/timefhuman](https://github.com/alvinwan/timefhuman)
52. dateutil. [github.com/dateutil/dateutil](https://github.com/dateutil/dateutil); [release notes 3.0.0 and 4.0.0](https://github.com/dateutil/dateutil/releases)
53. Microsoft Recognizers-Text. [github.com/microsoft/Recognizers-Text](https://github.com/microsoft/Recognizers-Text)
54. chrono (JavaScript). [github.com/wanasit/chrono](https://github.com/wanasit/chrono)
55. rasa/duckling Docker image. [hub.docker.com](https://hub.docker.com/r/rasa/duckling)
56. Apple. launchd.plist(5) man page (StartCalendarInterval and sleep). [opensource.apple.com](https://opensource.apple.com/source/launchd/launchd-258.1/launchd/src/launchd.plist.5.auto.html)
57. Apple Developer Forums. launchd StartInterval changed behavior in 10.11? [developer.apple.com/forums](https://developer.apple.com/forums/thread/23361)
58. APScheduler 3.x user guide (misfire_grace_time, coalesce, max_instances, job stores). [apscheduler.readthedocs.io](https://apscheduler.readthedocs.io/en/3.x/userguide.html)
59. van der Sijs H, Aarts J, Vulto A, Berg M. Overriding of drug safety alerts in computerized physician order entry. JAMIA. 2006. [PMC1447540](https://pmc.ncbi.nlm.nih.gov/articles/PMC1447540)
60. crowsnest: `later` presets and attention settings (`crowsnest/attention.py`, `crowsnest/config.py`). [github.com/thorwhalen/crowsnest](https://github.com/thorwhalen/crowsnest)
61. openloops: obligations, `manual-task` issues with shell predicates, `ol owed`. [github.com/thorwhalen/openloops](https://github.com/thorwhalen/openloops)
62. correspond: channel facade (GitHub, Gmail, Slack, Telegram, Signal, ntfy, Apprise, macOS). [github.com/thorwhalen/correspond](https://github.com/thorwhalen/correspond)
63. annals: phone-first in-tray for published documents. [github.com/thorwhalen/annals](https://github.com/thorwhalen/annals)
