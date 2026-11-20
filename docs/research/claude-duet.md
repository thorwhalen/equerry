# Claude Duet and an organisation layer over many agents

Research report, 2026-11-20. It covers what Anthropic's Claude Duet is, which parts of it overlap with equerry and the packages it builds on (crowsnest, openloops, hatchway), how to integrate the two, and every surface through which Duet can be shaped. It is a design question only and uses no private material.

## Summary and recommendation

Claude Duet is Anthropic's proactive agent app. It was announced on 2026-10-21 as a waitlist research preview for US Pro, Max and Ultra accounts and is not available on Team or Enterprise [1][2][3]. It runs on Opus 5.5 in a cloud VM with its own browser. It checks the user's connected apps every few hours, sends a morning brief, drafts and prepares on its own, and asks before it sends, pays, signs or deletes [1][2][4]. It runs on one Claude account and inherits that account's connectors, skills and memory [2]. It has no API, SDK, webhook, hook, mod or export, and the model cannot be chosen [2][5].

**Verdict: depend on Duet behind a seam, as with Anthropic's other agent primitives [6]: adopt it for the sources the organisation layer never connected, keep the layer for the fleet, and join the two through the MCP connector the layer already exposes.**

1. **Duet covers what the brief lacks.** It reads mail, calendar and chat, follows up on threads that have gone quiet, and drafts and schedules on the user's behalf. Anthropic maintains it. An organisation layer that has not yet ingested those sources ([briefs-and-attention-triage.md](briefs-and-attention-triage.md) lists them under "Not connected") should not now build that ingestion.
2. **The layer stays the system of record for the fleet.** Duet sees one account. It cannot be read from outside, and its memory cannot be inspected or exported. It ranks by model judgement and says it may "miscalibrate what's urgent" [1]. Sessions across machines and accounts, obligations (openloops), ledgers (crowsnest), the consent table and rule-based ranking stay where they are.
3. **The seam is the connector, and it already exists.** Connectors belong to the account and are shared between Claude's apps ("switching an app off in one place switches it off in both" [4]). A hatchway profile served as a claude.ai custom connector reaches Duet with no new code, provided custom connectors carry over to Duet, which is not yet documented [7]. Do not build a Duet-specific profile or server.
4. **Assume every connector call is unattended.** Duet runs routines with nobody watching after reading untrusted mail [4]. Write tools on the connector need the principal's own confirmation outside the model (a passkey tap, say), or must stay off or set to "needs approval". A call from a chat app no longer implies that the principal is present.
5. **Integration runs one way, with one return path.** Duet reads the fleet through the connector. The fleet cannot read Duet. Anything Duet learns that the fleet must act on comes back through a write tool the layer offers, such as a handoff note or an issue, behind that confirmation.
6. **Meter it.** Duet shares the plan's five-hour and weekly limits with coding, and its background checks count too [2].

## 1. What Duet is

| Aspect | Fact | Source |
|---|---|---|
| Access | Waitlist; US account on Pro, Max or Ultra; not on Team or Enterprise "yet" | [1][2] |
| Surfaces | iOS and Android app, plus claude.ai on the web; separate from the main Claude app "for now" | [1][3] |
| Model and runtime | Opus 5.5, not selectable; a cloud VM with its own browser the user cannot watch or take over | [1][2] |
| Cadence | Morning brief; checks connected accounts "every few hours"; the user says what to hear about and how often | [1][2] |
| Interaction | One thread plus topic channels sharing memory; text or recorded voice messages | [2] |
| Work products | Tasks tab (only the user marks a task done); Make tab for briefs, decks, models, dashboards, web apps | [2] |
| Memory | Inherited from the account, kept until reset; searches Duet and Claude chats when enabled | [2][4] |
| Relation to Claude | "Can read your work in Claude but can't change it" | [2] |
| Cost | Included in the plan during the preview; shares its limits, background work included | [2] |

**Guardrails.**
- **Approval.** Before any send, payment, signature or deletion, Duet shows a card with the action, the destination, the content and every recipient [4].
- **Auto-mode classifier.** It checks each action against the user's intent and runs outside the working model. It is built on Claude Code's auto mode [4].
- **Sensitive-information classifier.** It checks every outgoing message and upload [4].
- **Credentials.** They never reach the model: passwords are sealed to the browser VM, and tokens are swapped in by an outside proxy [4].
- **Hard refusals.** Some actions are refused even with approval: trading securities, moving money between accounts, accessing another person's account [4].
- **Purchases.** Each one uses a one-time virtual card with a cap the user approves [8].
- **Prompt injection.** "No defense is perfect, which is one reason consequential actions need your approval" [2].

**Duet and Claude Code.** Two secondary sources report an update that lets Duet see and message Claude Code sessions [9][10]. No Anthropic page or changelog says so [11]. Treat it as unconfirmed. If it is real, it covers only sessions on Duet's own account. Separately, and officially, the Claude app already lists Remote Control and cloud sessions and pushes a notification when one needs permission [12].

**Reception.** Hands-on reviews report the following [9][13][14][15]:
- useful cross-app connections and good drafts;
- slow follow-through, including turnarounds of ten minutes or more;
- anxious alerts about deadlines that were not firm;
- stalled browser tasks;
- background usage that one reviewer measured at a few per cent of a weekly limit.

## 2. Overlap with the organisation layer

| Duet feature | Organisation-layer component | Verdict |
|---|---|---|
| Morning brief | equerry brief (desk, `equerry-secretary`), on the principal's cadence | **Bridge**: one brief per source family; Duet's may cite one line from the connector's triage tool |
| Watching apps, deciding what needs you | Rule-ranked sources (severity, band, age, cap), `crowsnest watch`, `ol owed` | **Keep the layer** for the fleet; **adopt Duet** for mail and chat |
| Proactive phone messages | Named push jobs with a daily budget, `ol remind` | **Keep the layer** for fleet pushes; Duet pushes about its own sources |
| Follow-ups on quiet mail threads | none | **Adopt Duet** |
| "Remind me" | `ol remind` (exact time) | **Keep `ol remind`** for anything the fleet must see; Duet's tasks are not exportable |
| Approval before acting | Consent table, the principal's tap, a publication check | **Keep the layer** for fleet writes; Duet's classifier cannot know what our tools mean |
| Doing admin knowledge work (drafts, scheduling, decks) | The front agent never does the work; teams and sessions do | **Adopt Duet** as a worker for non-code admin |
| Memory of the principal | acquaint record, principal model | **Keep the layer**: inspectable, sourced, versioned |
| Voice | a voice adapter over the text core; Claude's voice mode on the connector | **Keep the layer**; Duet only transcribes recorded voice messages |
| Session roster and control | crowsnest, hatchway fan-out, Remote Control | **Keep the layer**; the Claude app's own push answers permission prompts on one account |
| Publishing | annals | **Keep the layer** |
| Payments, signing | none | Adopt only if wanted |

## 3. Customisation surfaces

| Surface | Exists? | What it allows | Source |
|---|---|---|---|
| Chat instructions (what to hear about, how often) | Yes | Split fleet from life; tune cadence | [1][2] |
| Topic channels | Yes | A separate channel for fleet questions | [2] |
| Directory connectors (6,000+) | Yes, shared with the account | Mail, calendar, chat, drive, finance apps | [1] |
| Custom remote MCP connector | Not documented for Duet | The organisation layer inside Duet | [7] |
| Per-tool connector permissions | In claude.ai; not documented for Duet | Write tools set to "needs approval" | [7] |
| Skills | Inherited from the account; local Claude Code skills do not carry | A skill telling Duet how to use the layer's connector | [2][13] |
| Plugins | Supported (e.g. a small-business plugin); custom or Git-marketplace plugins not documented for Duet | Ship that skill and the connector as one plugin | [2][16][17] |
| Memory | Inherited, resettable | Little: not inspectable or exportable | [2][4] |
| Approval policy, model choice | Fixed | Nothing | [2][4] |
| Hooks, mods | No (mods are Claude Code only) | Nothing | [18] |
| API, SDK, webhooks, export | No | No outbound integration | [5] |
| MCP tunnels | Enterprise only | Not available on Duet's plans | [19] |

Duet is a closed product with two seams that matter, connectors and skills or plugins, and both are account-level. An organisation layer that already publishes a remote MCP connector has nothing new to build. It has a caller with nobody watching to defend against.

## 4. Open questions

1. Do custom connectors reach Duet, and are their per-tool permissions honoured there?
2. Does Duet see Claude Code sessions, and which ones?
3. Do uploaded skills or plugins from a Git marketplace reach Duet?
4. Can a connector server tell a Duet call from a chat call?
5. What does a week of Duet with connector reads cost against the plan's limits?

Each is settled by one test with access to the preview.

## What this means for the build

- Write no Duet-specific code. The connector the layer serves for claude.ai is also its Duet integration ([equerry#18](https://github.com/thorwhalen/equerry/issues/18) covers its write path).
- Every write class on the connector requires the principal's own confirmation outside the model before the connector is enabled in Duet, because a connector call no longer implies the principal is present.
- The brief keeps its own sources. When the principal uses Duet for mail, calendar or chat, the brief's "Not connected" line names Duet as the place those are covered, instead of implying nobody watches them.
- Hold any ingestion of mail, calendar or chat into the brief until a month of Duet use shows what it misses.
- A short skill telling Duet how to use the connector (overview tools first, writes only with the principal, fleet reminders through the layer) is the one artefact worth drafting, once open question 3 is answered.

## REFERENCES

[1] [Introducing Claude Duet — Anthropic, 2026-10-21](https://claude.com/resources/articles/claude-duet)
[2] [Get started with Claude Duet — Claude Help Center](https://support.claude.com/en/articles/17631495-get-started-with-claude-duet)
[3] [Claude Duet product page and FAQ — Anthropic](https://claude.com/product/claude-duet)
[4] [Claude Duet safety, privacy and security — Anthropic, 2026-10-23](https://claude.com/resources/articles/claude-duet-safety-privacy-and-security)
[5] [Claude Duet — DataCamp, 2026-10-25 (secondary)](https://www.datacamp.com/blog/claude-duet)
[6] [Prior art: AI secretaries and chiefs of staff, §4 — equerry research, 2026-11-09](prior-art-ai-secretaries.md)
[7] [Get started with custom connectors using remote MCP — Claude Help Center](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp)
[8] [How to use Link by Stripe with Claude Duet — Claude Help Center](https://support.claude.com/en/articles/17666484-how-to-use-link-by-stripe-with-claude-duet)
[9] [A personal assistant with short arms — Artificially Intimidating (hands-on)](https://artificiallyintimidating.com/p/claude-duet-review)
[10] [Always-on agents: Claude Duet — AI Regeneration (secondary)](https://airegeneration.substack.com/p/always-on-agents-claude-duet-the)
[11] [Claude Code changelog — Anthropic](https://code.claude.com/docs/en/changelog)
[12] [Remote Control — Claude Code docs](https://code.claude.com/docs/en/remote-control)
[13] [Claude Duet review: great design, slow follow-through — ChatPRD (hands-on)](https://www.chatprd.ai/how-i-ai/claude-duet-review)
[14] [Helpful, but still too eager — CCTest (hands-on)](https://cctest.ai/en/articles/claude-duet-review-useful-at-connecting-dots-still-bad-at-restraint)
[15] [Claude Duet review — eesel (secondary)](https://www.eesel.ai/blog/claude-duet-review)
[16] [Use plugins in Claude — Claude Help Center](https://support.claude.com/en/articles/13837440-use-plugins-in-claude)
[17] [Manage plugins, skills and connectors from one Plugins page — Claude Help Center](https://support.claude.com/en/articles/17740565-manage-plugins-skills-and-connectors-from-one-plugins-page)
[18] [Mods overview — Claude Code docs](https://code.claude.com/docs/en/plugins/mods/overview)
[19] [MCP tunnels overview — Claude docs](https://claude.com/docs/connectors/mcp-tunnels/overview)
