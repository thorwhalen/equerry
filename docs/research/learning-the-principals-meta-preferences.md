# Learning one person's decision-making model: meta-preferences, principles from cases, scrutable models, elicitation and evaluation

Research report, standard depth, 2026-11-18. Theme 4 of the principal-model study for equerry. Design question only: no private material was used. It builds on, and does not repeat, the earlier report `docs/research/principal-model-and-review-organisation.md` (digital-twin accuracy limits, PRELUDE/CIPHER learning from edits, the veto-predictor gate, recalibration on overrides) and `docs/architecture.md` (the learning loop). How sources were checked: each reference below was found through a web search that returned its title, authors and abstract or a summary of it; full texts were mostly not read, and where a claim rests on a summary only, it says so. Estimates are labelled as estimates.

## Summary

**Main finding.** The cheapest large gain over "store the person's corrections as notes" is to model **what the person trades off, not only what they chose**: a small set of named trade-off axes (speed against safety, privacy and legality against velocity, effort against polish, and so on) with a weight, a hard floor where the person treats the axis as non-negotiable, and a link to the cases that justify it. That is the structure used by hierarchical Bayesian choice models [4], multi-objective preference learning [5][6] and inverse reward design [3], and it is also the shape in which a person can read and correct their own model [19][20]. Principles induced by an LLM from cases are good hypotheses, not facts: they must be shown to the person with their supporting cases, their counter-cases and a held-out test before they enter the record [9][2]. The record should be a small, versioned, scrutable document whose lines each carry provenance, a confidence and a last-confirmed date, because the deployed memory features of 2024 to 2026 show the same failure modes again and again: stale entries, over-application, and sycophancy amplified by remembered profiles [21][23][26].

**Three things the earlier report did not settle, now settled enough to build on.**

1. **Rules before models.** For a decision-maker who will read and edit the model, an interpretable structure (ordered rules with exceptions, weights on named axes, a precedent library) is preferable to a fitted black box when stakes are high and an interpretable model performs about as well [17]. This is a published argument, not a proof for this setting; equerry should test it on its own override log (approach 1 below).
2. **Ask the person, selectively.** Active elicitation can reach a given certainty with far fewer labels than passive logging [28][29][30], and LLM-driven open-ended elicitation produced responses more informative than user-written prompts or labels in preregistered studies [27]. The interview is a strong, cheap signal in the earlier report's sources too [34].
3. **Widening autonomy needs a measured, per-class, drift-aware record.** "Good enough" is a calibrated predictive score on held-out real decisions, in a decision class, over a window that survives a drift test [32][33]; there is no published threshold for coding-agent delegation, so equerry must set its own (see Evaluation).

**Biggest risks.** Treating revealed choices as the person's values when the person's own second-order view disagrees (Frankfurt) [1][7]; extracting a principle that fits the cases but that the person rejects on sight; a memory that remembers too much and flatters (memory profiles produced the largest sycophancy increases in one two-week study [23]); and a model that quietly changes the person it models (preference shift induced by the system [8]).

## 1. Hierarchical and meta-preferences

### 1.1 The concepts

**First-order and second-order preferences.** Frankfurt's 1971 paper separates first-order desires (wanting X) from second-order desires (wanting to want X) and second-order volitions (wanting a desire to be the one that moves you to act); he treats having second-order volitions as the mark of a person as opposed to a "wanton" [1]. For equerry the practical reading is: the person's choices (what they approved last Tuesday, in a hurry) are first-order evidence, and their stated principles ("I never ship without tests", "I would rather be slow than leak data") are second-order statements about which first-order tendencies they endorse. When the two conflict, the stated principle is not automatically right, but the conflict is the most informative thing the system can surface. The unresolved philosophical critiques (are second-order desires special; do third-order desires regress) [1] do not matter for the design; the two-level structure is enough.

**Preferences are not the whole of values.** Zhi-Xuan, Carroll, Franklin and Ashton argue that "preferences" as the target of alignment omit much of what makes up values, and that preference satisfaction is not the right account of rationality or of what an assistant should serve [7]. Their positive proposal (normative standards suited to an assistant's social role, negotiated with stakeholders) points at something equerry can use directly: the principal-agent relation has a role, and "what would my principal want, given the role I hold for them" is a more stable target than "what did they click".

**Preferences change, and the system can change them.** Carroll et al. formalise that a recommender's output shifts what users want, and propose estimating "safe shifts" as a trust region [8]. A representative that nudges the principal's decisions through its annotations can drift the record away from the principal's reflective view. This is a risk for equerry because the annotation "the model thinks you may disagree because Y" is itself a nudge. A cheap mitigation is to log whether the principal saw the prediction before deciding, and to evaluate calibration only on decisions made before the prediction was shown (a holdout, see Evaluation).

### 1.2 What a trade-off model looks like

Value hierarchy as a data structure (a design proposal assembled from the sources below, not one published model):

| Layer | Content | Example (generic) | Learned from |
|---|---|---|---|
| Axes | named trade-offs, each a pair of goods | speed against safety | stated principles, interview |
| Weights | a distribution over the axis weight per decision class | in the "ship a release" class, safety outweighs speed by about 3 to 1 (illustrative) | pairwise choices |
| Floors (constraints) | axes where the person refuses any trade | no secret ever in a public repo | statements; every override that cites it |
| Priority between axes | which axis wins in a conflict | legality above privacy above velocity | explicit orderings, conflicts in history |
| Exceptions | contexts that flip a weight | throwaway prototypes drop the test requirement | counter-cases |
| Provenance | the cases and statements behind each line | links | the log |

The split between floors and weights matters: a floor is lexicographic (never traded), a weight is compensatory. Fitting both into one linear utility is a known mistake; treat floors as hard filters applied before scoring and keep weights for what remains. I did not find a source that tests this split on delegation data; it is a modelling choice to validate (approach 1).

### 1.3 Computational models, in order of fit

**Hierarchical Bayesian choice models.** In marketing science, conjoint and discrete-choice models give each respondent their own trade-off weights ("partworths") drawn from a population distribution, so a person's weights are estimated by borrowing strength from the population and shrinking towards it when their own data are thin [4]. The cited results show this works creditably even with more parameters than observations per individual [4] (reported in a secondary summary of Lenk et al. and Allenby et al.; I did not read the originals). For equerry the "population" is not other people but the person's own other decision classes: the weight on safety in "ship a release" is shrunk towards their general weight on safety, so a class with five observations still gets a sensible estimate with honest uncertainty. This is the right small-data structure and needs only a few hundred decisions.

**Learning trade-off weights from pairwise choices.** The standard form is Bradley-Terry: each option has a score that is a weighted sum of its attributes, and the probability of choosing A over B is a logistic function of the score difference. Applied to multi-objective optimisation, one recent paper learns linear objective weights by maximising Bradley-Terry likelihood from pairwise comparisons and uses active learning to choose which pairs to ask about [5] (I know the paper only from a search summary; its exact title is not confirmed). Another uses the Plackett-Luce generalisation, with an inverse-temperature parameter for noisy feedback, to learn Pareto-optimal rewards from noisy preferences [6]. A caution from the same literature: what preference learning recovers from pairwise data depends on assumptions about noise and invariances [see the results listed under 6; I did not open the paper whose title suggests this, so no claim is made].

**Inverse reward design (IRD).** The decision-maker's stated rules are evidence about their intent, not the intent itself: a rule written for the situations the person had in mind will be misspecified in new ones, and the system should infer the true objective from the stated one plus the context it was written for [3]. This is the formal version of "a principle stated in one context should be held with uncertainty in a new one", and it is the right treatment of CLAUDE.md-style rule lines. Practically: each rule in the record carries the contexts it was stated in; a decision in a context far from them gets a wider uncertainty band, and that, not the rule's wording, drives the "ask the person" decision.

**Mixture and personalised reward models.** In LLM alignment, standard RLHF assumes preferences come from one distribution; personalised variants learn a user representation jointly with the model [31]. This is relevant if equerry ever fine-tunes a classifier on the override log; for v1 the hierarchical Bayesian structure above is simpler and inspectable.

### 1.4 Terminology (see also section 8)

Meta-preference, second-order preference, preference over preferences, value hierarchy, lexicographic preference, compensatory against non-compensatory choice, partworth, Bradley-Terry, Plackett-Luce, inverse reward design, revealed against stated preference, preference shift.

## 2. Learning principles from cases

### 2.1 What already exists

**Inverse Constitutional AI (ICAI).** Treats pairwise preference data as a compression problem: find a short list of natural-language principles from which an LLM annotator can reconstruct the original judgments, and measure how well it does. It was tested on synthetic data with known principles, annotated data and Chatbot Arena data; code is public [9]. This is the closest published recipe for "extract the principles behind a person's decisions", and its evaluation (reconstruction of held-out judgments) is the one equerry should reuse.

**Learning from language feedback.** Imitation learning from language feedback turns a written correction into improved outputs through refine, select, fine-tune [10]; the PRELUDE/CIPHER line (in the earlier report) infers a latent preference from edits. Both treat feedback as training signal for an output, which is a different goal from producing an inspectable principle.

**Rubric and criteria induction.** A cluster of 2024 to 2026 work extracts reusable natural-language criteria from feedback: mining user feedback in real conversations into checklist-style preference summaries [12]; contrastive rubric generation from chosen and rejected pairs [search summary of OpenRubrics, no reference opened]; an iterative propose-evaluate-revise loop that tests each rubric's discriminative power [13]; and learning reusable rubrics from inline comments on drafts while keeping each criterion grounded in the local comment [11]. The last is the closest to equerry's case (comments on agents' work), and its stated difficulty is the one equerry will hit: grounded locally, yet general enough to apply elsewhere. I read these only through abstracts or summaries.

**Closed loops in coding agents.** A 2026 preprint proposes converting point corrections from review into class-level constraints in a version-controlled instruction file, and feeding only accepted review outcomes into it, changing no model weights [18]. It is self-reported and I did not verify its evaluation. Its design choice (only accepted outcomes become rules; the rules file is the model) agrees with the architecture's "reviewed consolidation".

### 2.2 Classical alternatives: rule induction and cases

**Rule induction.** Algorithms such as RIPPER learn ordered if-then rules with pruning, are fast, and produce a model a person can read [16]. Rudin's argument is that for high-stakes decisions one should prefer a model that is interpretable from the start to explaining a black box, when accuracy is comparable [17]. For equerry the features would be decision attributes (reversibility, spend, public or private, touches a person, concern) and the label approve, modify, reject. With a few hundred labelled decisions this is feasible; its limit is that the attributes must be extracted from free-text decisions first (an LLM step that adds noise).

**Case-based reasoning and precedent.** CBR solves a new problem by retrieving similar past cases, reusing their solution, revising it, and retaining the result [14] (the four-step cycle is described in the paper's overview, which I could not open in full). Legal precedent systems are the instructive variant: HYPO compares a new fact situation with cases along "dimensions" (graded factors, not yes or no), and reports "near misses" where a dimension almost applies, and the best cases for and against [15]. Two transfers to equerry: (a) the representative should predict by citing the nearest decided cases along named dimensions, which is more checkable than a free-text rationale; (b) near-misses are exactly the "items shown first teach the record most" that the architecture already says. CBR also gives a natural answer to supersession: a newer case on the same dimensions with a stated reason overrides an older one explicitly, rather than silently outvoting it.

**Recommendation.** Use all three layers as a stack, each with a defined job: precedent library (source of truth, every decision with its dimensions and the person's stated reason when given); induced rules and principles (compressed, human-readable, each linking to its supporting and counter cases); trade-off weights (for ranking within what the rules leave open). A prediction is made by the top layer that has direct coverage, and says which layer it used.

### 2.3 Presenting an induced principle for confirmation (reflective equilibrium)

Reflective equilibrium (Rawls, building on Goodman) is the method of adjusting general principles and judgments about particular cases against each other until they cohere; in the "wide" form one also considers alternative principles and the arguments for them [2]. It maps onto the confirmation step exactly: the person's case judgments are fixed points, the principle is a candidate, and either can move. A confirmation card should therefore contain (this is a design derived from [2] and [9], not a published UI):

1. The candidate principle in one sentence, in the person's likely wording.
2. Three to five supporting cases, with dates, as links.
3. The strongest counter-cases (decisions that appear to break it), because the person's reaction to those is the signal.
4. A rival principle that fits the same cases equally well (the wide-equilibrium move [2]), so the person chooses between hypotheses, not accepts one.
5. A held-out probe: "if X happened tomorrow, would this principle say approve? Is that right?" (one or two synthetic cases near the boundary).
6. Four responses: confirm, confirm with an exception (which opens a field), reject (with a reason, stored as a counter-principle so it is not re-proposed), and not sure (marks the line tentative).

Cap the daily number of cards; a review burden above what the person tolerates is the usual way this loop dies (an estimate from the general automation-bias literature cited in the earlier report; no equerry-specific data).

## 3. Personalisation in deployed assistants, 2024 to 2026

**What exists.** ChatGPT keeps saved memories and, in its newer design, a memory summary the user can review and correct; OpenAI's own help text says the older design often went stale and relied on users to keep it updated, and lists controls: flag an item as incorrect or outdated, edit or delete items, refresh the summary, and (on some plans) search, sort, automatic prioritisation and version history [21]. Deleting the chat does not delete the saved memory; deleted memories may be retained up to 30 days for safety and debugging [21]. Availability varies by plan and platform, and I could not confirm the current control set. Claude Code has two stores that load at session start: a user-written `CLAUDE.md` hierarchy (global, project, local) and an auto-written per-project memory with an index and topic files, with a command to browse and toggle them; details (such as the 200-line index limit) come mostly from third-party guides, so verify against the official page [22]. Codex-style agents keep a separate memory store that "may graduate to rules after human review" according to a practitioner write-up; I have not seen the primary source.

**What went wrong (evidence, strongest first).**

- **Sycophancy amplified by memory.** In a two-week study with 38 participants, agreement sycophancy generally rose when user context was present, with memory profiles giving the largest increases (in one version of the paper: about 45 percent for one model, 33 percent for another, 16 percent for a third, none significant for a fourth; figures differ between paper versions, so check the version) [23]. The result is for conversational assistants, not agents, but equerry's representative is exactly a remembering model asked to predict approval, so the risk is direct.
- **A sycophancy incident at deployment scale.** OpenAI rolled back a model update in April 2025 after it became noticeably more sycophantic, and said sycophancy had not been flagged in internal hands-on testing and would be added to deployment evaluations [24]. The relevance is the lesson (an evaluation that does not measure the failure will not catch it), not the cause.
- **Over-personalisation and misuse of memory.** Benchmarks formalise irrelevance, repetition and sycophancy as over-personalisation, and show agents retrieving and stressing stored memories when they are not needed [26]; another targets stored details surfacing in contexts where they do not belong and reports identity-validation cases as the most sycophantic [26]. A contextual-integrity benchmark finds privacy violations increase as more personal information accumulates [26]. All three are from preprints I read only as summaries.
- **Staleness and change.** PersonaMem, in which personas evolve through life events, reports frontier models near 50 percent overall and better on static facts than on applying the latest situation [25]; a 2026 critique argues that existing memory benchmarks test updates over at most two or three sessions [25]. So evidence on long-horizon preference change is thin.
- **Control exists but is shallow.** The user can delete or edit a memory [21], but the shipped controls act on facts ("lives in X"), not on a preference's scope, strength, exceptions or priority against other preferences. That is the gap the trade-off record above addresses.

**Implications for equerry.** (a) Scope every line (when it applies, when it stops). (b) Date every line and track last confirmation. (c) Keep the record out of the prompt of the agents that do the work, and give it only to the representative and the consolidation job, so a remembered profile cannot flatter the worker. (d) Test the representative for sycophancy directly: feed it the same item with and without the principal's last stated opinion and measure the prediction shift. (e) Prefer retrieval of the few relevant lines over a loaded profile.

## 4. Scrutable, editable user models

**The tradition.** A user model is "an individual, dynamic and explicit model the system holds about the user"; a model is scrutable if the user can inspect it, see how it is used to adapt, and change it. Kay's group built this into adaptive hypertext teaching (a working model and framework in 2000 to 2002) and the Personis user-model server, where users can see what is held, how it was gathered and how it is used [19] (read through summaries). The open-learner-model literature separates inspectable models (the learner can view) from models that also allow interactive maintenance, including negotiation between learner and system, and its SMILI framework describes the design space (how the model fits the interaction, whether it is always available, who controls availability, who else can access it); a 2016 revision found it was used more to define the field than for systematic comparison [20]. Scrutability's evidence base is from education and adaptive hypertext, not from delegation, so what transfers is the design vocabulary, not effect sizes.

**What a scrutable model needs (a synthesis, not one source).**

1. **Every line has a source and a way to ask "why".** The line links to the cases and statements it rests on (the Personis idea of showing how the model was gathered [19]).
2. **Every line is editable in place, and editing wins.** An edit is the strongest evidence there is; a person-edited line is marked and can only be superseded by the person, never by a consolidation job.
3. **A line says how sure it is and when it was last confirmed**, and shows its uncertainty visibly, so a stale line looks stale.
4. **Negotiation, not only editing.** The system can say "your last three decisions disagree with this line: keep, narrow, or retire?" (the interactive-maintenance end of the OLM scale [20]).
5. **The same format for the person and for the machine.**

**Formats that work (judgement, with reasons).** Plain Markdown or YAML with a fixed per-line schema (id, statement, scope, strength, floor or weight, supersedes, sources, status, last confirmed). Markdown because the person and the agents already read and edit it with the tools they have (the same reason `CLAUDE.md` works [22]); a fixed schema so a consolidation job can diff it and a linter can check it. A graph view is optional. A visual form is useful for the sliders (axis weights with uncertainty bands) but should be generated from, never the source of, the text. No source found compares formats for scrutable preference models; this is an engineering judgement.

## 5. Active elicitation

**Which question to ask.** Three families, in increasing machinery:

- **Information gain.** Choose the query whose answer most reduces uncertainty about the model parameters; BALD does this for Gaussian-process classification and preference learning, selecting queries by expected information gain about the parameters [29]. Sadigh et al. generate pairwise trajectory comparisons chosen to remove the most volume from the hypothesis space and report faster convergence than non-active or non-synthesised queries, with a user study [28].
- **Regret.** Minimax-regret elicitation asks only the queries that reduce the worst-case loss of the currently recommended decision under the remaining uncertainty; Boutilier and colleagues report that heuristic strategies find optimal or near-optimal decisions with far fewer queries than a theoretically motivated algorithm [30] (abstract-level reading). This criterion fits equerry better than parameter accuracy: it asks only what would change the gate's decision.
- **Language-model elicitation.** GATE has the model ask free-form questions or generate edge cases for the person to label; in preregistered studies on email validation, content recommendation and moral reasoning, it often produced more informative responses than user-written prompts or labels [27].

**Query types ranked by what they teach (judgement).** Pairwise trade-off dilemmas ("speed or safety here?") teach weights; boundary cases for a candidate principle teach exceptions; ordering questions between axes teach priority; open "what would make you override this?" teach unknown axes. For a coding-agent fleet, the most useful are dilemmas drawn from the person's own recent real items (synthetic variants that change one attribute), because they carry context and answer faster.

**Structured interviews.** The study behind the earlier report found that a two-hour interview gave the strongest simulations of a person [34]; I take that as evidence the interview is the cheapest strong signal. A short structured interview of 20 to 40 trade-off dilemmas plus an ordering exercise (an estimate: 30 to 60 minutes) can initialise the axes, floors and priorities before the override log is large enough, with the log then adjusting the weights. Re-run a short version on a schedule or when drift fires.

**Budget the asking.** Each question costs principal attention, the scarce resource. A rule of thumb (an estimate, not a source): ask only when the expected change in the gate decision is above the cost of the question; and prefer piggy-backing questions on items the principal is already reviewing, since the override is a free label.

## 6. Evaluation

**What to predict.** The primary label is the three-way outcome on real, held-out items (approve as proposed, approve with modification, reject or escalate), the secondary label is whether the principal would have wanted to see it at all (needs-approval against can-go). The second is the one the gate uses.

**Protocols.**
- **Temporal holdout, not random splits.** Train on decisions up to time T and score decisions after T; random splits leak preference changes and repeated near-duplicate items and overstate accuracy (a standard concern in time-ordered data; I did not find an equerry-specific source).
- **Shadow mode.** The representative predicts every item before the principal decides, with the prediction hidden until after the decision. This gives uncontaminated labels and avoids the self-induced shift in section 1.1 [8].
- **Baselines that must be beaten.** Always predict "approve"; predict the person's last decision in the same class; a rules-only model; the LLM with the raw log and no structure. A structured model that does not beat "same as last time in this class" has not learned anything.
- **Scores.** Proper scoring rules for probabilities (Brier or log loss), plus calibration. Modern networks are often miscalibrated, and temperature scaling is a simple one-parameter fix [33]; expected calibration error bins predictions and compares confidence with accuracy [33]. For equerry the most important numbers are the false-approve rate (the model said fine, the principal would have vetoed) in each decision class, with a confidence interval, because that is the error that costs.
- **Coverage.** Report the fraction of items where the model claims direct or adjacent coverage and its accuracy on that subset; selective prediction trades coverage for accuracy (learning to defer is in the earlier report).

**Drift.** Preferences change, and so does the work (new repos, new risks). Streaming change detectors such as ADWIN keep a variable-length window and shrink it when two sub-windows' means differ significantly, with proven bounds on false-positive and false-negative rates and a tolerance parameter; they have been paired with a predictor to monitor its error and trigger revision [32] (abstract-level). Apply it per decision class to the stream of "prediction wrong" indicators and to the override rate. When it fires, narrow autonomy in that class and send the recent cases to a confirmation card, rather than retraining silently.

**When is the model good enough to widen autonomy?** No published threshold exists for delegated coding-agent decisions, so these are proposals to adopt and then revise (labelled estimates):

1. A decision class qualifies only if it has at least about 30 shadow-mode decisions (an estimate; 30 gives a false-veto upper bound near 10 percent at zero observed vetoes, by the usual rule of three, so it is a floor, not a comfort).
2. Observed false-approve rate in the class below a tolerance the principal sets per class (tighter for one-way doors), with the upper confidence bound, not the point estimate, below tolerance.
3. Calibration in the class no worse than a stated limit, checked on a recent window.
4. No drift alarm in the last window.
5. The class is reversible by rule (the architecture's first gate condition).
6. Widening is one step (for example, from "show with annotation" to "log, show only a digest"), is announced to the principal, and automatically reverts on any alarm. This is the idea of graded autonomy levels from the earlier report's sources, applied per class.

## 7. Things not asked about that matter

- **Disagreement among the principal's own selves.** A person's answers vary with mood, time pressure and framing; the earlier report's test-retest figure already bounds accuracy. Practically: ask the same trade-off dilemma twice, weeks apart, and treat the disagreement rate as the noise ceiling for that axis. Axes with high self-disagreement should never auto-approve.
- **Normative role over raw preference.** [7] suggests the model should capture what the role requires (for example, an owner-operator accountable for security of public repos), which can override momentary preference. Record these as floors with a stated reason.
- **Privacy of the model itself.** A decision-model is sensitive personal data. Contextual-integrity results show leakage rises with accumulated personal information [26]. Keep the record in the data repository, never in public repositories, and expose to a given agent only the lines relevant to its decision.
- **Poisoning.** Anything that can write to the record (agents, tools reading web pages) is an injection route. Only the principal, or a consolidation job reviewed by the principal, may write. I did not research memory-poisoning attacks in this report.
- **Consolidation is where errors enter.** Every LLM extraction step (decision to attributes, case to principle) adds error that the held-out evaluation measures only end-to-end. Keep the pre-extraction raw case so the extraction can be re-run with a better model.

## 8. Standard terminology

| Term | Meaning in this context |
|---|---|
| Revealed against stated preference | what a person does against what they say; disagreement between them is data |
| Second-order preference / volition | a preference about one's preferences; the one that moves one to act [1] |
| Meta-preference | preference about how preferences are formed, weighed or changed |
| Value hierarchy, lexicographic preference | an ordering where a higher value is never traded for a lower one |
| Compensatory against non-compensatory | trade-offs allowed against refused |
| Partworth | the weight of one attribute in a person's utility, estimated per person [4] |
| Hierarchical Bayes / partial pooling | estimating individual weights shrunk to a population distribution [4] |
| Bradley-Terry, Plackett-Luce | choice models for pairwise and ranked comparisons [5][6] |
| Inverse reward design | inferring the true objective from a stated proxy and the context it was written for [3] |
| Preference elicitation | the field of choosing questions to learn a utility function [28][29][30] |
| Minimax regret | choose the decision whose worst-case loss under remaining uncertainty is smallest [30] |
| Constitution; inverse constitutional AI | a list of principles; inferring one from judgments [9] |
| Rubric / criteria induction | learning natural-language criteria from feedback [11][12][13] |
| Case-based reasoning, precedent, dimension, near miss | retrieving similar past decisions; graded factors; almost-applicable factors [14][15] |
| Reflective equilibrium (narrow, wide) | adjusting principles and case judgments to cohere; wide also tests rival principles [2] |
| Scrutable, inspectable, open learner model | user can see, and change, the model [19][20] |
| Selective prediction, abstention, learning to defer | predicting only when confident; see the earlier report |
| Calibration, ECE, proper scoring rule | agreement of stated confidence with outcomes; Brier or log loss [33] |
| Concept drift | change in the distribution or the labelling rule over time [32] |
| Induced preference shift | the system changes what the person wants [8] |
| Over-personalisation | irrelevant, repeated or sycophantic use of memory [26] |

## 9. Four approaches equerry could pilot, each in about a week

Each assumes a working log of the principal's decisions on agent items and corrections; if the log has under about 100 labelled items, run approach 3 first to create labels.

**Approach 1. Trade-off ledger with a three-way baseline.**
Build: from the existing log, hand-tag (or LLM-tag, with spot checks) each decision with its decision class and the axes in tension and the side chosen; fit three predictors on the earliest 70 percent: (a) same-as-last-time in the class, (b) per-class Bradley-Terry weights with partial pooling to a global weight per axis [4][5], with floors as hard filters, (c) an LLM given the record text and no structure.
Worked if: on the latest 30 percent, (b) beats (a) and (c) on Brier score and on false-approve rate, with the difference larger than the bootstrap interval; and the fitted weights and floors read as sensible to the principal (a yes or no after five minutes). Failed if (a) matches (b): then the record is better spent on precedent (approach 4).

**Approach 2. Principle induction with confirmation cards.**
Build: run an ICAI-style compression [9] over the last 100 to 200 corrections and overrides to propose at most 15 candidate principles; render each as the six-part card in section 2.3 [2]; the principal answers in one sitting (a budget of 20 minutes).
Worked if: (i) at least about half of the principles are confirmed or confirmed-with-exception (an estimate of a useful hit rate, not from a source); (ii) the confirmed set, used as a prompt, reconstructs held-out decisions better than the unreviewed set and better than no principles [9]; (iii) rejected principles are mostly ones with thin or one-sided support, which tells you what to filter next time. Failed if confirmation is below about a quarter: the extraction prompt needs the case-grounding fix from [11].

**Approach 3. A 40-minute adaptive trade-off interview.**
Build: a script that picks 20 to 40 dilemmas, built by changing one attribute of real recent items, selected greedily by disagreement between candidate weight settings (the BALD idea [29]) or by regret [30], plus an ordering exercise over axes and a set of "never" statements; record answers into the schema of section 4.
Worked if: weights and floors from the interview alone predict the held-out latest items at least as well as the same number of randomly chosen override cases, and better than the unstructured baseline; and two repeat answers a fortnight apart agree enough to define a noise ceiling per axis (record the rate). Compare adaptive against fixed question order on a second sitting to see whether the selection earns its complexity [28][29].

**Approach 4. A scrutable record with shadow-mode scoring and a drift alarm.**
Build: convert the current position lines into the line schema of section 4 (scope, strength, sources, supersedes, last confirmed, status), add a representative that must name the layer it used (precedent, principle, weight) and cite lines, run it in shadow mode for a week on every item, and add an ADWIN-style detector [32] on per-class wrong-prediction and override streams.
Worked if: (i) the principal can find, understand and edit any line in under a minute (timed on five lines); (ii) the cited lines are judged relevant on at least about 80 percent of a 30-item sample (an estimate); (iii) the sycophancy probe (same item with and without the principal's last stated view) shifts the predicted risk by less than the calibration noise [23]; (iv) the alarm fires on a deliberately injected preference change (the principal states a reversal and applies it to the next five items) within the week, and does not fire on the control class. Failed if the cited lines are often irrelevant: retrieval, not modelling, is the bottleneck, and the fix is better dimensions for the precedent library [15].

**Order.** 3 then 1 then 4 then 2 if labels are scarce (the interview creates the labels and the schema); 1 then 2 then 4 if the log is already large. Approach 4's shadow mode is the evaluation harness for all of the others and should start first even if it only logs.

## Open questions this report could not settle

- No source tests trade-off weights or Frankfurt-style two-level models on delegated software decisions; approaches 1 and 3 are the test.
- No source gives a threshold for "good enough to widen autonomy"; section 6 gives proposals only.
- The strength of the sycophancy effect for a predictor (as against a chat assistant) is unmeasured; approach 4 includes the probe.
- Several 2025 and 2026 preprints were read only as summaries; before citing a number from them elsewhere, open the paper.

## REFERENCES

1. Frankfurt HG. Freedom of the Will and the Concept of a Person. The Journal of Philosophy 68(1); 1971. [PDF copy](https://www.yorku.ca/dimock/documents/FrankfurtFreedomoftheWillandtheConceptofaPerson.pdf); summaries consulted: [Notre Dame lecture notes](https://www3.nd.edu/~jspeaks/courses/mcgill/201/frankfurt-free-will.html)
2. Stanford Encyclopedia of Philosophy. Reflective Equilibrium (Rawls; Goodman). [plato.stanford.edu](https://plato.stanford.edu/ENTRIES/reflective-equilibrium/)
3. Hadfield-Menell D, Milli S, Abbeel P, Russell S, Dragan A. Inverse Reward Design. NeurIPS 2017. [arXiv:1711.02827](https://arxiv.org/abs/1711.02827v2)
4. Allenby GM, Rossi PE. Hierarchical Bayes (chapter); with summaries of Lenk et al. 1996 and Allenby et al. 1998, and Andrews et al. on holdout prediction. [PDF](https://datajobs.com/data-science-repo/Hierarchical-Bayes-[Allenby-and-Rossi].pdf); [Sawtooth overview](https://sawtoothsoftware.com/help/lighthouse-studio/manual/what-is-cva-hb.html)
5. Preference elicitation for multi-objective combinatorial optimization with active learning (title as given in a search summary; not confirmed). [arXiv:2503.11435](https://arxiv.org/abs/2503.11435v2)
6. Learning Pareto-Optimal Rewards from Noisy Preferences: A Framework for Multi-Objective Inverse Reinforcement Learning. [arXiv:2505.11864](https://arxiv.org/pdf/2505.11864)
7. Zhi-Xuan T, Carroll M, Franklin M, Ashton H. Beyond Preferences in AI Alignment. Philosophical Studies; 2024. [arXiv:2408.16984](https://arxiv.org/html/2408.16984v1)
8. Carroll MD, Dragan A, Russell S, Hadfield-Menell D. Estimating and Penalizing Induced Preference Shifts in Recommender Systems. ICML 2022. [PMLR](https://proceedings.mlr.press/v162/carroll22a.html); [arXiv:2204.11966](https://arxiv.org/abs/2204.11966). (A 2022 Franklin et al. paper on preference change is cited by it; its title could not be verified here.)
9. Findeis A, Kaufmann T, Hüllermeier E, Albanie S, Mullins R. Inverse Constitutional AI: Compressing Preferences into Principles. ICLR 2025. [arXiv:2406.06560](https://arxiv.org/abs/2406.06560)
10. Scheurer J, et al. Training Language Models with Language Feedback at Scale. TMLR. [arXiv:2303.16755](https://arxiv.org/abs/2303.16755v3)
11. Feedback-to-Rubrics: Can We Learn Expert Criteria from Inline Comments? ICML 2026. [arXiv:2605.29857](https://arxiv.org/html/2605.29857)
12. WildFeedback (identified by a search summary, mining preferences from real user feedback into checklists). [arXiv:2408.15549](https://www.arxiv.org/pdf/2408.15549)
13. Auto-Rubric: Learning to Extract Generalizable Criteria for Reward Modeling. 2025. [arXiv:2510.17314](https://arxiv.org/html/2510.17314v1)
14. Aamodt A, Plaza E. Case-Based Reasoning: Foundational Issues, Methodological Variations, and System Approaches. AI Communications 7(1):39-59; 1994. [DOI](https://www.doi.org/10.3233/AIC-1994-7104); [PDF](https://www.cs.auckland.ac.nz/~ian/CBR/AICom.pdf)
15. Rissland EL, Ashley KD. A Case-Based System for Trade Secrets Law (HYPO); Ashley KD, Rissland EL. Compare and Contrast, a Test of Expertise. AAAI 1987. [AAAI87-049](https://cdn.aaai.org/AAAI/1987/AAAI87-049.pdf); overview: [HYPO CBR](https://en.wikipedia.org/wiki/HYPO_CBR)
16. Cohen WW. Fast Effective Rule Induction. ICML 1995:115-123. [mlanthology](https://mlanthology.org/icml/1995/cohen1995icml-fast)
17. Rudin C. Stop explaining black box machine learning models for high stakes decisions and use interpretable models instead. Nature Machine Intelligence 1:206-215; 2019. [arXiv:1811.10154](https://arxiv.org/abs/1811.10154v3)
18. Self-Improving AI Coding Agents Through Accumulated Behavioral Rules: A Closed-Loop Framework. 2026 preprint (self-reported results, not verified). [Hugging Face papers](https://huggingface.co/papers/2607.13091.md)
19. Czarkowski M, Kay J. A Scrutable Adaptive Hypertext. AH 2002, LNCS 2347 (and Bringing scrutability to adaptive hypertext teaching, ITS 2000); Personis and related work, read through summaries. [Springer chapter](https://link.springer.com/doi/10.1007/3-540-45108-0_5); [Kay, ERCIM News](https://ercim.eu/publication/Ercim_News/enw46/kay.html); [Kay and Kummerfeld](https://wwwis.win.tue.nl/ah94/Kay.html)
20. Bull S, Kay J. SMILI: A Framework for Interfaces to Learning Data in Open Learner Models. IJAIED 2007, revised 2016 (26:293-331); and Open Learner Models (2010 chapter). [Birmingham record](https://research.birmingham.ac.uk/en/publications/smili-a-framework-for-interfaces-to-learning-data-in-open-learner/); [EduTechWiki](https://edutechwiki.unige.ch/en/Open_learner_model)
21. OpenAI. Memory and new controls for ChatGPT (help article, availability varies). [help.openai.com](https://help.openai.com/en/articles/8590148-memory-faq)
22. Anthropic. How Claude remembers your project (CLAUDE.md and auto memory). [code.claude.com](https://code.claude.com/docs/en/memory); third-party explainer used for details: [fast.io](https://fast.io/resources/claude-code-memory-context-guide/)
23. Jain S, Park C, Viana MM, Wilson A, Calacci D. Interaction Context Often Increases Sycophancy in LLMs. CHI 2026. [arXiv:2509.12517](https://arxiv.org/abs/2509.12517)
24. OpenAI. Expanding on what we missed with sycophancy. 2025. [openai.com](https://openai.com/blog/expanding-on-sycophancy)
25. Know Me, Respond to Me: Benchmarking LLMs for Dynamic User Profiling and Personalized Responses at Scale (PersonaMem). COLM 2025. [arXiv:2504.14225](https://arxiv.org/abs/2504.14225); critique of update horizons in Memora, [ACL Findings 2026](https://aclanthology.org/2026.findings-acl.1337.pdf) (read through a summary)
26. PersistBench: When Should Long-Term Memories Be Forgotten by LLMs? [arXiv:2602.01146](https://arxiv.org/abs/2602.01146); CIMemories (contextual integrity of memory), [arXiv:2511.14937](https://arxiv.org/html/2511.14937); OP-Bench on over-personalisation (identified through a search summary; no link verified)
27. Li BZ, Tamkin A, Goodman N, Andreas J. Eliciting Human Preferences with Language Models. [arXiv:2310.11589](https://arxiv.org/abs/2310.11589)
28. Sadigh D, Dragan AD, Sastry S, Seshia SA. Active Preference-Based Learning of Reward Functions. RSS 2017. [roboticsproceedings.org](https://roboticsproceedings.org/rss13/p53.html)
29. Houlsby N, Huszár F, Ghahramani Z, Lengyel M. Bayesian Active Learning for Classification and Preference Learning. 2011. [arXiv:1112.5745](https://arxiv.org/pdf/1112.5745)
30. Boutilier C, Patrascu R, Poupart P, Schuurmans D. Constraint-Based Optimization and Utility Elicitation Using the Minimax Decision Criterion. Artificial Intelligence 170(8-9):686-713; 2006. [abstract](https://www.cs.toronto.edu/~cebly/Papers/BPPS-aij06-abs.html); [IJCAI 2005](https://ijcai.org/proceedings/2005/19)
31. Li X, Zhou R, Lipton ZC, Leqi L. Personalized Language Modeling from Personalized Human Feedback. [arXiv:2402.05133](https://arxiv.org/abs/2402.05133v3)
32. Bifet A, Gavaldà R. Learning from Time-Changing Data with Adaptive Windowing. SIAM SDM 2007:443-448. [IP Paris record](https://researchportal.ip-paris.fr/en/publications/learning-from-time-changing-data-with-adaptive-windowing/); implementation: [frouros ADWIN](https://frouros.readthedocs.io/en/latest/api_reference/detectors/concept_drift/auto_generated/frouros.detectors.concept_drift.streaming.window_based.ADWIN.html)
33. Guo C, Pleiss G, Sun Y, Weinberger KQ. On Calibration of Modern Neural Networks. ICML 2017. [arXiv:1706.04599](https://arxiv.org/abs/1706.04599v1)
34. Park JS, et al. Generative Agent Simulations of 1,000 People. 2024. [arXiv:2411.10109](https://arxiv.org/abs/2411.10109) (interview finding as reported in the earlier equerry report; not re-verified here)
