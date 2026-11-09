# Voice as a layer over a text organisation core

Research report, deep tier, 2026-11-09. Scope: what the text-based organisation layer (secretary, desk, briefs, proposals) must offer so that speech-to-text (STT) and text-to-speech (TTS) can be added, swapped or upgraded to full duplex without changing it. Prices are list prices read on or near this date; estimates are labelled; anything not confirmed against a primary source is marked **(unverified)**. The web-search budget ran out before the TTS price check was complete, so OpenAI and Cartesia TTS figures are taken from the prior work cited below rather than re-read.

## Summary and recommendation

**Prior work, not repeated here.** The owner's existing voice manager and its survey (two private reports dated 2026-10-27, plus a design record of 2026-10-31) already cover the vendor landscape (NotebookLM, ElevenLabs Agents, OpenAI Realtime, Gemini Live, Pipecat, LiveKit, Vapi, community Claude Code voice tools), the STT/TTS catalogue with licences and CPU feasibility, VAD and end-of-turn models, WebRTC versus WebSocket, the latency budget, and phone playback basics. Their verdict, "build the brain, buy the voice", cascaded STT → Claude → TTS, turn-based push-to-talk first, full duplex deferred, stands. This report covers what they did not: the contract between a text core and a voice adapter, the changes since 2026-10-27, and the brief-out direction.

**Main finding.** Every serious voice framework already treats the reasoning core as a text stream behind a seam: Pipecat's LLM service, LiveKit's `llm_node`, Vapi's custom-LLM URL, and, new since September 2026, OpenAI's GPT-Live-1, which is a full-duplex voice layer that hands thinking and tool calls to a text model you choose [1]. So "voice as a layer" is not exotic; it is the industry's default shape. What the frameworks demand of the core is also convergent, and small: stream the reply in speakable units, accept cancellation, record only what the listener actually heard, tolerate a slow tool without going silent, and treat the spoken channel as lower-trust input. The strongest new evidence for keeping the reasoning in text is τ-Voice (ICML 2026): on 278 grounded tasks, speech-native agents completed 31–51% on clean audio and 26–38% with noise and accents, against 85% for a text agent on the same tasks, keeping only 30–45% of the text capability [2].

**Recommendation.** Write the ten-line contract below into the organisation layer now, before any voice code touches it, and make the existing turn-based front end its first client. The four requirements that cost almost nothing now and a refactor later are: (1) every entity carries a speakable name and the core publishes one vocabulary that feeds both STT biasing and TTS pronunciation; (2) the transcript can truncate an assistant message to what was heard; (3) proposals carry a reversibility tier that decides whether a spoken "yes" is enough; (4) briefs are structured documents (items with ids, priority, spoken and written forms) whose "heard" position lives in the core, with audio as a derived, cacheable rendering. For briefs on an iPhone, render one continuous audio file per brief with chapters and a sentence-aligned transcript, not a playlist of per-item files, because WebKit closes a backgrounded web app's audio session when a track ends naturally [3].

## The contract: what the text core must satisfy for voice to be an adapter

Each requirement names the framework behaviour that motivates it. Together they are the test that voice is a layer: if an adapter needs anything not on this list, the boundary is wrong.

1. **One turn API, text in, a stream of events out.** `turn(conversation_id, user_turn) -> async stream of events`. Every framework surveyed calls the brain this way: Pipecat as an LLM service in a frame pipeline, LiveKit through `llm_node(chat_ctx, tools, model_settings)` which yields text chunks [4], Vapi through an OpenAI-compatible chat-completions endpoint that should stream server-sent events [5], GPT-Live-1 through a backend text model [1]. The core never sees audio; audio, if kept, is an attachment referenced by id.
2. **A user turn carries its provenance.** Fields: `text`, `modality` (typed, push-to-talk, open-mic, wake-word), STT `confidence` and optional alternatives, `locale`, `captured_at`. The core's existing rule that a turn which has read others' text may not act until the owner speaks again needs to know *who* spoke: push-to-talk is the owner's deliberate intent; an open microphone may be hearing a podcast, a colleague, or the agent's own echo. Open-mic and wake-word turns should therefore enter at a lower trust level than push-to-talk turns.
3. **Replies stream in speakable segments with two renderings.** Each `segment` event has an id, `display` text (Markdown allowed) and `spoken` text (no Markdown, numbers and identifiers written out). TTS can only start on a complete clause [prior survey], and frameworks already strip Markdown at the edge (Pipecat's `text_transforms` with `strip_markdown`, replacing the deprecated `MarkdownTextFilter` [6]; LiveKit's `tts_node` for pronunciation replacements [4]), but an edge filter cannot know that `sweep2-qh` is "the second sweep session" or that `#177` is "crowsnest issue one-seven-seven". Only the core knows that, so the core supplies the spoken form or the lexicon.
4. **Cancel and commit-heard.** `cancel(turn_id)` stops generation and in-flight cancellable tools; `heard(turn_id, upto=segment_id | char_offset)` truncates the stored assistant message to what played. Pipecat commits "only the words that were actually spoken" and exposes an `interrupted` flag [7]; LiveKit truncates the transcript "to match the spoken output" [8]; OpenAI Realtime's `conversation.item.truncate` with `audio_end_ms` deletes the unheard server-side transcript [9]. If the core keeps the full unheard text, the next turn reasons from words the owner never heard ("as I said…"). Tools need a declared `cancel_on_interruption` property, as Pipecat has [7]; actions never do, because they are proposals (item 6).
5. **Early acknowledgement and deferred replies.** The core emits an `ack` (a short spoken line, e.g. "checking the parser session") before slow tool work, `progress` events while it runs, and may deliver a `deferred_reply` later into the same conversation, by push if the owner has moved on. An agentic turn breaks any conversational latency budget [prior survey]; Gemini Live's default non-blocking function calling, which lets the model keep talking while a tool runs [10], and GPT-Live-1's "keeps talking while that work runs in the background" [1] both assume a core that can answer later.
6. **Actions are proposals with a reversibility tier.** Every consequential act is a `Proposal` with an id, the exact words of the action, a `tier` (reversible-cheap, reversible-costly, irreversible) and an expiry; confirmation is a separate call `confirm(proposal_id, channel, evidence)`. Voice-design guides agree: explicit confirmation only where an error costs money or data, implicit confirmation elsewhere [11][12]; Apple's App Intents call `requestConfirmation` "before performing any work that might be destructive or unsafe" [13]. The tier, not the adapter, decides whether a spoken "yes" after a read-back suffices or a tap on the card is required. The existing manager's card already does the second; the tier makes the first possible without weakening it.
7. **Clarification is a structured result.** When the core is unsure which session, person or item is meant, it returns `choices` (at most three, each with a spoken label) rather than prose. A listener has about eight seconds to take in options and answer [14], and Alexa's guidance is "one to a few options at a time" [14]. A screen can show ten; a voice cannot.
8. **One vocabulary, published by the core.** A `vocabulary()` endpoint returns every speakable entity: session names, repositories, people, verbs ("snooze", "later", "dispatch"), each with its written form, spoken form, optional IPA, and aliases. The same list feeds STT keyterm biasing (Scribe v2 keyterms, Deepgram keyterm prompting, AssemblyAI keyterms up to 100 live terms, updatable mid-stream [15][16][17]; Apple's `DictationTranscriber` contextual strings [18]) and TTS pronunciation (ElevenLabs alias rules work with all models; phoneme rules only with some [19][20]). Without it, domain words are both misheard and mispronounced, and the two errors are fixed in two places.
9. **Briefs are documents; audio is a rendering.** A brief is a list of items, each with id, priority, `headline_spoken` (one breath), `body_spoken`, `body_display` and links, grouped into chapters. The core owns the per-item state: `heard`, `skipped`, `acted`. Playback reports `heard(item_id)` back, so the desk can avoid repeating what was heard, a second device can resume, and "what did I miss" means what was not *heard*, not what was not generated. This is the voice analogue of crowsnest's `seen` verb.
10. **Every item carries a privacy class.** `public`, `private`, `restricted`. The adapter routes private segments to local STT/TTS (Kokoro, Whisper, Parakeet) and restricted ones to text only. A single brief can mix classes, so routing is per segment, not per session. Cloud STT and TTS see everything they render.

Not in the contract, on purpose: audio formats, transports (WebRTC, WebSocket, HTTP upload), VAD, end-of-turn detection, barge-in policy, voices and engines. These belong to the adapter and change without touching the core.

## What each voice architecture demands of the core

| Architecture | How the text brain plugs in | Barge-in and truncation | Turn detection | What it demands of the core | Verdict |
|---|---|---|---|---|---|
| Pipecat (BSD-2) | LLM service in a frame pipeline; can be replaced by a processor that calls the core's turn API | Interruption frames cancel LLM, TTS and queued audio; only spoken text is committed; `on_assistant_turn_stopped` reports `interrupted` [7] | VAD plus pluggable turn-start strategies (min-words, Krisp backchannel filter) [7] | Streaming, cancel, commit-heard, `cancel_on_interruption` per tool | Best fit for the duplex stage; the prior survey's pick |
| LiveKit Agents (Apache-2.0) | Override `llm_node` to call the core; `on_user_turn_completed` to inject context [4] | Transcript truncated to spoken output; TTS-aligned transcripts at word level for Cartesia, ElevenLabs, Rime, Speechify [8] | Adaptive interruption model (1.5.0) separates backchannels from barge-ins; resumes after false interruptions; vendor claims 86% precision, rejects 51% of VAD barge-ins [21][22] | Same as Pipecat; text and voice in one session (`lk.chat` text interrupts speech) [8] | Equal alternative; stronger turn-taking out of the box, heavier to self-host |
| Vapi | Custom-LLM URL, OpenAI chat-completions shape, SSE streaming recommended [5] | Platform-managed | Platform-managed | An OpenAI-compatible facade over the turn API | Not needed (telephony-first), but the facade is cheap and also serves other OpenAI-compatible hosts |
| OpenAI Realtime (`gpt-realtime-2.x`) | The model is the brain; Claude only as a tool | `conversation.item.truncate` with `audio_end_ms` [9] | `server_vad`, `semantic_vad` with eagerness low/medium/high, or manual commit (push-to-talk) [23] | Tool endpoints; nothing else, since it reasons itself | Reject as brain (τ-Voice gap [2]); acceptable as a fast "receptionist" calling the core |
| GPT-Live-1 (API since 2026-09-10) | Full-duplex voice layer that delegates thinking and tools to a chosen text model, "alongside third-party models" [1] | Built in | Built in (full duplex) | Exactly items 1, 4, 5 of the contract; whether a custom endpoint (rather than a listed model) can be the backend is **unverified** | Watch: the first hosted product shaped like this contract. $0.05/min voice layer, backend billed separately [1] (secondary source) |
| Gemini Live | The model is the brain; non-blocking tools let it talk while a tool runs [10] | Provider-managed | Provider-managed; 15-minute audio sessions without compression, 128k context, resumption handles valid 2 h [24] | Tool endpoints; deferred replies (item 5) | Cheapest receptionist; same brain objection as OpenAI |
| Apple Speech (`SpeechAnalyzer`, iOS 26+) | On-device STT only; native apps only | n/a | App-managed | Vocabulary (contextual strings on `DictationTranscriber` [18]; long-form `SpeechTranscriber` may lack them [25], **unverified**) | Relevant only if a native iOS shell is built; free and private |
| Browser Web Speech | STT in the page | n/a | Browser-managed, unreliable | Nothing | Not on iOS home-screen web apps [prior survey]; Chrome has on-device recognition (`processLocally`, en-US and de-DE) and a 2026 quality hint `command`/`dictation`/`conversation` [26][27]. Desktop-only convenience |
| Moshi / Kyutai (weights CC-BY-4.0, code MIT/Apache) | Moshi is a full-duplex speech-to-speech model with its own 7B brain; needs a 24 GB GPU or Apple MLX [28] | Native | Native; Kyutai STT 1B predicts end-of-turn ("semantic VAD") with 0.5 s delay [29] | Nothing; it replaces the brain | Reject Moshi as brain; Kyutai STT is a candidate local streaming STT once a GPU exists (licence of the STT weights **unverified**) |

## STT and TTS: changes since 2026-10-27, with verdicts

The prior survey's tables remain valid. What changed or was newly verified:

| Option | Kind | Licence | Cost (list) | Change or note | Verdict for this system |
|---|---|---|---|---|---|
| ElevenLabs Scribe v2 Realtime | Cloud STT, streaming | Proprietary | $0.39/h (≈$0.0065/min); keyterm prompting +$0.08/h [15] | Keyterms are the fix for domain words | Keep as the cloud default (already chosen); turn on keyterms from the core's vocabulary |
| Deepgram Flux / Nova-3 | Cloud STT | Proprietary | Flux $0.0077/min; Nova-3 pre-recorded $0.0043/min; keyterms +$0.0013/min [16] | Earlier "promotional" Flux price of $0.0065 is no longer listed | Duplex-stage alternative (Flux has a built-in end-of-turn signal [prior survey]) |
| AssemblyAI Universal-3.5 Pro / 3.6 Pro Realtime | Cloud STT | Proprietary | Async $0.21/h; realtime $0.45/h with up to 100 keyterms included and updatable mid-stream; session time billed, idle included [17] | New since the prior survey; mid-stream keyterm updates suit a vocabulary that changes as sessions spawn | Alternative; close idle sockets |
| OpenAI `gpt-4o-mini-transcribe` | Cloud STT | Proprietary | $0.003/min [prior survey] | Realtime transcription sessions support manual commit, i.e. push-to-talk [23] | Cheapest cloud fallback |
| faster-whisper / whisper.cpp | Local STT | MIT | Free | Unchanged; CPU large models are not real time [prior survey] | Keep as the local fallback for private content |
| NVIDIA Parakeet TDT 0.6B v3; Parakeet-unified-en-0.6b | Local STT | v3 CC-BY-4.0; unified model NVIDIA Open Model License [30] | Free | Unified model does offline and streaming in one English model, inference-only release [30] | v3 stays the permissive local choice; check the Open Model License before adopting the unified one |
| Apple `SpeechAnalyzer` | On-device STT | Platform | Free | iOS 26+, on-device only, model ships with the OS [25] | Only with a native shell |
| ElevenLabs Eleven v4 / v4 Turbo | Cloud TTS | Proprietary | $80 per 1M characters on one reseller listing, 50% launch discount to 2026-10-19 [31] (official price **unverified**) | Released 2026-09-28; v4 Turbo median time-to-first-speech ~150 ms (vendor figure); better IPA handling; audio tags [32]; SSML `<break>` reportedly disabled **(unverified)** | Keep (already chosen). Use the with-timestamps endpoint, which returns character-level alignment with the audio [33] |
| AssemblyAI TTS-1 | Cloud TTS | Proprietary | $15 per 1M characters [17] | New | Cheap cloud fallback worth a listening test |
| Deepgram Aura-2 | Cloud TTS | Proprietary | $0.030 per 1k characters [16] | — | No advantage here |
| OpenAI `gpt-4o-mini-tts`, Cartesia Sonic, Gemini Flash TTS | Cloud TTS | Proprietary | See prior survey | Not re-verified (search budget) | Unchanged verdicts |
| Kokoro-82M v1.0 | Local TTS | Apache-2.0 weights; 54 voices, 8 languages [34] | Free | No release since 2025-01 [34] | Keep as the local, private default |
| Piper | Local TTS | Engine GPL-3.0 [prior survey] | Free | — | Avoid in anything distributed |

Cost of a ten-minute brief, about 9,000 characters (estimate): Eleven v4 at the reseller rate ≈ $0.72; AssemblyAI TTS-1 ≈ $0.14; Kokoro $0. A brief rendered every weekday at Eleven v4 rates is about $16 a month (estimate, 22 briefs); local rendering for private items reduces this.

## Voice UX, direction one: commands and short questions in

**Push-to-talk stays the input mode.** The owner's front end already records a whole utterance and transcribes it once, and the design record explains why: browser recognisers submit too early and too late. Nothing since changes that. Push-to-talk also gives the strongest provenance signal (contract item 2). A wake word is not available to a web page on iOS, which cannot keep a microphone open in the background (the prior survey's PWA findings); in a native app it would need a model, and the popular open option, openWakeWord, has Apache-2.0 code but CC BY-NC-SA 4.0 pre-trained phrase models, so a commercial build must train its own phrase heads [35]. Full duplex is the deferred stage below.

**Fast-path commands live in the adapter.** "Pause", "next", "back", "repeat", "louder" should never reach the core; they are player controls. Commands that change state ("snooze that until Monday", "mark done") go to the core as ordinary turns and come back as proposals or immediate reversible actions according to tier.

**Confirmation by voice.** The design guides converge on three levels: explicit (a spoken yes is required before acting), implicit (the system restates what it heard and proceeds), contextual (the next response shows it understood) [11][12]. Map them onto the proposal tier: reversible-cheap acts (snooze, mark seen) proceed with implicit confirmation and an "undo" window; reversible-costly acts (send a message to a session) need an explicit read-back and a spoken yes; irreversible acts (merge, delete, send an email to a person) need the card tap even when the conversation is spoken. Read the action back in its exact words, attribution first ("To the parser session: …"), and never accept "yes" heard during the system's own playback (echo). Google adds the inverse rule: do not over-confirm simple, high-confidence input [12].

**Errors.** After two misrecognitions in a row, stop asking by voice and put the choice on screen; Google's guidance caps repeated no-match prompts at two or three before exiting [12].

## Voice UX, direction two: briefs out

**Write for the ear, in the core's `spoken` fields.** The radio and voice-assistant guidance is consistent: short sentences of one idea (limits quoted range from 8–12 to 20–25 words) [36][37]; attribution before the claim [36]; round numbers ("nearly four in ten", not 38.7%) [36]; read the draft aloud, the "one-breath test" [38][37]; progress markers ("first", "two more", "last one") [39]; contractions and active voice [39]; ask a question only at the end, right before the microphone opens [39]. For this system, that becomes a short style card for the brief writer: lead each item with who needs what ("The release session needs a yes on the version bump"); one item per paragraph; at most three items before a signpost ("That's the urgent ones. Four more can wait."); identifiers spoken by their spoken name, never as `#177` or a hash; no tables, code or URLs in spoken text, but "it's on your desk" instead.

**Normalise before synthesis, not in the engine.** Engines normalise numbers, dates and acronyms inconsistently; ElevenLabs' own advice for LLM-written text is to have the model write numbers, dates and acronyms out in words [19]. The core's writer does this; a lexicon from contract item 8 catches the rest.

**Chapters.** Give each brief chapters (urgent, waiting on you, done, later), each with a start time. Two standard encodings exist: Podcasting 2.0's JSON chapters file and `<podcast:transcript>` (the namespace is CC0, and Apple Podcasts supports both tags) [40], and in-page `MediaMetadata.chapterInfo`, which is experimental and not Baseline [41]. Use the JSON chapters shape as the stored format and draw chapter buttons in the page; do not rely on the lock screen to show chapters.

**Pause and resume.** Keep the position in two places: the player (`MediaSession.setPositionState`, supported in Safari since 15 [42]) for seconds, and the core (contract item 9) for items. Resume by item boundary, not by second, after more than a few minutes away, and re-announce the current item's headline.

**Transcript in sync.** Render sentence by sentence (already the design for answers) and the concatenation gives sentence-level timestamps for free, with any engine, local or cloud. ElevenLabs' with-timestamps endpoint adds character-level alignment in the same stream [33]; LiveKit's TTS-aligned transcripts do the same in the duplex stage [8]. Store the result as WebVTT next to the audio. Sentence level is enough to highlight the current sentence and to tap a sentence to seek.

**Phone constraints that shape briefs.** A report from 2026-10 describes WebKit closing a home-screen web app's audio session when an `<audio>` element ends naturally while backgrounded, after which iOS will not reopen it until the app returns to the foreground; the workaround is to loop the element so `ended` never fires, and to re-register Media Session next/previous handlers on each `playing` event so the lock screen shows them [3]. The simpler design is one continuous file per brief with chapter offsets, which avoids track changes entirely. For recording, the Audio Session API (`navigator.audioSession.type = "play-and-record"`) is Safari-only and still a draft [43][44]; set it before opening the microphone and back to `playback` after, because the prior survey and WebKit reports describe degraded output while capture is active.

## Recommended v1 (turn-based) and the path to full duplex

**v1, what the organisation layer must ship for the existing front end.** No new voice technology. The core implements contract items 1–3, 6, 8, 9 and 10; the existing front end calls it instead of its own manager loop, or the manager becomes the core's first adapter. Concretely: the turn API with provenance; `spoken`/`display` segments; proposals with tiers; `vocabulary()` wired into Scribe keyterms and the TTS lexicon; briefs as item lists with heard-state; privacy class per item with local Kokoro/Whisper rendering for private ones; one audio file per brief with chapters and WebVTT. Items 4 and 5 can be stubs in v1 (tap-to-stop truncates at the current sentence; ack lines are written by the core) but their signatures should exist.

**Path to full duplex, in stages that add at the boundary.**

1. *Half duplex with spoken barge-in, headphones only.* Open the microphone during playback with platform echo cancellation; the adapter's VAD stops playback and calls `heard()`; contract item 4 becomes real. Tests the iOS echo-cancellation issues the prior survey flagged.
2. *Cascaded duplex.* Pipecat or LiveKit on the server over WebRTC; the core plugs in as the LLM node, streaming segments; turn detection, backchannel filtering and false-interruption resume come from the framework [7][21]. The core does not change; if it has to, the contract was wrong.
3. *Optional receptionist.* A speech-native model (GPT-Live-1 delegating to the core, or Gemini Live calling it as a non-blocking tool) handles small talk, acknowledgements and "hold on", while every substantive turn goes through the core. Adopt only if stage 2 feels slow; τ-Voice is the reason the receptionist never decides anything [2].

## Evaluation

**Word error rate on the fleet's words, not on LibriSpeech.** Overall WER barely moves when domain terms fail, because they are a small share of words; the contextual-biasing literature splits it into B-WER (on the bias list) and U-WER (everything else) and reports recall on the bias terms [45]. Build a set of about 100 utterances recorded by the owner on the phone, outdoors and indoors, each containing session names, repository names, people and verbs from `vocabulary()`; score B-WER, U-WER and entity recall per backend, with and without keyterms. Re-run when the vocabulary changes materially.

**Task success, typed versus spoken.** The τ-bench family now runs the same tasks half-duplex in text and full-duplex in voice [46], which is the right design to copy at small scale: 30 scripted requests ("snooze the release session until Monday", "what is waiting on me") issued once typed and once spoken; measure the rate of correct proposals and correct answers, and the gap between the two. The gap is the cost of the voice layer; it should be small for a cascaded design, and τ-Voice's 85% versus 26–51% is the warning of what a large one looks like [2].

**Safety metric.** False-accept rate of consequential proposals from speech: the number of times an irreversible action was confirmed by something other than the owner's deliberate yes. Target zero; any occurrence is a bug in the tier mapping.

**Brief metrics.** Time to first audio; share of items heard to the end; skip rate per chapter; items acted on within a day; how often the owner asks for a repeat (a proxy for sentences that do not work aloud).

## Standard terminology

- **Cascaded pipeline**: STT → text model → TTS. **Speech-to-speech (S2S)** or **speech-native**: one model hears and speaks.
- **Half duplex**: one side speaks at a time. **Full duplex**: both can speak at once, the system listening while talking.
- **Push-to-talk (PTT)**, **wake word** (keyword spotting), **open mic**.
- **VAD** (voice activity detection); **endpointing** or **end-of-turn detection**; **semantic VAD** (end-of-turn judged from the words, not silence) [23][29].
- **Barge-in**: the user interrupts playback. **False interruption**: noise or a backchannel treated as barge-in. **Backchannel**: "mm-hm", "right", which should not stop the speaker [21].
- **Truncation** (commit-heard): cutting the stored assistant turn to what was played [7][9].
- **AEC**: acoustic echo cancellation.
- **TTFA / TTFB / time to first speech**: delay from end of user speech (or from request) to first audio.
- **RTF**: real-time factor of a model.
- **Text normalisation**: turning "#177", "3.2%" and dates into words. **Lexicon / pronunciation dictionary**: alias and phoneme rules [19][20]. **SSML**: Speech Synthesis Markup Language. **Audio tags**: inline delivery cues in newer engines [32].
- **Keyterm prompting / contextual biasing / hotwords**: telling STT which rare words to expect [15][16][17].
- **WER**, **B-WER / U-WER**, **entity recall** [45].
- **Explicit, implicit, contextual confirmation** [11]. **Read-back**: restating the action in its exact words.
- **Earcon**: a short non-speech sound marking an event. **Signposting / progress markers**: "first", "two more" [39].
- **Forced alignment / TTS-aligned transcript**: timestamps linking text to audio [8][33].

## What else matters that was not asked

- **Speakable names are a data-model decision, not a voice feature.** Session and branch names in this fleet are written for terminals. If the organisation layer does not give each entity a spoken name at creation time, every voice backend will mishear and mispronounce them, and the fix will be scattered across prompt hacks.
- **Audio is a cache, text is the record.** Key rendered audio by a hash of (spoken text, voice, engine, version) and store it as a derived artefact; the transcript and the brief items are the source of truth. This makes swapping engines free and makes re-rendering after an edit cheap.
- **Content leaves the machine twice.** Cloud STT hears the owner; cloud TTS reads the brief. A brief can contain material from private repositories or messages; per-segment privacy routing (contract item 10) is the only design that keeps one brief playable while honouring both.
- **Audio is an injection channel.** An open microphone hears anything nearby, including other speech audio; a spoken instruction from a podcast is untrusted input. Provenance on the turn (item 2) and tiers on proposals (item 6) contain it; content filtering alone cannot.
- **Voice is never the only path.** Every voice action needs a tap equivalent and every spoken brief a readable page, both for noisy places and because the owner reads on a phone most of the time.
- **Anthropic still offers no developer speech API or voice for Claude Code Remote Control.** Claude Code's `/voice` is dictation only, and requests for spoken replies on the phone remain open [47][48][49]; nothing found changes the prior survey's "watch" items. A search for an official Claude realtime voice API found none **(not conclusive: the search budget ran out before a primary-source check)**.

## What this means for the build

1. Put the ten-item contract in the organisation layer's first interface; the existing voice front end becomes its first adapter, not a second core.
2. Add `spoken_name` (and optional IPA, aliases) to every entity the secretary can mention, and a `vocabulary()` call that feeds STT keyterms and the TTS lexicon.
3. Model a brief as items with ids, priority, spoken and display forms, chapter and privacy class; store heard/skipped/acted in the core, next to crowsnest's `seen`.
4. Give every proposal a reversibility tier; spoken "yes" only below the irreversible tier, card tap above it.
5. Make the transcript truncatable (`heard()`) and the turn cancellable now, even if v1 only uses tap-to-stop.
6. Emit `ack`, `progress` and `deferred_reply` events from slow turns.
7. Render briefs as one continuous file with JSON chapters and WebVTT from sentence-by-sentence synthesis; route private segments to local Kokoro.
8. Build the 100-utterance B-WER set and the 30-task typed-versus-spoken check before choosing between STT backends or moving to duplex.
9. Do not adopt a speech-native brain; revisit GPT-Live-1-style delegating voice layers only as a receptionist in front of the core.

## REFERENCES

1. [DataNorth: OpenAI launches GPT-Live-1 in the API](https://datanorth.ai/news/openai-launches-gpt-live-1-in-the-api) (2026-09-11; secondary source, OpenAI's page not read)
2. [Ray S, Dhandhania K, Barres V, Narasimhan K. τ-Voice: Benchmarking Full-Duplex Voice Agents on Real-World Domains. arXiv:2603.13686 (2026-03-14; ICML 2026)](https://arxiv.org/abs/2603.13686)
3. [Getting my iPhone music app to keep playing on the lock screen (Medium, 2026-10)](https://amsyeung.medium.com/getting-my-iphone-music-app-to-keep-playing-on-the-lock-screen-a5fb15311002) (single practitioner report; iOS version not stated)
4. [LiveKit docs: Pipeline nodes and hooks](https://docs.livekit.io/agents/logic/nodes.md)
5. [Vapi docs: Custom LLM, using your server](https://docs.vapi.ai/customization/custom-llm/using-your-server)
6. [Pipecat docs: Text to speech (text transforms)](https://docs.pipecat.ai/pipecat/learn/text-to-speech)
7. [Pipecat docs: Interruptions](https://docs.pipecat.ai/pipecat/fundamentals/interruptions)
8. [LiveKit docs: Text and transcriptions](https://docs.livekit.io/agents/multimodality/text.md)
9. [OpenAI Ruby SDK reference: ConversationItemTruncateEvent](https://www.rubydoc.info/gems/openai/OpenAI/Models/Realtime/ConversationItemTruncateEvent)
10. [Google AI for Developers: Live API capabilities guide](https://ai.google.dev/gemini-api/docs/live-api/capabilities) (non-blocking function calling, as read by the prior survey on 2026-10-27)
11. [Amazon Alexa Haus: Be trustworthy (confirmations)](https://developer.amazon.com/en-US/alexa/alexa-haus/design-principles/be-trustworthy)
12. [Google conversation design: Confirmations](https://developers.google.com/assistant/conversation-design/confirmations) and [Errors](https://developers.google.com/assistant/conversation-design/errors)
13. [Apple Developer: AppIntent.requestConfirmation](https://developer.apple.com/documentation/appintents/appintent/requestconfirmation(conditions:actionname:dialog:showdialogasprompt:content:).md)
14. [Amazon Alexa Haus: Lists pattern](https://developer.amazon.com/en-US/alexa/alexa-haus/patterns-and-components/patterns-lists)
15. [ElevenLabs API pricing](https://elevenlabs.io/pricing/api)
16. [Deepgram pricing](https://deepgram.com/pricing)
17. [AssemblyAI pricing reference](https://www.assemblyai.com/llms/pricing.md)
18. [Apple Developer: DictationTranscriber](https://developer.apple.com/documentation/speech/dictationtranscriber.md)
19. [ElevenLabs help: Why are numbers, dates, symbols and acronyms not properly pronounced?](https://elevenlabs.io/docs/help-center/product/core-capabilities/text-to-speech/why-are-numbers-dates-symbols-and-acronyms-not-properly-pronounced-or-spoken-in-the-correct-language)
20. [Vapi docs: Pronunciation dictionaries](https://docs.vapi.ai/assistants/pronunciation-dictionaries)
21. [LiveKit docs: Adaptive interruption handling](https://docs.livekit.io/agents/logic/turns/adaptive-interruption-handling/)
22. [livekit-agents 1.5.0 release notes](https://newreleases.io/project/github/livekit/agents/release/livekit-agents@1.5.0)
23. [OpenAI docs: Voice activity detection (Realtime)](https://developers.openai.com/api/docs/guides/realtime-vad)
24. [Google AI for Developers: Session management with Live API](https://ai.google.dev/gemini-api/docs/live-session)
25. [Crosley B. Apple's new Speech framework: SpeechAnalyzer vs SFSpeechRecognizer](https://blakecrosley.com/blog/speech-framework-vs-sfspeechrecognizer) (third-party)
26. [MDN: SpeechRecognition.processLocally](https://developer.mozilla.org/docs/Web/API/SpeechRecognition/processLocally)
27. [blink-dev: Intent to Ship, Web Speech API on-device recognition quality](https://groups.google.com/a/chromium.org/g/blink-dev/c/P8P-x7AnC6I) (2026-05)
28. [kyutai-labs/moshi (GitHub)](https://github.com/kyutai-labs/moshi)
29. [Kyutai STT](https://kyutai.org/stt)
30. [NVIDIA: Parakeet-unified-en-0.6b model card](https://huggingface.co/nvidia/parakeet-unified-en-0.6b) (read via a mirror of the card)
31. [OpenRouter: ElevenLabs Eleven v4](https://openrouter.ai/elevenlabs/eleven-v4) (reseller listing)
32. [ElevenLabs blog: Eleven v4](https://elevenlabs.io/blog/eleven-v4) (2026-09-28, updated 2026-10-19)
33. [ElevenLabs API reference: Stream speech with timing](https://elevenlabs.io/docs/api-reference/text-to-speech/stream-with-timestamps)
34. [hexgrad/Kokoro-82M model card](https://huggingface.co/hexgrad/Kokoro-82M)
35. [davidscripka/openwakeword models (Hugging Face)](https://huggingface.co/davidscripka/openwakeword)
36. [Media Helping Media: Writing a radio news script](https://mediahelpingmedia.org/quick-guides/writing-a-radio-news-script/)
37. [University of Florida IFAS: Writing for the ear (WC193)](https://edis.ifas.ufl.edu/publication/wc193)
38. [Amazon Alexa blog: Things every Alexa skill should do, pass the one-breath test](https://developer.amazon.com/blogs/alexa/post/531ffdd7-acf3-43ca-9831-9c375b08afe0/things-every-alexa-skill-should-do-pass-the-one-breath-test)
39. [Amazon Alexa Haus: Natural speech](https://developer.amazon.com/en-US/alexa/alexa-haus/natural-speech)
40. [Podcastindex-org/podcast-namespace (GitHub)](https://github.com/Podcastindex-org/podcast-namespace)
41. [MDN: MediaMetadata.chapterInfo](https://developer.mozilla.org/docs/Web/API/MediaMetadata/chapterInfo)
42. [MDN: MediaSession.setPositionState()](https://developer.mozilla.org/docs/Web/API/MediaSession/setPositionState)
43. [MDN: Audio Session API](https://developer.mozilla.org/en-US/docs/Web/API/Audio_Session_API)
44. [W3C: Audio Session (Editor's Draft)](https://w3c.github.io/audio-session/)
45. [Contextual biasing evaluation with biased and unbiased WER, e.g. Improving contextual recognition of rare words with an alternate spelling prediction model, arXiv:2209.01250](https://arxiv.org/pdf/2209.01250)
46. [sierra-research/tau2-bench (GitHub)](https://github.com/sierra-research/tau2-bench)
47. [anthropics/claude-code#104372: voice mode in the mobile Code tab for Remote Control sessions](https://github.com/anthropics/claude-code/issues/104372)
48. [anthropics/claude-code#104930: voice conversation mode for Claude Code sessions](https://github.com/anthropics/claude-code/issues/104930)
49. [Claude Help Center: Use voice mode](https://support.claude.com/en/articles/11101966-use-voice-mode)
