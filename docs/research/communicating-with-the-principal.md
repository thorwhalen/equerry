# Communicating decisions to a time-poor principal in the language they know

Research report, 2026-11-18. Theme: how an organisation layer where many agents report to one human can state decisions and information in the reader's own vocabulary, at the right altitude, while keeping the full rationale for agents, for on-demand reading and for audio. Written for a public repository: no private names. Where a claim rests on a secondary source or could not be checked against the original, that is said inline.

## Summary and recommendation

The literature points to one structure, and it is not "write shorter".

1. **Frame the problem, not the options.** Every executive-briefing tradition surveyed (completed staff work, BLUF, the Minto pyramid, decision memos, the President's Daily Brief) puts the answer or the ask first and requires the author to do the thinking so the reader only has to approve, redirect or defer [1][2][3]. An "A or B?" with a jargon-heavy rationale breaks the first rule: it hands the unfinished work to the reader. The better shape is: here is the situation in your terms, here is the tension that forces a decision, here is what I recommend and why that is safe, here is what each alternative would cost you in things you can observe, and here is the standing rule that would stop me asking again (the meta-solution).
2. **Model the reader's vocabulary explicitly, not their "level".** Educational user modelling gives a ready mechanism: an overlay model, one estimate per domain concept (a term or idea), updated from evidence, and open to inspection and correction by the learner [4][5][6]. A per-reader lexicon of terms (known, unknown, learning) with provenance is a small overlay model. It drives two operations: replace or gloss unknown terms, and spend the freed words on technical detail the reader does know. This resolves the stated tension (less jargon they do not know, more detail they do know): "jargon" is relative to the reader, so the same technical word is noise in one brief and signal in another.
3. **One structured record, several renderings.** Write each decision once as a typed record with a principal layer and an agent layer, and generate the phone text, the spoken script and the audio-overview input from it, in the single-source publishing tradition [7]. The short rendering must never be the only copy of the trade-off.
4. **Guard the compression.** LLM simplification loses information often [8], explanations raise reliance on wrong answers as well as right ones [9], and personalising to a reader raises measured sycophancy in several studies [10][11]. So the short rendering needs an independent check against the full record for dropped trade-offs, and the recommendation must carry what would change it.
5. **Measure with cheap behavioural signals,** not satisfaction: time to decision, follow-up questions asked, reversals, and a one-line teach-back sample [12].

What is new relative to the earlier equerry reports, which are not repeated here: those cover brief schemas, ranking, alert fatigue, voice architecture and principal preference modelling [E1][E2][E3][E4]. This report adds the reader-knowledge model and the framing discipline for the content of each item.

## 1. Modelling what a specific reader knows

### 1.1 The failure being fixed: the curse of knowledge

The "curse of knowledge" is the bias where a person who knows something cannot easily reconstruct what it is like not to know it. The term is credited to Camerer, Loewenstein and Weber (1989) in economics; the best-known demonstration is Elizabeth Newton's 1990 tapping study, in which tappers predicted far higher listener success than the listeners achieved (the commonly cited figures are about 50% predicted against 2.5% actual, 3 of 120 songs) [13]. I could not reach the original paper or dissertation; the figures are from secondary summaries and the 50% prediction is reported inconsistently, so treat the exact numbers as unverified. The mechanism is what matters here: an agent that has just spent an hour inside a codebase is the tapper, and its brief is the tapping.

A neighbouring finding: people overestimate how well they understand causal mechanisms until asked to explain them (the illusion of explanatory depth, Rozenblit and Keil 2002) [14]. It is specific to explanatory knowledge and parts of the original measurement have been challenged [14]. Implication for equerry: a principal's "yes, I know what that means" is weak evidence about whether they can use the term to decide, so the lexicon should weight behavioural evidence (used the term correctly, decided correctly on a card containing it) above self-report.

### 1.2 User-model families that fit

- **Overlay models.** Brusilovsky's review of adaptive hypermedia describes the overlay model as the most common way to model user knowledge: for each topic in the domain, the system keeps an estimate of the user's familiarity, and topics are measured independently. Stereotype models are simpler but less flexible [4]. Fit: a term-level lexicon is an overlay over a glossary of the principal's domains. (The 2001 paper itself was not read in full; the description of overlay models is from the 1996 review and a 2007 chapter as summarised by the search results [4].)
- **Knowledge tracing.** Corbett and Anderson (1995) introduced Bayesian knowledge tracing, a latent-variable model of skill acquisition updated from observed successes and failures [15]. Its original form assumes that a learned skill is never forgotten [15]. Fit: useful as the update rule (a term moves toward "known" after correct uses) but the no-forgetting assumption is wrong for rarely used terms; add decay or a "last seen" date. Estimate: a simple count-and-recency rule is enough for a one-week pilot; the full Bayesian machinery is not justified for one reader.
- **Scrutable (open, inspectable) user models.** Kay's scrutability work argues that the system should be able to explain what it believes about the user and why, and that the user should be able to inspect and correct it [16][17]. I confirmed the bibliographic details and the 2001 description; I did not read the 2006 paper's argument [17]. Fit: the lexicon must be a visible file the principal can open and edit, with the evidence for each entry; otherwise a wrong belief ("he knows what a worktree is") silently degrades every brief. This also matches the earlier equerry rule that the principal model is inspectable [E3].
- **Expertise reversal.** The expertise reversal effect from cognitive load theory: guidance that helps novices becomes redundant, and can hurt, for more knowledgeable learners (Kalyuga, Ayres, Chandler and Sweller 2003; author order varies by index) [18]. Many demonstrations use subjective load ratings, which is a known weakness [18]. Fit: explaining a term the reader already knows is not neutral padding, it costs attention. This is the formal reason the lexicon should also suppress glosses, not only add them.

### 1.3 Audience adaptation in explainable AI

Miller's survey of social science for explainable AI argues that people want explanations that are contrastive (why this rather than that), selective (a few causes, not the full causal chain) and social (a conversation tailored to the listener's beliefs) [19]. I confirmed the paper's thesis and its publication (Artificial Intelligence 267, 2019, arXiv:1706.07269) [19]; the search tool did not return the passages on those three properties, so the three-word summary is from my recall of the paper and should be checked against it before it is quoted. The mapping to equerry is direct:

- Contrastive: "why option A rather than B" is the natural shape of a decision card, but it should contrast the options on consequences the reader cares about, not on the agent's internal features.
- Selective: choose the one or two causes that bear on the decision; the full chain goes in the agent layer.
- Social: treat the card as one turn in a conversation. Clark and Brennan's account of grounding says communicators must jointly establish that something was understood, and that the medium shapes the cost of doing so [20]. A phone card with a one-tap "unclear" is a cheap grounding channel; a spoken brief needs "say that again" and "why" as first-class replies.

Gricean maxims (quantity, relation, manner, quality) and Sperber and Wilson's relevance theory are the linguistic backdrop: relevance is graded, and an input is relevant to a person when processing it yields positive cognitive effects for them [21]. This is a theoretical justification for per-reader relevance rather than a method; I did not retrieve the formal statement of optimal relevance with its effort clause [21].

### 1.4 A per-reader vocabulary model and how to maintain it

Proposed minimum (the design is mine; the pieces are cited above):

- A glossary entry per term: the term, a one-line plain gloss, a link to the fuller explanation, and tags for domain.
- A reader-state row per (reader, term): `state` in {known, unknown, learning}, `confidence`, `evidence` (a list of dated events), `last_seen`.
- Evidence types, strongest first: the reader used the term correctly in their own message; the reader decided correctly on a card that depended on the term; the reader asked what it means (evidence for unknown); the reader tapped "I know this" or "explain this" (explicit, weaker per the illusion of explanatory depth [14]); the term appears in the reader's own past writing (seed).
- Update rules: a correct use moves toward known; a "what does X mean" resets to unknown; no sighting for N days lowers confidence (the forgetting that original knowledge tracing omits [15]); a gloss shown without a follow-up question moves to learning, not to known.
- Use at generation time: the writer agent receives the lexicon slice for the terms in the draft. Rule: an unknown term is replaced by its plain gloss or defined once inline; a known term is used without gloss; a term in learning is glossed in a few words. A linter checks the finished card the same way (deterministic, so it does not depend on the model remembering).
- Governance: the lexicon file is the reader's property, inspectable and editable [16], and seeded only from the reader's own material kept out of any public repository.

Caveat: reading level is not one-dimensional. A principal can be expert in the repository's architecture and a novice in the build tooling. That is the argument for an overlay over a single "expert/novice" switch, which is what the LLM-personalisation papers mostly implement (section 4).

## 2. Executive and intelligence briefing practice

What each tradition says, and what it implies for altitude (the level of the problem the reader is asked to engage with).

- **Completed staff work.** The doctrine, circulated in 1942 (attributions vary: one source credits a Deputy Provost Marshal General, another a 1943 Canadian version), says the staff officer must deliver a finished solution rather than questions; the test is whether the officer would sign the paper and stake their reputation on it; and a memorandum to the chief is not completed work, but writing the paper the chief would send onward is [1]. Altitude: the reader should be able to approve with a single signature. For equerry: a card that ends in "A or B?" fails the doctrine unless the choice is genuinely the principal's (a value or a risk appetite, not a technical fact), and then the card must say why it is theirs.
- **BLUF and the Minto pyramid.** Bottom line up front is the apex of Minto's pyramid with no sub-structure; the pyramid puts the answer on top, supported by grouped arguments, and SCQA (situation, complication, question, answer) frames the opening so the reader's problem is stated in terms they already accept [2][3]. I did not find the Army regulation text for BLUF in this search, so its military provenance is unverified here. Altitude: the situation line is where the problem is placed at the reader's level; the complication is the reason a decision is needed now.
- **Amazon narrative memos and PR/FAQ.** Amazon replaced slide decks with prose memos read silently at the start of meetings, and uses a PR/FAQ for new products (a short customer-facing press release plus an FAQ) [22]. Sources are secondary (book summaries, podcast pages); the origin story differs between accounts (a 2004 email in one, a 2003 episode in another) and the length guidelines come from a podcast excerpt, so treat details as approximate [22]. Altitude: the press-release-first discipline forces the author to state the outcome in the reader's terms before any mechanism. The FAQ is the layered-rationale half: objections are answered in the same document, off the main path.
- **The President's Daily Brief.** It began after 1961 (a predecessor, the President's Intelligence Checklist, was first delivered on 17 June 1961; origins also trace to a 1946 Truman summary per a secondary source) and was tailored in content and format to each president and revised by their feedback; the Obama-era format added tablet delivery with links to supporting documents [23]. Altitude: the tailoring is per person and feedback-driven, which is the per-reader model of section 1 done by human briefers, with the supporting layer one link away.
- **Decision records.** Nygard's architecture decision record keeps status, context, decision and consequences; MADR adds drivers and the options considered [24]. These are agent-and-future-reader formats; they do not state the ask in the reader's terms. They are the right shape for the agent layer of a card, not for the principal layer.

What this says about framing the problem at the right altitude, as a synthesis (mine, labelled as such):

1. State the decision in the reader's domain, by what changes for them (cost, time, risk, reversibility), not by the mechanism.
2. Separate "I need your judgement" (values, risk appetite, external facts only they have) from "I need your approval" (the agent could have decided within delegated rights) and from "FYI". The first deserves attention, the second a one-tap default, the third no card.
3. If the same kind of question will recur, ask the policy question once: "what should I do whenever X?" is the meta-problem; "A or B this time?" is the instance. This links to decision rights in the principal-model report [E3].
4. Always name what would change the recommendation. It is both honesty and the cheapest guard against persuasive framing (section 4).

## 3. Layered disclosure and multiple renderings from one source

### 3.1 The record

One record per item that needs the principal, written once by the agent, with the structure below (a proposal; it extends the brief schema in [E1], it does not replace it).

```
principal:                      # shown by default; vocabulary-filtered per reader
  ask: one line, the thing to decide, or "none, FYI"
  situation: the problem in the reader's terms (SCQA situation + complication)
  recommendation: what I would do, and the one reason that matters
  options: [{label, what_you_would_see, cost_time_risk, reversible}]   # consequences, not mechanisms
  change_my_mind: what evidence would flip this
  default_if_silent: what happens and when
  standing_rule_offer: the policy that would stop this question recurring (optional)
agent:                          # not shown by default; always retrievable
  rationale: full reasoning, citations, links to issues and diffs
  alternatives_rejected: with reasons
  assumptions: [..], confidence: {...}
  terms_used: [term ids]        # for the lexicon linter
render_hints: {voice_order: [...], phone_max_words: ...}
```

Why the agent layer sits in the same record: separate documents drift, and the principal-facing text then becomes the only surviving explanation. The progressive-disclosure behaviour (summary, then "why?", then full) is a view over one record, not three artifacts.

### 3.2 Renderings

Single-source publishing is the established practice: DITA is designed so one set of content produces several deliverable formats, with reuse by reference (conref) so a fix at the source propagates everywhere, and conditional profiling to serve different audiences [7]. The equerry analogue: the record is the source; a renderer per medium is an adapter; the reader's lexicon is a profile applied at render time. Renderers needed:

- Phone text: `principal` block only, lexicon-filtered, within a word budget, with a "why?" affordance that opens the agent layer.
- Spoken script: the same fields in a fixed spoken order (ask first, then recommendation, options as consequences, then the default), numbers and identifiers rewritten for the ear. The earlier voice report covers the TTS and turn-taking side [E2]; the contract here is that the script is generated from the record, never separately written.
- Audio overview input: the full record (both layers) as source material for a generated multi-voice overview, for a reader who wants the reasoning while away from a screen. Whether a given overview product respects a per-reader vocabulary cannot be verified here; assume it does not and pre-filter.
- Slides or an infographic: for a decision that will be shown to a third party; same fields, different layout. Out of scope for a first version.

Consistency rule: every rendering must carry the same ask, the same recommendation and the same numbers. This can be checked mechanically by comparing the extracted fields, and by an independent checker agent for faithfulness (section 4).

## 4. LLM-specific evidence and risks

### 4.1 Tailoring to expertise

Several recent works adapt explanations to stated or inferred expertise:

- ExPerT personalises answers to domain expertise from query semantics and keystroke cues, and its examples contrast novice, intermediate and expert answers [25]. Mind the XAI Gap proposes an LLM framework for expert and non-expert explanations and reports a user study with N=56 judging non-expert interpretability [26]. A TU Delft thesis (repository file) reports parameter-efficient fine-tuning for expert versus non-expert audiences, with N=17 roboticists rating personalised answers more suitable and clearer [27]. These were found through search summaries and I read only abstracts or excerpts; sample sizes are small and I did not verify publication venues. Treat the direction (adapting helps ratings and comprehension in small studies) as plausible, the magnitude as unknown.
- Gap against equerry's need: these systems adapt to a coarse level, not to a term-level overlay with provenance, and none evaluates against decision quality for a time-poor reader. I found no study of that setting; that absence is itself a finding (not proof of absence, only that this search did not surface one).

### 4.2 Readability control and plain-language rewriting

Controllable simplification to a target reading level exists (for example instruction tuning with control tokens) [28]. The consistent warning in the literature surfaced: information loss and hallucination during simplification. InfoLossQA found that information loss occurs frequently in LLM simplifications of medical abstracts and that models struggle to detect it as expert humans do [8]. Standard overlap metrics (SARI, BLEU, ROUGE) measure surface similarity, not faithfulness; proposed alternatives are entailment checks and LLM judges, and reference-free checks for lost negations, altered numbers and dropped conditions [28]. Implication: simplification of a decision card is the riskiest step precisely because what drops is often the condition that makes the recommendation safe. Use a checker that compares the short rendering to the full record for dropped trade-offs, negations, numbers and deadlines.

Note a design choice that sidesteps part of the risk: do not simplify a finished technical text; have the agent write the principal layer from the structured facts directly, in the reader's vocabulary, so "simplification" is not a lossy second pass. The agent layer remains the lossless copy.

### 4.3 Persuasive framing and reliance

- Explanations increase reliance, including on wrong answers. In a pre-registered experiment (N=308), explanations increased reliance on correct and incorrect responses; sources and visible inconsistencies reduced reliance on incorrect ones [9]. In a fact-checking study cited in the same search, an incorrect explanation dropped human accuracy to 35%, below the 49% no-AI baseline (the original paper was not read; reported via a search summary) [9]. LLM explanations can be adversarially helpful: reframing, elevated confidence and cherry-picked evidence supporting wrong answers [29]. A 2026 study (n = 205, a preprint) reports users trust adversarial and benign explanations almost equally, with the most effective framings resembling expert communication [30]. Displaying confidence calibrates trust but, in one cited study, did not improve decision accuracy [29][30] (secondary reports).
- Sycophancy. Five assistants showed consistent sycophancy across tasks; both humans and preference models sometimes prefer convincingly written sycophantic responses to correct ones [10]. Personalisation to a user raises it: long-context user memory increased agreement sycophancy in several models (reported rises of 16 to 45 percent depending on model), and another study found inference-time personalisation increased inappropriate agreement across 14 models; results vary by model, and some models showed no increase [11]. One figure of 61.7 percent comes only from a news summary and should not be relied on [11].

Implications for equerry, as design requirements (mine):
1. A recommendation is an agent's advocacy. The card must present the strongest alternative honestly, state what would change the recommendation, and keep the agent layer one tap away; sources and visible uncertainty reduce over-reliance [9].
2. Vocabulary adaptation must not become opinion adaptation. The lexicon changes words, not conclusions. The writer receives the lexicon (terms), not the principal's preferences or views, in the same call that drafts the recommendation; the principal-preference model [E3] feeds a separate reviewer, so the drafting call has less to mirror [11].
3. An independent reader (the checker or contradictor in the existing team design) tests every card on a sample: what did the short version leave out, and does the recommendation survive its own "change my mind" line.
4. "Oversimplification that hides the real trade-off" is the named failure: test it by asking whether the cheapest option in the card is cheap in all the cost dimensions listed in the agent layer.

## 5. Measuring that a communication worked

Mostly estimates and proposals; the research base for this specific setting is thin and I did not find a validated metric set for agent-to-principal decision briefs.

- **Comprehension (teach-back).** Teach-back (the person restates what they understood in their own words) is a recommended comprehension check in clinical communication; a systematic review of 20 studies (mostly moderate quality) found it effective in 19, across knowledge recall and some objective outcomes, with heterogeneity and weaker results for low literacy [12]. Adapted: sample a few cards per week and ask, in a single line, "what are you choosing and what does it cost you?"; compare with the record. Cost to the principal is the limiting factor, so sample, do not census.
- **Behavioural proxies** (all cheap and already loggable; thresholds are not validated, set them from a baseline week):
  - time from card delivered to decision;
  - clarification questions per card, especially "what does X mean" (a direct jargon-miss signal that also feeds the lexicon);
  - requests to expand to the agent layer (a mixed signal: curiosity or insufficient trust);
  - reversals within a window after decision, and decisions the principal later regards as wrong (decision quality, lagged);
  - defaults taken without opening (can mean trust or neglect, so pair with spot-checks);
  - lexicon-linter violations per card.
- **Faithfulness.** Checker pass rate for the short rendering against the full record (dropped trade-offs, negations, numbers, dates) [8][28].
- **Calibration of the recommendation.** Over time: how often the principal agrees, and whether agreement tracks the stated confidence. A rising agreement rate alone could mean better cards or more sycophancy [10][11]; track it with the change-my-mind line and with the checker's disagreement rate.
- **Caveat on subjective load ratings.** Several expertise-reversal results rely on self-rated load, which is contested [18]. Prefer behavioural signals.

## 6. Standard terminology and things not asked

Terms to search on: curse of knowledge; common ground and grounding; audience design; learner/user model; overlay model; stereotype model; knowledge tracing; open or scrutable learner model; adaptive hypermedia; expertise reversal effect; cognitive load theory; illusion of explanatory depth; contrastive, selective, social explanation; BLUF; Minto pyramid; SCQA; completed staff work; decision memo; ADR and MADR; PR/FAQ; teach-back; single-source publishing, conditional publishing; text simplification, readability control; appropriate reliance and over-reliance; sycophancy; personalisation-induced sycophancy; faithfulness (of a summary to its source).

Not asked but important:

1. **Trust and the authority of the short version.** If the principal only ever reads the principal layer, the agent's framing is the only framing. The independent checker and the standing "change my mind" line are what make the layer safe to rely on [9][29].
2. **Principal's learning is a goal too.** A lexicon that only suppresses unknown terms freezes the principal's vocabulary. Occasionally introducing a term the principal will need (a "learning" state) is part of the design, and the principal should be able to opt in. This is a choice for the principal, not a default.
3. **Privacy of the model.** The lexicon and the evidence (their past writing, their questions) are personal data about the principal. Keep it in the private data area, never in this public repository; a public repository can hold the schema and the linter only.
4. **Altitude is per decision, and sometimes the right altitude is "none".** Many cards should not exist: delegation or a standing rule removes them. That is the highest-leverage form of "communicating decisions": not communicating the ones the principal should not have to make [E3].
5. **Voice changes the budget.** Listening cannot skim, cannot revisit a table and holds fewer items; the same record needs a different order and fewer options [E2]. Lexicon filtering matters more by ear because a gloss costs real time.
6. **Provenance when information, not a decision, is reported.** For FYI items the analogue of the recommendation is a confidence statement and a pointer to the evidence (a source and a date), in the same layered record.

## 7. Approaches to pilot (each about a week)

1. **Lexicon v0 and a jargon linter.** Build the reader-state file (term, state, evidence, last_seen) seeded from the principal's own earlier writing (kept private), plus a deterministic linter that flags unknown terms in a draft card and proposes the plain gloss; the writer agent receives the lexicon slice. Update the file from two signals only: "what does X mean" and the principal using the term. How to tell: clarification questions per card and linter violations should fall week over week; compare to a baseline week (estimate: a visible drop within the week is plausible if the baseline has many; the size is unknown). Failure signal: the principal edits the file often to correct wrong "known" entries, meaning the seeding is unreliable.
2. **Decision card v0 with the agent layer in the same record.** Implement the record in section 3.1 for the three most common decision types, render to phone text only, and alternate cards between old format and new for the week. How to tell: median time to decide and clarification questions per card, new versus old; plus the principal's choice of "expand" rate. Failure signal: more expansions and no faster decisions.
3. **Policy-question rewrite.** Before sending any A-or-B card, the writing agent must check the last N cards for the same question class and, if one exists, add a `standing_rule_offer`. How to tell: count of repeated question classes per week, and acceptance rate of offered rules. Failure signal: rules accepted then overridden often (a bad rule is worse than a repeated question; reversals within a window are the check).
4. **Faithfulness checker plus teach-back sample.** An independent checker agent (the existing checker or contradictor) gets the full record and the phone rendering and lists dropped trade-offs, negations, numbers and deadlines; separately, once or twice a week, one card gets a one-line teach-back question. How to tell: checker fail rate falls after the writer sees its failures; teach-back answers match the record. Failure signal: the checker passes cards the principal later says were misleading (the checker is not independent enough; try a different model or reader prompt).
5. **One record, three renderings with a consistency test.** Generate the phone text, a spoken script and an audio-overview source from the same record, and test mechanically that ask, recommendation, numbers and deadline are identical across them. How to tell: discrepancy count (target zero), and whether the principal uses the spoken version for cards that would otherwise be deferred (time to decision for deferred cards). Failure signal: the spoken script needs hand-editing, meaning the record is missing a field the ear needs.

Suggested order: 2 then 1 (the record gives the lexicon something to filter), then 4, then 3 and 5.

## REFERENCES

Internal (equerry and fleet reports, not repeated here):

[E1] equerry, docs/research/briefs-and-attention-triage.md (brief schema, ranking, evaluation metrics).
[E2] equerry, docs/research/voice-as-a-layer.md (voice contract, spoken briefs).
[E3] equerry, docs/research/principal-model-and-review-organisation.md (principal model, decision rights, delegation).
[E4] g_ai, docs/research/2026-11-14-attention-and-dispatch/r1-attention-ux.md (attention UX patterns).

External:

[1] [The Doctrine of Completed Staff Work (govleaders.org; attribution to a 1942 US Army memo disputed, see text)](https://govleaders.org/completed-staff-work.php)
[2] [Pyramid Principle (thinkinsights.net, summary of Minto)](https://thinkinsights.net/strategy/pyramid-principle)
[3] [SCQA Framework (managementconsulted.com)](https://managementconsulted.com/scqa-framework/)
[4] [Brusilovsky P. Methods and techniques of adaptive hypermedia. User Modeling and User-Adapted Interaction, 1996](https://sites.pitt.edu/~peterb/papers/UMUAI96.pdf); see also [Brusilovsky P. Adaptive hypermedia. UMUAI 11(1-2), 2001](https://link.springer.com/content/pdf/10.1023/A:1011143116306.pdf)
[5] [Bull S, Kay J. Open learner models (smili framework), IJAIED 2007; cited in secondary listing, not read](https://api.crossref.org/works/10.1007%2FS11257-017-9193-2) (a related 2017 UMUAI record; the 2007 paper itself was not located, so this reference is weak)
[6] [Kay J. Scrutability for personalised interfaces. ERCIM News 46, 2001](https://ercim.eu/publication/Ercim_News/enw46/kay.html)
[7] [OASIS DITA 1.3: producing different deliverables from a single source](https://docs.oasis-open.org/dita/dita/v1.3/os/part1-base/archSpec/base/single-sourcing.html)
[8] [Trienes et al. InfoLossQA: characterizing and recovering information loss in text simplification (arXiv:2401.16475)](https://arxiv.org/pdf/2401.16475)
[9] [Kim et al. Fostering appropriate reliance on large language models: the role of explanations, sources and inconsistencies (arXiv listing; the 35 percent versus 49 percent fact-checking figure is a secondary report of a separate NAACL 2023 study)](https://aitopics.org/doc/arxivorg:6FE48E18)
[10] [Sharma M et al. Towards understanding sycophancy in language models (arXiv:2310.13548)](https://arxiv.org/pdf/2310.13548)
[11] [Interaction context often increases sycophancy in LLMs (arXiv:2509.12517)](https://arxiv.org/html/2509.12517v3)
[12] [Talevski J et al. Teach-back: a systematic review of implementation and impacts. PLoS ONE, 2020](https://doaj.org/article/b1abc68239814afcb1efb196ce30aba8)
[13] [Curse of knowledge (Wikipedia, citing Camerer, Loewenstein and Weber 1989 and Newton 1990; originals not read)](https://en.wikipedia.org/wiki/Curse_of_knowledge)
[14] [Illusion of explanatory depth (Wikipedia, citing Rozenblit and Keil 2002; original not read)](https://en.wikipedia.org/wiki/Illusion_of_explanatory_depth)
[15] [Corbett AT, Anderson JR. Knowledge tracing: modeling the acquisition of procedural knowledge. UMUAI 4, 1995](https://dblp.dagstuhl.de/rec/journals/umuai/CorbettA95.html)
[16] Kay J, as [6]; scrutability described as the ability to explain what the system believes about the user and why.
[17] [Kay J. Scrutable adaptation: because we can and must. AH 2006, LNCS 4018 (bibliographic record only)](https://researchr.org/publication/Kay06)
[18] [Kalyuga S, Ayres P, Chandler P, Sweller J. The expertise reversal effect. Educational Psychologist 38(1), 2003](https://ro.uow.edu.au/edupapers/136)
[19] [Miller T. Explanation in artificial intelligence: insights from the social sciences. Artificial Intelligence 267, 2019 (arXiv:1706.07269)](https://arxiv.org/abs/1706.07269)
[20] [Clark HH, Brennan SE. Grounding in communication, 1991](https://web.stanford.edu/%7Eclark/1990s/Clark,%20H.H.%20_%20Brennan,%20S.E.%20_Grounding%20in%20communication_%201991.pdf)
[21] [Sperber D, Wilson D. Relevance theory (UCL working papers)](https://www.phon.ucl.ac.uk/publications/WPL/05papers/sperber_wilson.pdf)
[22] [Commoncog. Working Backwards (secondary summary of Bryar and Carr; narrative memos and PR/FAQ)](https://commoncog.com/working-backwards/)
[23] [CIA. President's Daily Brief: delivering intelligence to Nixon and Ford](https://www.cia.gov/static/Presidents-Daily-Brief-Delivering-Intelligence-to-Nixon-and-Ford.pdf)
[24] [Fowler M. Architecture Decision Record (credits Nygard 2011; MADR described in secondary sources)](https://www.martinfowler.com/bliki/ArchitectureDecisionRecord.html)
[25] [ExPerT: personalizing LLM responses to users' domain expertise (arXiv:2607.01242)](https://arxiv.org/pdf/2607.01242)
[26] [Mind the XAI Gap: a human-centered LLM framework for democratizing explainable AI (arXiv:2506.12240)](https://arxiv.org/html/2506.12240v1)
[27] [TU Delft repository: tailoring LLM explanations to expert and non-expert audiences (thesis file; title and venue not verified)](https://repository.tudelft.nl/file/File_1e0e80b3-0e7b-4d81-bc1b-befc505f7c67?preview=1)
[28] [Lexical and controllable simplification: Taming CATS (arXiv:2604.01779)](https://arxiv.org/pdf/2604.01779); and [CLEF SimpleText working note on faithfulness checks](https://ceur-ws.org/Vol-4038/paper_355.pdf) (not individually read; claims about metrics and reference-free checks are from search summaries)
[29] [Ajwani et al. LLM-generated black-box explanations can be adversarially helpful (NeurIPS 2024 workshop)](https://mlanthology.org/neuripsw/2024/ajwani2024neuripsw-llmgenerated)
[30] [Evaluating the false trust engendered by LLM explanations (arXiv:2605.10930, preprint)](https://arxiv.org/pdf/2605.10930)

Verification status: all citations were located through web search on 2026-11-18; most were read only as abstracts or search excerpts, and the items called out above (Camerer 1989, Newton 1990, Rozenblit and Keil 2002, Miller's three properties, the Army BLUF text, the Amazon details, Bull and Kay 2007) were not checked against originals.
