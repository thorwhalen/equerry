# Executive briefs and attention triage from many sources

Research report, deep tier, 2026-11-09. Design question only: no private material was used. Web searching hit the session's shared search budget partway through, so a few product claims rest on one secondary source; each is marked where it occurs.

## Summary and recommendation

**The question.** How to produce, on a cadence (daily, or per weekday, for example every day except Sunday at 06:00 local time), a "what needs me" brief drawn from coding-session activity, GitHub, Slack, email and chat exports, for a principal who reads on a phone and sometimes listens instead.

**Verdict in one paragraph.** The product record of 2025-2026 is unusually clear: standalone "here is your day" feeds were tried and pulled back, and what survived is a brief the user schedules and steers, that lists actions rather than news, and that explains why each item is there. OpenAI retired ChatGPT Pulse nine months after launch and moved its daily updates into user-scheduled tasks, saying proactive features work best when "personalized, action-oriented, and under user control" [2][3]; Google paused the Pixel 10 Daily Hub weeks after launch and relaunched the idea as an opt-in Gemini Daily Brief that ranks by the user's goals and suggests next steps [6][8][9]; Microsoft's Copilot inbox prioritisation attaches a written reason to every high-priority mail [20][21]. The research says the same thing from the other side: people override 49-96% of clinical alerts [28], each extra reminder per encounter cuts acceptance by about 30% and repeats cut it further [29], attention to a repeated warning decays within days [30], and a filter that is right less than about 70% of the time is worse than no filter [31]. So the brief should be **short, ranked, capped, deduplicated, verified at render time, explained per item, and measured by how often the principal says "this was not for me" and "you should have told me"**.

**The five strongest recommendations.**

1. **Separate "needs you" from "for your awareness", and cap only the first.** The "needs you" section holds at most 7 items (an estimate borrowed from the working-memory cap used in openloops' earlier design work), each in one of six named classes (decide, unblock, review, reply, deadline, risk); everything else is counted, not listed, behind one link. Slack's own recap is explicitly an awareness product for channels you read but do not post in [13]; it is not what the principal needs at 06:00.
2. **Rank with a cost-of-delay score, not a "how important does this sound" score.** Horvitz's expected cost of delayed review [39] multiplied by ownership (only you can do it), reach (how many sessions or people it unblocks, as openloops already proposes) and a confidence discount; irreversibility raises an item's floor, following the one-way/two-way door distinction [40]. A model guessing "importance" from text rewards spurious urgency, which people already over-weight [37].
3. **Keep two watermarks, not one.** An ingest cursor per source (what has been read) and a shown-ledger per item revision (what the principal has already seen, deferred or handled). crowsnest already pins seen/done to a hash of "what you would have to decide again" [54]; correspond already stores per-source cursors with at-least-once delivery and dedup on delivery id [56]. The brief covers "since the last brief that was delivered", not "the last 24 hours", which makes a Sunday skip correct by construction.
4. **Re-verify every "needs you" item at compose time and say "unknown" when you cannot.** openloops' rule that a snapshot never claims a live fact, and that `?` is never rendered as zero [55], is the single property most likely to keep the brief trusted. A brief composed at 06:00 and read at 08:30 should carry its time and a "check again" link.
5. **Instrument the two error types from day one.** Every item gets one-tap feedback: done, later, seen, and **not for me** (a false positive). A free-text **"you missed X"** (a near miss, a false negative [38]) is the scarcest and most valuable label. Precision of "needs you" and the near-miss rate are the headline metrics; target needs-you precision of at least 0.7 as a floor (from [31]; an estimate for this domain, not a measured threshold).

**Biggest risks.** Summary hallucination turning a neutral thread into a false alarm (Apple paused news notification summaries after they invented headlines [23]); stale items shown as live; the same thread appearing three times through GitHub, Slack and email; a brief that is the same shape every day and stops being read (habituation [30]); prompt injection from email and chat content into the composer; and scheduling bugs at daylight-saving changes and on sleeping laptops [48][49][51].

## What already exists in the fleet (cited, not repeated)

- **The owner's existing overview pipeline** (private; not named here) already does `sources → extract → compose → render`: a ref resolves to a `Source` with a kind, a per-kind extractor pulls text into a `Material` (cleaned, clipped at about 100k tokens, stamped with `read_at`), one LLM call composes a `Script` of speaker `Segment`s at a word budget of 150 words per minute, and a renderer produces audio with per-sentence timing cues and a player page. Section 7 maps it onto the brief pipeline.
- **openloops** [55] answers "what do I owe" with re-checked predicates, `ol owed`, `ol blocked`, a dashboard that stamps itself "snapshot" and renders unknowns as `?`, and a phone board whose taps become GitHub actions through a session. Its earlier design study (R6, "the observatory", unpublished) proposed an ordering score `reach × urgency × confidence` with saturating age, a default top-7 view, and the warning that a falsely clean slate is the failure that destroys trust. This report extends that score rather than replacing it.
- **crowsnest** [54] already has the attention verbs `seen`, `later`, `done`, `note`, pinned to a revision hash of the ask, with `later` waking on a time or on change, and a phone page.
- **correspond** [56] already has per-source cursors (`listen`), at-least-once delivery with dedup on `delivery_id`, and push receivers for GitHub, Slack and Telegram.
- **annals** is the in-tray where a brief can be published with a Listen button; **acquaint** holds the principal's preference model, which can supply the "goals" Gemini's brief ranks by [6].

## 1. How products do it today, and what users say

| Product | What it does | What worked | What failed or was criticised |
|---|---|---|---|
| ChatGPT Pulse (Sept 2025 – June 2026) | Overnight research over chats, memory, feedback and optionally Gmail/Calendar; a set of visual cards each morning; thumbs up/down; a "Curate" box to steer topics [1] | A columnist found it brought back "things that were important to me a few days earlier but had fallen off my radar" [5] | Retired on 17 June 2026 with 14 days' notice, replaced by user-defined scheduled tasks with a Scheduled page listing next run times [2][3]; one reviewer called it "unsolicited recommendations packaged as a premium feature" [4], and a subscriber complained it kept resurfacing questions resolved weeks earlier (user comment quoted in search results; source not verified) |
| Google CC → Gemini Daily Brief | CC (Labs, Dec 2025) emailed "Your Day Ahead" from Gmail, Calendar, Drive; you replied to the email to steer it [7]. Daily Brief (I/O, 19 May 2026) is opt-in, pulls urgent mail, events and follow-ups into a "skimmable briefing", "organizes and prioritizes based on your specific goals", suggests next steps, thumbs feedback [6] | Email as the delivery and the feedback channel; goal-based ranking | The Pixel 10 "Daily Hub", its predecessor, was paused weeks after launch; Google said it would return once performance and personalisation improved [8][9]; reviewers reported it was unreliable and often did not appear [10] |
| Superhuman | Split Inbox by importance; one-line Auto Summarize above every thread, updated as mail arrives [11] | Summary at the point of decision, not in a separate digest | Mostly marketing sources; no independent evaluation found |
| Shortwave | Bundles, delivery schedules (mail held to set times), instant TL;DR on threads [12] | Delivery schedules are batching, which has evidence behind it [33] | Gmail-only (third-party report) |
| Slack AI Recaps | Daily summary, each morning, of channels you pick; first suggestion is "channels you visit often but rarely post in"; citations to source messages [13] | Awareness without interruption; cited sources | Business+ and above only; sees only Slack; one fixed format; no actions [14] |
| GitHub notifications | No general scheduled digest (a third-party claim; only Dependabot offers a native daily/weekly email digest). The notifications API carries a `reason` per thread, including `review_requested`, `mention`, `assign`, `approval_requested`, `ci_activity`, `security_alert` and `agent_session_finished`, plus a separate *done* state [15] | `reason` is a ready-made, deterministic "why am I seeing this" | Reasons are sticky (once `mention`, always `mention`) [15], so they say why you were subscribed, not whether you still owe anything |
| Microsoft Copilot "Prioritize my inbox" | High/normal/low per mail with a written reason shown at the top of the message; user-written criteria [20] | A reason per item; user can state rules in prose | The ranking is opaque and the user's instructions are the only control [21] |
| Gmail Priority Inbox (2010, the canonical ML case) | Per-user logistic model on top of a global one; predicts P(act on mail); per-user threshold nudged when the user marks mail consistently [22] | Users spent 6% less time on mail and 13% less on unimportant mail; about 80% accuracy on implicit labels [22] | Users disagree about how much should be "important", and their behaviour does not reveal it, so a manual threshold control was needed [22] |
| Notion Mail | AI "Views" as smart folders, auto-labels from a prompt, thread summary under the subject [16] | Prompt-defined categories | No daily brief |
| Fyxer | Sorts mail on arrival, surfaces "the emails that need you today", drafts replies in your tone [17] | Drafts waiting next to the item that needs a reply | Marketing source only |
| Motion / Reclaim | Plan the day by moving tasks into calendar time [18] | Reclaim reviewers like automatic rescheduling [19] | Rescheduling "can feel aggressive" and "opaque when a task moves unexpectedly"; heavy setup [18][19] |
| Read AI, Lindy | Daily email digest at a user-set time; templated morning briefs to SMS, email or Slack [26][27] | Delivery time is the user's | Template products; no evaluation found |
| Huxe (audio daily briefing from Gmail and Calendar, by former NotebookLM leads) | A short spoken briefing of schedule and important mail [24] | Audio-first | Reported shut down in May 2026 with its technology licensed to Apple; single secondary source, unverified [25] |
| Apple Intelligence notification summaries | Summaries of stacks of notifications | — | News summaries paused in January 2025 after inventing false headlines; summaries elsewhere shown in italics to mark them as machine-written [23] |

**Patterns.** (a) Every surviving product gives the user the schedule and the steering (OpenAI's stated lesson [3]; Read AI's delivery time [26]; Gemini's opt-in [6]). (b) The products praised for usefulness attach a reason or a source to each item (Copilot's reason [20], Slack's citations [13], GitHub's `reason` [15]). (c) "Awareness" digests (Slack recaps) and "needs you" triage (Priority Inbox, Copilot, Fyxer) are separate products, and conflating them is what reviewers of Pulse complained about. (d) Nothing on the market spans coding sessions, GitHub, Slack, mail and chat exports together; the Lindy/Read AI pattern is per-source digests. A cross-source brief is not a commodity to buy.

## 2. The research on triage, alert fatigue and habituation

**Override rates are high and are driven by repetition and count, not raw workload.** A review of 17 studies found clinicians override drug safety alerts in 49% to 96% of cases [28]. In 112 primary-care clinicians over three and a half years, acceptance fell about 30% for each additional reminder in an encounter and about 10% for each five-point rise in the share of repeated reminders; workload itself and time since deployment had no detectable effect [29]. The design lesson for a brief: the number of items in one brief and the share that the principal has seen before are the two levers, and both are under the system's control.

**Habituation is fast and partly recovers.** fMRI and eye tracking over a five-day week showed attention to a repeated security warning dropping, partly recovering after a day without exposure; warnings whose appearance changed each time (polymorphic) resisted habituation, and a three-week field test confirmed it [30]. For a brief: an item that reappears unchanged should be visibly marked as a repeat (and demoted), and a changed item should look changed. crowsnest's revision-pinned `seen` already implements the "changed" half [54].

**Unreliable triage is worse than none below roughly 70%.** A meta-analysis of imperfect diagnostic automation found a crossover near 0.70 reliability, below which people did worse with the aid than without it, and the effect of reliability was larger under high workload [31]. That makes "needs you" precision a go/no-go metric, not a nice-to-have.

**Every page should be actionable, need judgment, and be new.** Google's SRE guidance limits pages to conditions that are urgent, actionable and novel, says a page that merits "a robotic response" should not be a page, and calls e-mail alerts "alert spam" that is rarely read [32]. Mapped to the brief: an item the system could have done itself is a defect in the system, not an item; an item with no action is awareness, not "needs you".

**Batch the non-urgent.** In a randomised field experiment with 237 people, notifications delivered three times a day (9:00, 15:00, 21:00) improved well-being, attention and sense of control compared with as-usual delivery; hourly batches did little, and no notifications raised anxiety and fear of missing out [33]. In a two-week Outlook study, turning notifications off left time in the mail client unchanged, users treated notifications as awareness rather than as a cue to switch, and some found the catch-up burden "counterproductive" [34]. A daily brief is a batch; it should not be the only channel for the genuinely urgent (that belongs to the reminders/escalation design in the sibling report), and silence has a cost of its own.

**Volume and receptivity.** Smartphone users received about 63.5 notifications a day in an in-situ study [35]; receptivity depends mostly on the content type and the sender [36]. For a model-based ranker, "who is asking" (a person, an agent session, a bot) is a first-class feature, as it is in Priority Inbox's social features [22].

**Urgency is over-weighted.** People choose spuriously urgent, lower-payoff tasks over important ones, violating dominance, in five experiments [37]. A model that ranks by how urgent a message *sounds* will reproduce this bias; rank by modelled cost of delay instead [39].

**What fraction to surface.** No study gives a universal fraction. Priority Inbox deliberately set thresholds so that false negatives ran 3-4 times false positives, and still needed a user control because preferred volume varies hugely between people [22]. The evidence supports a per-principal cap tuned by feedback rather than a fixed share. A starting point (estimate): at most 7 "needs you" items, at most 5 "changed since last brief" lines, and everything else as counts.

**Near-miss learning.** In patient safety a near miss is an event that did no harm only through chance or early detection; near misses are information-rich and systemic problems can be found "with far less than 100% reporting" [38]. The brief's near miss is the item that should have been in it and was not, found later by the principal or by an agent. Logging these is the only way to measure recall, because a filtered-out item leaves no trace by default.

## 3. Briefing formats

**BLUF (bottom line up front).** Military correspondence guidance puts the conclusion, recommendation or request first [41]. For a brief, the first line is the count and the single most consequential item: "3 need you; first: approve the release of X (blocks 2 sessions)".

**Management by exception.** Report only material deviations from plan, with thresholds set in advance [43]; its known failure is a preoccupation with unfavourable variances and blindness to what is quietly going well [43]. The brief's answer is a one-line "unblocked since last brief" register (openloops' "free to proceed" [55]).

**One-page and intelligence-style daily briefs.** The US President's Daily Brief moved to a tablet in 2014, and its own analysts wrote about rethinking its structure for that medium [42]; reported lengths have varied from a page or two to dozens by reader (secondary reports; not verified). The transferable principle is that the format is tailored to one reader and changes with them, which fits a per-principal schema with caps as settings rather than constants.

**The resulting order for a phone.** BLUF line → needs you (ranked, capped) → changed since you last looked → unblocked/free to proceed → unknown (could not check, with why) → counts of everything else, each count a link. This is openloops' register order [55] with a BLUF line and a "changed" register added.

## 4. A prioritisation model a language model can apply

**"Needs you" classes.** A deterministic class first, a model judgement second. The classes are derived from GitHub's notification reasons [15], openloops' obligation kinds [55] and crowsnest's `[decision]`/`[action]` tags [54]:

| Class | Meaning | Typical deterministic signal |
|---|---|---|
| `decide` | A choice only the principal may make | Session ask tagged decision; `manual-task` issue without predicate |
| `unblock` | A secret, access, purchase or command only the principal can supply | `manual-task` with a predicate; a blocked session's ask |
| `review` | Approve or review a change | `review_requested`, `approval_requested` [15] |
| `reply` | A person is waiting on an answer | Direct mail or DM addressed to the principal with a question; `mention` |
| `deadline` | A date that passes without action | Calendar event, due date in text, expiring credential |
| `risk` | Something is breaking or about to | `security_alert`, red CI on the default branch, failing scheduled job |

Anything that fits none of these is awareness, whatever its tone.

**Score.** For each item in a "needs you" class:

`priority = cost_of_delay(class, age, deadline) × ownership × reach × confidence`, then `+ floor` for irreversible items.

- `cost_of_delay` follows Horvitz's expected cost of delayed review, a per-class loss rate times delay that rises steeply before deadlines [39]; reuse openloops' saturating age term so one ancient item cannot own the top slot.
- `ownership` is 1.0 when only the principal can act, about 0.5 when the principal is the responsible person but could delegate, and about 0.1 when copied (estimates; tune from feedback).
- `reach` counts blocked sessions, people and pull requests, as in openloops' earlier design.
- `confidence` discounts unverified and stale items (verified now 1.0; unverified older than a day lower).
- Irreversible or hard-to-reverse items ("one-way doors" [40]: a release, a payment, a message to a client) get a floor that keeps them in the brief even when their score is low. This mirrors the reversibility gate in the phase-1 principal-model report.

**Where the model fits.** The model classifies items whose class the rules could not determine, writes the one-line "why you" from the source text, and proposes a next action; it does not set the score directly. That keeps the score auditable, which openloops' design already requires, and keeps "sounds urgent" out of the ranking [37].

## 5. Mechanics

### 5.1 Source adapters and incremental reads

| Source | Incremental mechanism | Item identity | "Needs you" signal |
|---|---|---|---|
| Coding sessions | crowsnest roster and ledgers; openloops digests (transcript-derived) [54][55] | session id + ask revision hash | `needs_you` verdict, `asks` |
| Obligations | `ol owed`, `ol blocked` with re-checked predicates [55] | issue URL | `manual-task` label, predicate state |
| GitHub | Notifications API polled with `If-Modified-Since` (a 304 is free), `X-Poll-Interval`, `since` [15]; correspond `listen github:` [56] | thread id → canonical issue/PR URL | `reason` [15] |
| Slack | Events API or Socket Mode via correspond [56]; `conversations.history` with `oldest` | channel id + `ts` (unique only within a channel) [44] | DM, mention, thread reply to the principal |
| Email | Gmail `history.list` from a stored `historyId`; a 404 means the id is too old and a full sync is required [45] | `Message-ID`, thread via `In-Reply-To`/`References` [46] | addressed to the principal, contains a question, from a known person |
| Chat exports (WhatsApp etc.) | file import with a per-conversation watermark (acquaint's digest skill already keeps one) | conversation + message time + hash | open threads in the digest |
| Calendar | events in the brief window | event id | today's events needing preparation |

### 5.2 Watermarks: two kinds, never conflated

- **Ingest cursor**, per source: the position read up to (a `historyId`, a Slack `ts`, a `Last-Modified`). Advance it only after the items are durably stored (correspond's rule: store the cursor after the hand-over, deliver at least once, dedup on delivery id [56]). A source whose cursor has expired (Gmail's 404 [45]) must say so in the brief's Unknown register rather than silently restarting.
- **Shown-ledger**, per item revision: whether the principal has seen, deferred, done or rejected this item **at this revision**. The revision is a hash of what the principal would have to decide again, not a timestamp (crowsnest's design [54]). An item comes back only when its revision changes or its `later` wakes.
- **Brief window**: from the delivery time of the last brief actually delivered to now. A skipped Sunday makes Monday's window Saturday 06:00 → Monday 06:00 with no special case, and a failed run makes the next one larger rather than losing a day.

### 5.3 Deduplication across sources

The same piece of work commonly arrives as a GitHub notification, a Slack unfurl of the PR link, an email from GitHub, and a session's ledger line. Dedup in three passes:

1. **Exact identity**: canonicalise URLs (strip tracking parameters, fragments, `/files` suffixes; map `api.github.com` thread URLs to `github.com` issue/PR URLs), and treat GitHub notification mail as the same thread as the API notification.
2. **Reference linking**: a Slack message or email that contains a canonical URL is attached to that URL's cluster as evidence, not shown as its own item.
3. **Near-duplicate clustering** by a model or embeddings for items with no shared URL, only within a time window, and shown as one item with "also in Slack, also by mail".

PagerDuty's experience gives two cautions: dedup only against *open* items (a resolved item that recurs is news) and a key that is too general silently swallows new, different problems [47]. Keep the cluster key specific and show the cluster's members on tap.

### 5.4 Ranking and the cap

Rank within each section by the score in section 4; break ties by age then by repository so the order is stable between briefs (openloops' rule). Show at most the cap; render the rest as "and N more" linking to the full list. Never render an unknown count as zero; an empty "needs you" says which checks earned it [55].

### 5.5 The feedback loop

Per item, one tap each: **done** (handled), **later** (with presets: this evening, tomorrow, on change), **seen** (read, nothing to do), **not for me** (false positive, with optional reason: "not mine", "already handled", "not important"). Per brief, a free-text **"you missed"** box (near miss). Map the first three onto crowsnest's verbs so one ledger serves both surfaces [54]. Use "not for me" and "you missed" to move a per-principal threshold, as Priority Inbox moved its threshold when a user marked mail in a consistent direction [22], and to update the preference model in acquaint; never let a single tap delete an obligation (openloops' "never auto-close on ambiguous evidence").

### 5.6 Rendering for a phone and for text-to-speech

- **Phone**: one screen before the first scroll holds the BLUF line and the top three "needs you" items; each item is one line (verb first, object, why-you in under 15 words), a source chip, an age, and one primary action link (open the PR, the board tap, the reply draft). Machine-written text is marked as such (Apple's italics convention [23]); the principal's or the requester's own words are quoted verbatim, not paraphrased.
- **Speech**: the existing overview pipeline budgets 150 words per minute; a 2-minute spoken brief is about 300 words (estimate: 5-7 items). No URLs, no hashes, no repository slugs read aloud; say counts first ("three things need you"), give each item a spoken ordinal, name the source in words, and end with an explicit closing line so the listener knows nothing was cut. The same composed item list renders to both, so phone and audio cannot disagree.

### 5.7 Scheduling with local time zones and per-weekday cadences

- **Store the schedule as an IANA time zone plus a recurrence rule**, for example `TZ=Europe/Paris; RRULE:FREQ=WEEKLY;BYDAY=MO,TU,WE,TH,FR,SA;BYHOUR=6;BYMINUTE=0`, and compute the next fire time in that zone. RFC 5545 resolves a local time that occurs twice to its first occurrence and reads a nonexistent local time with the offset before the gap [48]. A 06:00 brief is outside the usual 02:00-03:00 transition hour, where cron implementations differ (Debian's cron runs a skipped job once and avoids a double run; others skip or double-run) [51].
- **On the Mac, launchd's `StartCalendarInterval` uses local time and runs a job missed during sleep when the machine wakes** [49]; a job due while the machine was powered off is not run at boot (forum report, unverified), and recent macOS versions may fire during a dark wake (anecdotal) [50]. The run must therefore be idempotent per `(brief_id, scheduled_date)`: a wake-up catch-up and a manual run on the same morning deliver one brief.
- **Claude Code cloud scheduled tasks** (formerly routines) run without the laptop, accept presets in local time but read a bare five-field cron expression as **UTC** unless it starts with `CRON_TZ=`, reject intervals under an hour, and start runs on the hour several minutes late; the docs advise scheduling a few minutes past the hour [53]. They clone repositories fresh and cannot read local files [52], so they cannot see local transcripts. **Session-scoped** `CronCreate` tasks use local time but expire after seven days and only fire while the session is running and idle [52]; they are unsuitable for a standing brief.
- **Recommendation**: compose on the machine that holds the transcripts (a launchd or systemd timer at, say, 05:50 local), deliver at 06:00, and record `generated_at` and `delivered_at` in the brief so staleness is visible. Keep the cadence (which weekdays, what time, which zone, quiet days) as data the principal edits, not as a crontab line.

## 6. Recommended brief schema

```yaml
Brief:
  brief_id: "2026-11-10.morning"        # idempotency key: one per scheduled slot
  principal: <id>
  window: {since: <last delivered_at>, until: <generated_at>}
  generated_at, delivered_at, tz          # shown on the page, spoken as "as of 6 a.m."
  bluf: "3 need you. First: approve the release of X (blocks 2 sessions)."
  sections:
    needs_you:      {cap: 7, items: [Item]}   # ranked by score, classes in §4
    changed:        {cap: 5, items: [Item]}   # items you saw before whose revision changed
    unblocked:      {cap: 5, items: [Item]}   # free to proceed since last brief
    unknown:        {cap: none, items: [Check]}  # could not check, with why; never folded into zero
    counts:         {by_source: {...}, by_class: {...}, link: <full list>}
  sources_checked: [{source, cursor_before, cursor_after, ok, error}]
  feedback_link, listen_link

Item:
  item_id            # stable cluster id (canonical URL or hash)
  revision           # hash of what the principal would have to decide again
  class              # decide | unblock | review | reply | deadline | risk | awareness
  title              # verb-first, <= 12 words
  why_you            # <= 15 words; rule-derived if possible, else model-written and marked
  ask_verbatim       # the requester's own words, clipped, with its own timestamp
  requester          # person | agent session | bot, with name
  sources: [{kind, url, said_at}]   # all members of the dedup cluster
  next_action        # {label, url | board_tap | draft_id}
  score: {cost_of_delay, ownership, reach, confidence, irreversible, total}  # auditable
  first_seen_at, age, deadline
  verified: {at, by, state: yes | no | unknown}
  shown_before: {count, last_state: seen | later | none}
```

Caps are per-principal settings with these defaults (estimates), and a quiet day sends a one-line brief ("nothing needs you; 4 sources checked") rather than nothing, so silence is never ambiguous.

## 7. The pipeline, and what the existing overview pipeline already covers

`ingest → normalise → dedup → verify → rank → compose → render → deliver → feedback`

| Stage | What it must do | Existing overview pipeline | Other fleet pieces |
|---|---|---|---|
| ingest | read each source since its cursor | **Partial**: `gather` resolves refs and runs a per-kind extractor, with cleaning and clipping; no cursors, refs are named by hand | correspond cursors and receivers [56]; crowsnest, openloops readers [54][55] |
| normalise | turn raw material into `Item`s with identity, class, requester, timestamps | **None**: `Material` is a source plus text plus `read_at`; there is no item inside it | openloops and crowsnest already emit item-shaped rows |
| dedup | cluster across sources | None | — |
| verify | re-check live state at compose time | None | `ol owed`/`ol blocked` predicates [55] |
| rank + cap | score, order, cut | None | openloops' score design; this report's §4 |
| compose | BLUF line, sections, one-line items, spoken script | **Yes, for prose**: one LLM call writes a titled `Script` within a word budget, with citations | — |
| render | phone page and audio | **Yes, for audio**: timed audio, per-sentence cues, a player page; `Script.as_markdown` for text | annals page with Listen [context] |
| deliver | publish at the scheduled time | None | annals publish; launchd jobs |
| feedback | done / later / seen / not for me / you missed | None (a follow-up conversation loop exists in the voice product around it) | crowsnest verbs [54]; openloops board taps [55] |

**The fit.** The existing pipeline is the right back half (compose and render) and a reasonable ingest shell. What it lacks is the item layer: insert an `Item` type between `Material` and `Script`, make `compose` take a ranked, capped `Brief` rather than raw materials, and keep the `Script` as one rendering of the brief beside the phone page. The front half (cursors, normalise, dedup, verify, rank) and the feedback loop belong closer to openloops and crowsnest, which already own item identity and attention state.

## 8. Evaluation metrics

| Metric | Definition | How measured | Starting target (estimate) |
|---|---|---|---|
| Needs-you precision | share of "needs you" items not marked "not for me" | taps | ≥ 0.7, from the automation crossover [31] |
| Near-miss rate | "you missed" reports per week, plus items acted on within 24 h that were never shown | free-text box; reconcile actions against briefs | trending down; every one reviewed |
| Override rate | share of shown items the principal reorders, dismisses or defers without action | taps | falling over the first month |
| Action rate | share of "needs you" items done within 24 h | done taps and source state | rising |
| Staleness at read | share of items already resolved when opened | verify at open | < 5% |
| Duplicate rate | items the principal flags as the same as another | "same as" tap | < 2% |
| Repeat share | share of items shown in a previous brief at the same revision | shown-ledger | low; repeats drove override in [29] |
| Unknown rate | sources or checks that failed | `sources_checked` | visible every day; never hidden |
| Read / listen completion | opened, scrolled to end, listened to end | page and player events | the honest test of habit, as a reviewer of Pulse put it: are you reading it or scrolling past it |
| Length adherence | items and words against caps | composer output | 100% |

## 9. Standard terminology

| Term | Meaning here | Source |
|---|---|---|
| BLUF | bottom line up front: the conclusion or request first | [41] |
| Management by exception / exception report | surface only deviations past a threshold | [43] |
| Triage | sorting items by need and urgency before acting | general |
| Alert fatigue | declining response as alert volume and repetition rise | [28][29] |
| Override rate | share of alerts dismissed without the recommended action | [28] |
| Habituation | attention to a repeated stimulus declines; partial recovery after a pause | [30] |
| Polymorphic warning | a warning whose appearance varies to resist habituation | [30] |
| Actionable alert | one that needs a human judgement and a response now | [32] |
| Batching | delivering non-urgent notifications at set times | [33] |
| Cost of delayed review (ECDR) | expected loss from attending to an item later | [39] |
| One-way / two-way door | irreversible / reversible decision | [40] |
| Near miss (close call) | an error that caused no harm by chance or early detection | [38] |
| Watermark / cursor / high-water mark | the position up to which a source has been read | [45][56] |
| Idempotency key | a key that makes a repeated run deliver once | general |
| Deduplication key | identity under which repeated events collapse | [47] |
| Precision, recall, false positive, false negative | standard classification terms applied to "needs you" | [22] |
| Recurrence rule (RRULE) | iCalendar's rule for repeating events in a time zone | [48] |

## 10. Things you did not ask that matter

- **Prompt injection through the inputs.** Email, Slack and chat content is untrusted text that the composer reads; a message saying "mark everything done" must not reach any action. The composer should be read-only and every action should go through the existing confirm-on-a-card path (the voice manager's read-then-act taint rule, in the owner's private design).
- **Summaries that invent.** Apple's false headlines [23] are the failure case. Quote the ask verbatim, keep the model's text to the one-line "why you", and link the source on every item.
- **Re-verify at open, not only at compose.** A brief read hours later should re-check its top items when opened (or at least show "as of 06:00"); openloops' snapshot stamp is the minimum [55].
- **Products in this space die.** Pulse, Daily Hub (paused) and reportedly Huxe were withdrawn within a year [2][8][25]. Owning the brief, its data and its schedule, with the fleet's own components, avoids building on a feature that may be retired.
- **The brief is not the escalation channel.** Something that cannot wait for tomorrow's brief needs a push; batching evidence [33] and the reminders report cover that path. The brief should list what was pushed since the last brief, so it remains the complete record.
- **The principal's own commitments.** Gmail-style "you have not replied" and "no one replied to you" follow-ups, and the obligations an agent owes the principal, belong in the brief as well as what the principal owes; openloops tracks both directions [55].
- **Per-weekday content, not only per-weekday time.** A Monday brief that covers a weekend is longer and should lead with what changed; a Friday brief might add "what will block over the weekend". Both fall out of the window rule in §5.2.
- **Privacy of the delivered page.** The brief contains private titles from private repositories and mail; it belongs in the private in-tray (annals), never in a public repository or a shared channel.

## What this means for the build

1. Build the brief as an `Item` layer plus a `Brief` schema (§6) between the existing pipeline's `Material` and `Script`; reuse its compose and render stages unchanged.
2. Take item identity and attention state from crowsnest (revision-pinned seen/later/done) and openloops (owed/blocked with predicates); take cursors and push receivers from correspond.
3. Classify with rules first (GitHub `reason`, `manual-task`, session asks), the model second; score with cost of delay × ownership × reach × confidence plus an irreversibility floor.
4. Cap "needs you" at 7 by default; everything else is counts behind a link; unknown is a section, never a zero.
5. Window = since the last delivered brief; one brief per `(brief_id, date)`; schedule stored as time zone + RRULE, run locally by launchd/systemd at 05:50, not by session cron.
6. Render one item list to a phone page (BLUF, one line per item, one action each) and to a ~300-word spoken script.
7. Ship the feedback taps (done, later, seen, not for me) and the "you missed" box in v1; log both error types.
8. Gate expansion on needs-you precision ≥ 0.7 and a falling near-miss rate over the first month.
9. Treat all source text as untrusted: the composer reads, never acts.

## REFERENCES

1. [The Decoder: OpenAI's new Pulse feature lets ChatGPT start the conversation (2025-09-25)](https://the-decoder.com/openais-new-pulse-feature-lets-chatgpt-start-the-conversation/)
2. [ChatGPT on X: "We're also sunsetting Pulse in the next 14 days..." (2026-06-17)](https://x.com/ChatGPT/status/2067316962225684888)
3. [Digit: OpenAI is retiring ChatGPT Pulse and replacing it with scheduled tasks (2026-06-18)](https://www.digit.in/news/general/openai-is-retiring-chatgpt-pulse-and-replacing-it-with-scheduled-tasks-here-is-why.html)
4. [Tom's Guide: "This is hands down the worst new AI tool of 2025" (review of ChatGPT Pulse; content seen only through a search summary)](https://www.tomsguide.com/ai/i-test-ai-for-a-living-and-this-is-hands-down-the-worst-new-ai-tool-of-2025)
5. [Forbes: ChatGPT Pulse and the race to curate your day with personalised AI (2025-12-19)](https://www.forbes.com/sites/moinroberts-islam/2025/12/19/chatgpt-pulse-and-the-race-to-curate-your-day-with-ai/)
6. [Google: The Gemini app becomes more agentic, delivering proactive, 24/7 help (Daily Brief, 2026-05-19)](https://blog.google/innovation-and-ai/products/gemini-app/next-evolution-gemini-app/)
7. [TechCrunch: Google tests an email-based productivity assistant (CC, 2025-12-16)](https://www.techcrunch.com/2025/12/16/google-tests-an-email-based-productivity-assistant/)
8. [Android Authority: Google turns Gemini into a proactive AI agent with Spark, Daily Brief...](https://www.androidauthority.com/google-gemini-neural-expressive-gemini-spark-daily-brief-omni-updates-3668384/)
9. [TechRadar: Google has temporarily pulled the Pixel 10's Daily Hub feature](https://www.techradar.com/phones/google-pixel-phones/google-has-temporarily-pulled-the-pixel-10s-daily-hub-feature-in-an-attempt-to-make-it-more-useful)
10. [Android Central: Pixel 10's Daily Hub is gone for now, but Google says it will be back](https://www.androidcentral.com/phones/google-pixel/google-pause-pixel-10-daily-hub)
11. [Superhuman blog: How email AI will transform your inbox (Auto Summarize)](https://blog.superhuman.com/email-ai-writer/)
12. [Shortwave blog: Everything we shipped for AI launch week](https://shortwave.com/blog/everything-we-shipped-for-ai-launch-week)
13. [Slack Help Center: Guide to AI features in Slack (Recaps, summaries)](https://slack.com/help/articles/25076892548883)
14. [eesel: A practical guide to Slack AI daily recaps and its limitations (2025-11-06; vendor with a stake)](https://www.eesel.ai/blog/slack-ai-daily-recaps)
15. [GitHub Docs: REST API endpoints for notifications (reasons, polling, done)](https://docs.github.com/en/rest/activity/notifications?apiVersion=2022-11-28)
16. [TechCrunch: Notion releases its AI-driven email inbox (2025-04-15)](https://techcrunch.com/2025/04/15/notion-releases-its-ai-driven-email-inbox)
17. [Fyxer: inbox management (vendor page)](https://www.fyxer.com/go/inbox-management)
18. [Morgen: Motion vs Reclaim, 2026 review](https://morgen.so/blog-posts/motion-vs-reclaim)
19. [G2: Reclaim.ai reviews](https://www.g2.com/products/reclaim-ai)
20. [Microsoft Support: Prioritize my inbox (Copilot in Outlook)](https://support.microsoft.com/topic/prioritize-my-inbox-65e37040-2c90-4ee3-86d9-e95d5ba0e3cb)
21. [Practical 365: Prioritize My Inbox advances the state of the email filtering art](https://practical365.com/prioritize-my-inbox)
22. [Aberdeen D, Pacovsky O, Slater A. The learning behind Gmail Priority Inbox. NIPS 2010 workshop LCCC](https://storage.googleapis.com/gweb-research2023-media/pubtools/pdf/36955.pdf)
23. [The Decoder: Apple pauses AI-generated news summaries after serious accuracy issues surface (Jan 2025)](https://the-decoder.com/apple-pauses-ai-generated-news-summaries-after-serious-accuracy-issues-surface/)
24. [TechCrunch: Former NotebookLM devs' new app Huxe taps audio (2025-09-23)](https://techcrunch.com/2025/09/23/former-notebooklm-devs-new-app-huxe-taps-audio-to-help-you-with-news-and-research)
25. [iClarified: Apple strikes AI audio deal with Huxe (2026-10-11; single source for the shutdown)](https://www.iclarified.com/102723/apple-strikes-ai-audio-deal-with-huxe-founded-by-former-notebooklm-developers)
26. [Read AI: Daily email digests give you a head start on your day](https://www.read.ai/post/daily-email-digests-give-you-a-head-start-on-your-day)
27. [Lindy docs: Daily brief](https://docs.lindy.ai/features/meeting-assistant/daily-brief)
28. [van der Sijs H, Aarts J, Vulto A, Berg M. Overriding of drug safety alerts in computerized physician order entry. JAMIA 2006;13(2):138-147](https://pmc.ncbi.nlm.nih.gov/articles/PMC1447540)
29. [Ancker JS et al. Effects of workload, work complexity, and repeated alerts on alert fatigue in a clinical decision support system. BMC Med Inform Decis Mak 2017;17:36 (abstract-level)](https://psnet.ahrq.gov/issue/effects-workload-work-complexity-and-repeated-alerts-alert-fatigue-clinical-decision-support)
30. [Vance A, Jenkins JL, Anderson BB, Bjornn DK, Kirwan CB. Tuning out security warnings: a longitudinal examination of habituation through fMRI, eye tracking, and field experiments. MIS Quarterly 2018;42(2):355-380](https://misq.org/tuning-out-security-warnings-a-longitudinal-examination-of-habituation-through-fmri-eye-tracking-and-field-experiments.html)
31. [Wickens CD, Dixon SR. The benefits of imperfect diagnostic automation: a synthesis of the literature. Theor Issues Ergon Sci 2007;8(3):201-212 (report version)](https://apps.dtic.mil/sti/pdfs/ADA446167.pdf)
32. [Google SRE book: Monitoring distributed systems](https://sre.google/sre-book/monitoring-distributed-systems/)
33. [Fitz N, Kushlev K, Jagannathan R, Lewis T, Paliwal D, Ariely D. Batching smartphone notifications can improve well-being. Comput Human Behav 2019;101:84-94](https://static1.squarespace.com/static/57a40c19414fb54f51f8095f/t/614a55faa7b89e25f4e48ad1/1632261627146/2019+Fitz+Batching.pdf)
34. [Iqbal ST, Horvitz E. Notifications and awareness: a field study of alert usage and preferences. CSCW 2010](https://www.erichorvitz.com/shamsi_iqbal-eric_horvitz_cscw_2010.pdf)
35. [Pielot M, Church K, de Oliveira R. An in-situ study of mobile phone notifications. MobileHCI 2014](https://ic.unicamp.br/~oliveira/doc/MHCI2014_An-in-situ-study-of-mobile-phone-notifications.pdf)
36. [Mehrotra A, Pejovic V, Vermeulen J, Hendley R, Musolesi M. My phone and me: understanding people's receptivity to mobile notifications. CHI 2016](https://discovery.ucl.ac.uk/1502364/1/CHI16.pdf)
37. [Zhu M, Yang Y, Hsee CK. The mere urgency effect. J Consum Res 2018;45(3):673-690](https://academic.oup.com/jcr/article/45/3/673/4925434)
38. [AHRQ PSNet primer: Adverse events, near misses, and errors](https://psnet.ahrq.gov/primer/adverse-events-near-misses-and-errors)
39. [Horvitz E, Jacobs A, Hovel D. Attention-sensitive alerting. UAI 1999](https://arxiv.org/pdf/1301.6707)
40. [Amazon 2015 letter to shareholders (Type 1 and Type 2 decisions)](https://www.sec.gov/Archives/edgar/data/1018724/000119312516530910/d168744dex991.htm)
41. [Wikipedia: BLUF (communication), citing US Army AR 25-50](https://en.wikipedia.org/wiki/BLUF_(communication))
42. [CIA, Studies in Intelligence 57(4), 2013: Rethinking the President's Daily Brief (PDF; text not machine-readable here, cited for its existence and subject)](https://www.cia.gov/resources/csi/static/Rethinking-Presidents-Daily-Brief.pdf)
43. [AccountingTools: Management by exception](https://www.accountingtools.com/articles/what-is-management-by-exception.html)
44. [Slack developer docs: Retrieving messages (ts unique within a conversation)](https://docs.slack.dev/messaging/retrieving-messages)
45. [Google: Synchronizing clients with Gmail (history.list, 404 fallback)](https://developers.google.com/workspace/gmail/api/guides/sync)
46. [RFC 5322 §3.6.4: Identification fields (Message-ID, In-Reply-To, References)](https://datatracker.ietf.org/doc/html/rfc5322#section-3.6.4)
47. [PagerDuty Support: Event management and deduplication](https://support.pagerduty.com/docs/event-management)
48. [RFC 5545: iCalendar (§3.3.5 DATE-TIME, §3.3.10 RECUR)](https://datatracker.ietf.org/doc/html/rfc5545)
49. [Apple Developer: Scheduling timed jobs (launchd StartCalendarInterval and sleep)](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/ScheduledJobs.html)
50. [Apple Developer Forums: launchd StartCalendarInterval behaviour changed (anecdotal, macOS 15/26)](https://developer.apple.com/forums/thread/815034)
51. [Healthchecks.io blog: How Debian cron handles DST transitions](https://blog.healthchecks.io/hp-rewrite/55459acaf5fec7ca23b4160f7908c23e)
52. [Claude Code docs: Run prompts on a schedule (session tasks, jitter, seven-day expiry)](https://code.claude.com/docs/en/scheduled-tasks)
53. [Claude Code docs: Automate work with scheduled tasks (cloud; UTC cron unless CRON_TZ)](https://code.claude.com/docs/en/routines)
54. [crowsnest README (attention verbs, revision-pinned seen/done)](https://github.com/thorwhalen/crowsnest)
55. [openloops README (owed, blocked, dashboard, board, unknown as `?`)](https://github.com/thorwhalen/openloops)
56. [correspond README (listen cursors, at-least-once delivery, receivers)](https://github.com/thorwhalen/correspond)
