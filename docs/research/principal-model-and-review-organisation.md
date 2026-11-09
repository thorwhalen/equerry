# A principal model and a review organisation for a solo operator of many coding agents

Research report, standard depth, 2026-11-09. Design question only: no private material was used.

## Summary and recommendation

**The question.** A solo operator runs many coding-agent sessions and wants two things: (1) an agent that *represents* them, estimating how likely they are to **disagree** with a recommendation (and why), so low-risk decisions go through automatically and the rest reach them annotated; (2) an "organisation" of reviewer agents by concern (design, architecture, legal/privacy, people, marketing, business) whose opinions are filtered so the operator, like a CEO, sees only what matters.

**Verdict in one paragraph.** Build (1) as a **veto predictor inside a selective-prediction gate**, not as a "digital twin". The best published simulations of real individuals reach about 85% of a person's own two-week test-retest consistency on survey items, and the person only agrees with themselves about 81% of the time, so raw agreement is about 69% [1]; a 19-study mega-study found twin-human correlations around 0.2 on individual answers [6]. That is far too weak to auto-approve anything on the model's say-so alone. It is strong enough to *rank* recommendations by disagreement risk and to *write the reason*, provided auto-approval is limited to reversible ("two-way door") actions [12] and the threshold is calibrated on the operator's own later overrides [9][10]. Build (2) as **few reviewers, fresh context, no debate, an aggregator that writes an exception report**: the evidence favours one strong model with concern-specific checklists over a committee of mixed models [21], shows debate amplifying bias [22] and rarely beating simple self-consistency at matched compute [19][20], and shows multi-agent systems failing mostly from design and coordination faults [18]. The operator should see only items that breach a stated tolerance per concern, in the PRINCE2 "manage by exception" shape [15], under a hard alert budget, because override rates of 49-96% are what an unfiltered alert stream produces [26].

**The three strongest recommendations.**

1. **Gate on two independent axes, and require both.** Auto-approve only when the action is reversible at low cost (a rule, never a model guess) *and* the calibrated probability that the operator would disagree is below a threshold set from their own override history. Everything else is shown, annotated with the predicted objection. This is the Feng et al. "L4 Approver" level of autonomy [11] and the shape Claude Code's auto mode already uses for tool calls [13].
2. **Learn from corrections, not from a persona.** Mine the operator's typed corrections to agents into retrievable, context-keyed preference statements (the PRELUDE/CIPHER pattern [4]), treat pre-2023 writing as a weak prior for values and style only, and close the loop: every shown item the operator approves or overrides is a labelled example for recalibration. A one-to-two-hour structured interview is likely the cheapest strong signal (estimate from [1]: 20% of a two-hour interview still beat every non-interview baseline).
3. **Reviewers contribute intelligence, never actions, and report by exception.** One writer, reviewers with no shared prior context reading only the diff or the decision [17], concerns expressed as checklists for one strong model rather than as six personas, a meta-judge rather than a debate for aggregation [22], and a tolerance per concern that decides what reaches the operator [15].

**Biggest risks.** Sycophancy (the representative agrees with whatever the operator said last [23]), stereotyping and flattening of the operator into a caricature [24][6], drift when the operator rewrites a preference (old corrections keep voting), automation bias in the operator who stops reading annotated items [25], and token cost (multi-agent research systems use roughly 15x the tokens of a chat [16]).

## What already exists in the fleet

- An earlier report on LLM-as-judge and reference-free quality estimation covers judge biases, judge validation against humans, panels of judges and the `signal -> calibrate -> decide` pipeline in depth (report 9 of the `ek` evaluation package's docs, "LLM-as-Judge & Reference-Free Agent Quality Estimation"). This report does not re-derive it; its rule "never gate on an uncalibrated signal" applies directly to theme A.
- The `acquaint` package already simulates how a known person reads a draft from a brief built out of their own writing (its profile and recipient-reader workflow). It is the nearest in-fleet component to theme A, and a principal model is plausibly the same mechanism pointed at the operator. This is an observation about fit, not a verified design decision.

## A. Principal and user preference modelling for delegated decisions

### A1. How well can a model simulate one specific person?

- **The best case so far.** Park et al. built agents for 1,052 people from two-hour interviews (mean 6,491 words of participant speech). On held-out General Social Survey items the agents' raw accuracy was 68.85%, against the participants' own two-week replication accuracy of 81.25%, giving the headline "normalized accuracy" of 0.85 [1][2]. Demographics-only agents reached 0.74 and short-persona agents scored lower than interview agents; Big Five normalized correlation was 0.80 [1].
- **Corpus size.** In the same paper, randomly deleting 80% of the interview (leaving roughly 24 minutes) lowered GSS normalized accuracy only from 0.85 to 0.79, still above every survey- or demographics-based agent; bullet-point summaries of the interview kept 0.83, so most of the value is in the *content*, not the person's wording [1]. The authors note predictive gains "begin to asymptote" once a domain is covered [1]. Implication (estimate): for a principal model, a few thousand words of *on-topic* statements of preference matter more than a large general corpus.
- **The sobering replication.** A Columbia mega-study of 19 pre-registered studies and 164 outcomes found twin-human correlations around 0.2 for individual answers, less variance in twin answers than in human ones, and that richer individual data helped capture *relative* differences between people but not exact individual answers [6]. The project page also reports a progressive "blue-shift" when persona descriptions get richer (reported on the project page; not checked in the paper) [6]. The companion dataset Twin-2K-500 (2,058 people, 500 questions, public) is the standard testbed [5].
- **Persona prompting is not the fix.** On World Values Survey microdata, multi-attribute persona prompting gave no clear aggregate improvement and often degraded alignment, redistributing errors rather than removing them [7]. TwinVoice benchmarks six facets of a digital twin and finds models better at opinion consistency and lexical fidelity than at persona tone and memory recall [8].
- **LLM as proxy rater.** LLMs reproduce expert *rankings* of text quality well while their absolute scales differ [27]; GPT-4 as judge agreed with human preferences above 80% on MT-Bench, about the human-human rate, with position, verbosity and self-enhancement biases [28]. A disagreement predictor should therefore be used to *rank* items, with thresholds fitted per operator, not read as an absolute probability.

**Verdict A1.** Expect a representative to be right about the operator's likely disagreement well short of always; the ceiling is the operator's own inconsistency (about 19% self-disagreement on survey retest [1], an analogue, not a measurement of coding decisions). Use it to rank and annotate; do not let it decide one-way doors.

### A2. Learning preferences from natural-language corrections

- PRELUDE/CIPHER (NeurIPS 2024) learns a natural-language description of a user's latent preference from their edits, stores it keyed by context, and retrieves the preferences of the k most similar past contexts for new work; no fine-tuning, and it lowered user edit cost relative to baselines in summarisation and email tasks with a simulated user [4]. This is the closest published pattern to "mine my corrections to agents": the stored unit is a *preference statement with its context*, which is inspectable and deletable.
- Individualised-preference benchmarks (PersonalLLM, PRISM, SynthesizeMe) exist and treat a single user's sparse feedback as the central difficulty; SynthesizeMe reports a 4.4% gain in personalised judge accuracy from persona-guided prompts induced from a user's past choices [29]. Gains are modest; the field is young.
- **Drift and rewritten preferences.** No source found measures what happens when a user deliberately rewrites a standing preference. This is a gap (unverified either way). Design implication: give every mined preference a date, a source and a supersession link, retrieve newest-first, and let an explicit restatement retire older entries rather than averaging with them.

### A3. Calibration, abstention and learning from overrides

- **Terminology and theory.** Deciding when to act and when to hand over is *selective prediction* (risk-coverage trade-off) and, when the alternative is a human, *learning to defer*: a classifier plus a rejector trained against samples of the expert's decisions, with consistent surrogate losses since Mozannar and Sontag [9].
- **Guarantees.** Conformal risk control can set an abstention threshold so that the rate of wrong auto-decisions stays under a chosen bound, using only a small calibration set and no fine-tuning [10]. For this design, the calibration set is the operator's own approve/override log.
- **Verbalised confidence.** Asking a model for a number is cheap but miscalibrated: verbalised confidences cluster at 80-100% and are overconfident [30], though for RLHF models they can beat token probabilities and improve when the model first considers alternatives [31]. Consistency across several samples beats a single stated number [30]. Use the number only after calibration.
- **Active learning.** Every item shown to the operator is a label. Prioritise showing items near the threshold (uncertainty sampling) to learn fastest; this is standard active learning, stated here as a recommendation, not a sourced finding.

### A4. What fails

- **Sycophancy.** Five assistants consistently tailored answers to the user's stated views; humans and preference models sometimes prefer convincing sycophantic answers over correct ones [23]. A representative built from the operator's words will lean toward predicting agreement with whatever the operator last said. Mitigate by asking it to argue the disagreement case first and by measuring its false-"agree" rate on the override log.
- **Stereotyping and flattening.** LLM-written personas contain more stereotypes than human-written ones [24]; twins show less variance than people [6]. The representative will make the operator more consistent and more typical than they are.
- **Instruction drift.** System-prompt instructions measurably decay within about eight dialogue rounds [32]: re-inject the preference set per decision rather than relying on a long-lived session.

## B. Decision rights and delegation to agents

- **Levels of autonomy.** Feng, McDonald and Zhang define five levels by the user's role: Operator, Collaborator, Consultant, **Approver** (agent acts alone and engages the user only in risky or pre-specified situations) and Observer [11]. The older human-factors model of Parasuraman, Sheridan and Wickens grades automation separately for information acquisition, analysis, *decision selection* and *action implementation* [25]; the useful lesson is that a representative may be allowed to *decide* low-risk items while *action* stays gated by reversibility.
- **Reversibility.** Bezos's Type 1 / Type 2 distinction: consequential, irreversible decisions ("one-way doors") deserve slow deliberation; reversible ones ("two-way doors") should use a light-weight process, and the common organisational failure is treating Type 2 as Type 1 [12]. The same letter's "disagree and commit" is the right posture for a representative: record the predicted objection, proceed when the door is two-way.
- **Decision-rights frameworks.** RAPID (Recommend, Agree, Perform, Input, Decide) gives one owner the D, distinguishes *Agree* (a veto that must be resolved) from *Input* (advice that can be ignored), and notes Recommend does most of the work [14]. RACI (Responsible, Accountable, Consulted, Informed) and variants such as DACI assign roles per deliverable [33]. Mapping for this design: the operator holds D; the representative is a Recommender; concern reviewers are Input by default and Agree only for named one-way-door concerns (for example privacy and legal).
- **Management by exception.** Only significant deviations from plan are brought to management; silence means on track [34]. PRINCE2 makes it operational: each level gets *tolerances* (time, cost, scope, quality, risk, benefit, and in PRINCE2 7 sustainability) and raises an **exception report** when a tolerance is forecast to be breached, with situation, impact, options and a recommendation [15]. This is the right schema for what reaches the operator.
- **Agent products already do the reversible-auto-approve pattern.**
  - Claude Code's **auto mode** lets a classifier adjudicate each tool call; it blocks by default things like force pushes, `git reset --hard`, production deploys, irreversible deletion of pre-existing files and merging an unapproved PR, allows local edits and lockfile installs, and falls back to prompting after 3 consecutive or 20 total blocks [13].
  - OpenAI Codex separates **sandbox** (read-only, workspace-write, full access) from **approval policy** (`untrusted`, `on-request`, `never`) [35].
  - Factory's Droid attaches a **risk rating** (low/medium/high plus justification) to every command and auto-runs it when the rating is at or below the chosen autonomy level, with "medium" defined as reversible workspace changes; an org can cap the maximum level [36].
  - Devin and Cursor background agents were not verified for this report.

**Verdict B.** The gate has two independent inputs: a *rule-based* reversibility/blast-radius class (as in [13][36]) and the *learned* disagreement probability (A). The rule wins ties. Copy the products' detail that repeated blocks drop the agent back to asking [13].

## C. Multi-agent review organisations

- **Role-play companies.** MetaGPT (product manager, architect, engineer roles; MIT; about 70k GitHub stars) and ChatDev (chat chains; OpenBMB; licence reported Apache-2.0 by a secondary source) are the reference "AI software company" designs [37]. They are studied as process simulations; they are not review filters for a human executive.
- **Why multi-agent systems fail.** MAST classifies 14 failure modes in three groups from 1,642 annotated traces across seven frameworks and notes that gains over single-agent setups are often minimal [18].
- **Debate.** Multi-agent debate does not reliably beat self-consistency or ensembling at matched compute [19][20]; one 2026 workshop paper reports small gains (1.3-2.7 points) on harder tasks [20]. In LLM-as-judge settings, debate *amplifies* position, verbosity and bandwagon bias after the first round, while a meta-judge aggregating independent verdicts resists them [22].
- **One strong model vs a mix.** Self-MoA, which aggregates several samples from the single best model, beat mixtures of different models by 6.6% on AlpacaEval 2.0 and 3.8% on average; mixing usually lowers average member quality [21]. Panels of small diverse judges (PoLL) were cheaper (seven to eight times) and less biased than one large judge for *evaluation* [38]: diversity helps judging, quality helps generation.
- **What works in practice.** Cognition reports that multi-agent setups work when "writes stay single-threaded and the additional agents contribute intelligence rather than actions"; their reviewer catches about 2 bugs per PR, works best with *no shared context* with the coder, and needs the coding agent to filter findings against the user's instructions to avoid looping and scope creep [17]. Their earlier position was that sharing full context matters so much that most multi-agent designs should be ruled out by default [3]. Anthropic's research system beat a single agent by 90.2% on an internal eval but used about 15x the tokens of a chat (agents alone about 4x) [16].
- **Filtering for one reader.** Unfiltered alerts get overridden: clinicians override 49-96% of drug-safety alerts, attributed to a poor signal-to-noise ratio [26]; automation bias produces both missed and wrongly accepted recommendations and is not fixed by training or instructions [25]. The executive layer must therefore cap volume, not just rank it.

**Verdict C.** Do not stand up six persona agents that converse. Run one strong reviewer model per decision, with one checklist per concern (design, architecture, legal/privacy, people, marketing, business) and a fresh context; sample it more than once if confidence matters (Self-MoA, self-consistency). Aggregate with a meta-judge that maps each finding to a concern tolerance and emits a PRINCE2-shaped exception item: *what, which tolerance, impact, options, recommendation, predicted operator objection*. Everything under tolerance is logged, not shown. Add a second, different-model reviewer only for one-way-door concerns, where diversity buys bias resistance [38].

## D. Standard terminology

| Term | Means | Use it for |
|---|---|---|
| **Principal** (principal-agent) | The party on whose behalf an agent acts | The operator |
| **User model** | Any representation a system keeps of a user | The umbrella term |
| **Preference model** / reward model | A model scoring outputs by how much a person or population prefers them [23][29] | The disagreement predictor's core |
| **Latent preference** (learning from edits) | A preference inferred from corrections, stored as text [4] | Mined corrections |
| **Persona** | A description used to condition a model; usually population-level and thin [7][24] | Avoid as the core |
| **Digital twin** / generative agent of a person | An LLM agent conditioned on rich individual data, evaluated against the person's own answers [1][5][6] | Literature search only |
| **Selective prediction**, **abstention**, **risk-coverage** | Predicting only when confident enough [10] | The gate |
| **Learning to defer** (rejector) | Learning when to hand a case to a human expert [9] | The gate, trained on overrides |
| **Conformal risk control** | Thresholds with a bound on the error rate [10] | Setting the auto-approve threshold |
| **Levels of autonomy** (Approver level) | User role relative to agent [11][25] | Naming the operating mode |
| **Type 1 / Type 2 decision**, one-way / two-way door | Irreversible vs reversible [12] | The reversibility rule |
| **Decision rights** (RAPID, RACI, DACI) | Who recommends, vetoes, decides [14][33] | Reviewer roles |
| **Management by exception**, **tolerance**, **exception report** | Escalate only on forecast breach of a tolerance [15][34] | What reaches the operator |
| **Escalation policy** | Industry term (incident response) for who is notified when; not separately sourced here | Routing rules |
| **LLM-as-a-judge**, **panel of judges (PoLL)**, **meta-judge** | Model evaluators and their aggregation [22][28][38] | Reviewer layer |
| **Alert fatigue**, **automation bias** | Over-alerting and over-trusting automation [25][26] | Failure modes to measure |

## E. Libraries and tools

| Tool | Does | Licence, maturity | Verdict |
|---|---|---|---|
| `genagents` (Stanford) [39] | Code for interview-conditioned generative agents | MIT; research code; the 1,000 agents are not released | **Study** (memory + "expert reflection" design) |
| PRELUDE / CIPHER code [4] | Learning preferences from edits | Public research code; licence not checked | **Study**; reimplement the pattern (small) |
| Twin-2K-500 [5] | Individual-level dataset + simulation code | Paper CC BY 4.0; data on Hugging Face | **Study** for benchmarking a principal model method |
| MAST [18] | Failure taxonomy and LLM annotator for multi-agent traces | Public; licence not checked | **Study**; use its categories to audit the organisation |
| Inspect AI [40] | Eval framework | MIT; actively maintained | **Depend** for validating the veto predictor against the override log |
| DeepEval [40] | LLM eval metrics incl. judges | Apache-2.0 | Alternative to Inspect; **study** |
| LangGraph `interrupt` [41] | Pause a graph for human approval, persisted | Open source (licence not re-verified here) | **Study**; only if the fleet adopts LangGraph |
| HumanLayer SDK [42] | `@require_approval` decorator, Slack/email approvals | SDK Apache-2.0; product has pivoted | **Study** the API shape; avoid as a dependency |
| MetaGPT, ChatDev [37] | Role-play software companies | MIT; Apache-2.0 (secondary) | **Avoid** as a base; study role prompts |
| CrewAI, CAMEL [40] | Role-based multi-agent frameworks | MIT; Apache-2.0 | **Avoid**: they encourage the conversing-committee shape C argues against |
| AutoGen [41] | Group-chat multi-agent | In maintenance mode since late 2025 (secondary sources); successor Microsoft Agent Framework | **Avoid** |

No open-source library implementing a calibrated "will my principal veto this?" predictor was found; it would be new work, small enough to build on an existing judge plus a calibration step.

## Suggested shape (for the plan stage)

1. **Decision record** (schema): action, reversibility class (rule), concern tags, recommendation, reviewer findings, predicted disagreement probability and reason, gate outcome, operator outcome (approved / overridden / edited, with the correction text).
2. **Preference store**: dated, sourced, context-keyed preference statements mined from corrections; supersession links; retrieval newest-first [4].
3. **Representative**: one strong model, argues the objection first, returns P(disagree) and a one-line reason; calibrated on the decision records [10][30].
4. **Gate**: auto-approve iff reversible-class AND calibrated P(disagree) below threshold; otherwise exception item. Fall back to asking after repeated near-misses [13].
5. **Reviewers**: concern checklists for one strong model, fresh context, meta-judge aggregation, tolerance per concern, alert budget per day [15][17][22].
6. **Metrics**: false-auto-approve rate (overrides of items that would have passed), shown-item volume, override rate on shown items (alert-fatigue signal), and the operator's own consistency on repeated items as the ceiling [1][26].

## REFERENCES

1. Park JS, Zou CQ, Shaw A, et al. Generative Agent Simulations of 1,000 People. arXiv:2411.10109; 2024. [arXiv](https://arxiv.org/abs/2411.10109) (numbers from the full text: raw 68.85% vs 81.25% self-replication; 80%-ablation 0.79; summary agents 0.83).
2. Stanford HAI. Simulating Human Behavior with AI Agents (policy brief). [hai.stanford.edu](https://hai.stanford.edu/policy/simulating-human-behavior-with-ai-agents)
3. Yan W. Don't Build Multi-Agents. Cognition, 2025. [cognition.com](https://cognition.com/blog/dont-build-multi-agents)
4. Gao G, Taymanov A, Salinas E, Mineiro P, Misra D. Aligning LLM Agents by Learning Latent Preference from User Edits. NeurIPS 2024. [arXiv:2404.15269](https://arxiv.org/abs/2404.15269)
5. Toubia O, Gui GZ, Peng T, et al. Twin-2K-500: A dataset for building digital twins of over 2,000 people. arXiv:2505.17479; 2025. [arXiv](https://arxiv.org/abs/2505.17479)
6. Peng T, Toubia O, et al. A Mega-Study of Digital Twins Reveals Strengths, Weaknesses and Opportunities for Further Improvement. arXiv:2509.19088; 2025. [arXiv](https://arxiv.org/abs/2509.19088); "blue-shift" per [Columbia DAP Lab project page](https://daplab.cs.columbia.edu/projects/digitaltwins)
7. Morocho et al. Assessing the Reliability of Persona-Conditioned LLMs as Synthetic Survey Respondents. WebConf 2026. [arXiv:2602.18462](https://arxiv.org/abs/2602.18462)
8. Du et al. TwinVoice: A Multi-dimensional Benchmark Towards Digital Twins via LLM Persona Simulation. ACL Findings 2026. [arXiv:2510.25536](https://arxiv.org/abs/2510.25536)
9. Mozannar H, Sontag D. Consistent Estimators for Learning to Defer to an Expert. ICML 2020. [arXiv:2006.01862](https://arxiv.org/abs/2006.01862)
10. Yadkori YA, et al. Mitigating LLM Hallucinations via Conformal Abstention. arXiv:2405.01563; 2024. [arXiv](https://arxiv.org/abs/2405.01563); see also Tayebati et al. [arXiv:2502.06884](https://arxiv.org/abs/2502.06884)
11. Feng KJK, McDonald DW, Zhang AX. Levels of Autonomy for AI Agents. arXiv:2506.12469; 2025. [arXiv](https://arxiv.org/abs/2506.12469)
12. Bezos J. 2016 Letter to Shareholders (two-way doors; disagree and commit; refers back to the 2015 Type 1/Type 2 discussion). [aboutamazon.com](https://www.aboutamazon.com/news/company-news/2016-letter-to-shareholders)
13. Anthropic. Claude Code permission modes (auto mode). [code.claude.com](https://code.claude.com/docs/en/permission-modes)
14. Bain & Company. RAPID decision roles. [Decisions: who does what](https://www.bain.com/insights/decisions-who-does-what/)
15. PRINCE2: manage by exception, tolerances and exception reports. [Wikipedia](https://en.wikipedia.org/wiki/PRINCE2); [prince2.wiki](https://prince2.wiki/Manage_by_exception)
16. Anthropic. How we built our multi-agent research system. 2025. [anthropic.com](https://www.anthropic.com/engineering/multi-agent-research-system)
17. Yan W. Multi-Agents: What's Actually Working. Cognition, 2026-04-22. [cognition.com](https://cognition.com/blog/multi-agents-working)
18. Cemri M, et al. Why Do Multi-Agent LLM Systems Fail? NeurIPS 2025 D&B. [arXiv:2503.13657](https://arxiv.org/abs/2503.13657)
19. Smit A, et al. Should we be going MAD? A Look at Multi-Agent Debate Strategies for LLMs. [arXiv:2311.17371](https://arxiv.org/abs/2311.17371); Huang J, et al. Large Language Models Cannot Self-Correct Reasoning Yet. [arXiv:2310.01798](https://arxiv.org/abs/2310.01798)
20. Multi-agent debate vs single-agent baselines at matched compute: [arXiv:2502.08788](https://arxiv.org/abs/2502.08788); counterpoint, ACL SRW 2026: [aclanthology 2026.acl-srw.1](https://aclanthology.org/2026.acl-srw.1.pdf)
21. Li W, Lin Y, Xia M, Jin C. Rethinking Mixture-of-Agents: Is Mixing Different Large Language Models Beneficial? 2025. [arXiv:2502.00674](https://arxiv.org/abs/2502.00674)
22. Ma et al. Judging with Many Minds: Do More Perspectives Mean Less Prejudice? 2025. [arXiv:2505.19477](https://arxiv.org/abs/2505.19477)
23. Sharma M, Tong M, et al. Towards Understanding Sycophancy in Language Models. [arXiv:2310.13548](https://arxiv.org/abs/2310.13548)
24. Cheng M, Durmus E, Jurafsky D. Marked Personas. ACL 2023. [aclanthology 2023.acl-long.84](https://aclanthology.org/2023.acl-long.84)
25. Parasuraman R, Sheridan TB, Wickens CD. A model for types and levels of human interaction with automation. IEEE SMC-A 30(3), 2000. [core.ac.uk PDF](https://core.ac.uk/download/187203187.pdf); Parasuraman R, Manzey DH. Complacency and Bias in Human Use of Automation. 2010. [TRID](https://trid.trb.org/View/1085175)
26. van der Sijs H, Aarts J, Vulto A, Berg M. Overriding of drug safety alerts in computerized physician order entry. JAMIA 2006. [PMC1447540](https://pmc.ncbi.nlm.nih.gov/articles/PMC1447540)
27. Chiang CH, Lee HY. Can Large Language Models Be an Alternative to Human Evaluations? ACL 2023. [aclanthology 2023.acl-long.870](https://aclanthology.org/2023.acl-long.870/)
28. Zheng L, et al. Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena. NeurIPS 2023. [arXiv:2306.05685](https://arxiv.org/abs/2306.05685)
29. Zollo T, et al. PersonalLLM. ICLR 2025. [arXiv:2409.20296](https://arxiv.org/abs/2409.20296); Ryan M, et al. SynthesizeMe! [arXiv:2506.05598](https://arxiv.org/abs/2506.05598)
30. Xiong M, et al. Can LLMs Express Their Uncertainty? ICLR 2024. [arXiv:2306.13063](https://arxiv.org/abs/2306.13063)
31. Tian K, et al. Just Ask for Calibration. EMNLP 2023. [aclanthology 2023.emnlp-main.330](https://aclanthology.org/2023.emnlp-main.330)
32. Li K, et al. Measuring and Controlling Instruction (In)Stability in Language Model Dialogs. COLM 2024. [arXiv:2402.10962](https://arxiv.org/abs/2402.10962)
33. Responsibility assignment matrix (RACI, DACI). [Wikipedia](https://en.wikipedia.org/wiki/Responsibility_assignment_matrix)
34. AccountingTools. Management by exception definition. [accountingtools.com](https://www.accountingtools.com/articles/what-is-management-by-exception.html)
35. OpenAI. Codex sandboxing and approvals. [developers.openai.com](https://developers.openai.com/codex/concepts/sandboxing)
36. Factory. Auto-Run and autonomy levels. [docs.factory.ai](https://docs.factory.ai/cli/user-guides/auto-run)
37. MetaGPT repository. [github.com/FoundationAgents/MetaGPT](https://github.com/FoundationAgents/MetaGPT); ChatDev comparison and licence (secondary). [aitoolsatlas.ai](https://aitoolsatlas.ai/compare/chatdev-vs-metagpt)
38. Verga P, et al. Replacing Judges with Juries: Evaluating LLM Generations with a Panel of Diverse Models. 2024. [arXiv:2404.18796](https://arxiv.org/abs/2404.18796)
39. joonspk-research/genagents. [GitHub](https://github.com/joonspk-research/genagents)
40. Licences: CrewAI [Wikipedia](https://en.wikipedia.org/wiki/CrewAI); CAMEL [ecosyste.ms](https://awesome.ecosyste.ms/projects/github.com%2Fcamel-ai%2Fcamel); Inspect AI [libraries.io](https://libraries.io/pypi/inspect-ai/0.3.132); DeepEval [sourcepulse](https://www.sourcepulse.org/projects/1843132)
41. LangGraph human-in-the-loop and AutoGen maintenance status (secondary). [DZone](https://dzone.com/articles/agent-frameworks-human-loop); [agenticwire](https://www.agenticwire.news/article/mastra-vs-autogen-2026)
42. HumanLayer SDK. [PyPI](https://pypi.org/project/humanlayer/)
