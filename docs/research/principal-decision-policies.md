# Deciding at the level of a principle: how one standing decision settles many concrete ones

Research report for equerry, 2026-11-18. Theme: how a busy principal makes one high-level decision (a policy, a philosophy, a standing order) that settles many concrete low-level decisions, and how the agents working for them find, group and apply those principles. It builds on, and does not repeat, the representative / veto-predictor model, the gate (reversible by rule and low disagreement risk) and the consent table (allowed / ask_first / off) already in the repository.

Evidence labels used below: **[verified]** means a source was opened or returned by search this session; **[secondary]** means only secondary summaries were seen; **[memory]** means stated from background knowledge and not checked this session, so confirm before relying on it; **[estimate]** is my own judgement.

## 1. Main finding

The literature converges on one structure, and it is not "the principal states a rule". It is a **second-order decision** [1]: the principal decides *how* a class of first-order decisions will be decided, and the cheap, durable way to do that is to state **intent plus bounds** (purpose, the trade-off ordering, the tripwires that force escalation) rather than a rule list [2][3]. Three consequences for equerry follow.

1. A policy question should be asked as a **trade-off between two or three things the principal already understands, with the concrete cases it would settle listed under it**, because stated preferences are constructed at the moment of asking and are sensitive to how the question is framed [4][5]. A policy elicited without consequences is the least stable kind.
2. The policy must carry **its own break conditions** (exceptions that escalate), a **scope**, an **origin** (which cases it was induced from) and a **review trigger**. Rules are entrenched generalizations that are wrong at the edges by construction [6]; the failure modes (over-generality, silent drift, Goodhart) are all failures of missing metadata, not of the idea [7][8][9].
3. The cheapest first step already exists in the gate: a saved rule is a policy. What is missing is the **induction step** (finding the cluster of pending decisions that one answer would cover) and the **audit step** (sampling cases a policy settled and checking they still match the principal's view).

## 2. Organisational and military practice

### 2.1 Mission command and commander's intent

US Army doctrine defines mission command as the commander's use of mission orders to enable disciplined initiative within the commander's intent; intent is a concise statement of purpose and desired end state that lets subordinates act without further orders when events depart from the plan; mission orders say what to achieve and leave the how to the subordinate [2] **[secondary: study aids and an Army article were returned; the current ADP 6-0 text was not opened]**. The doctrine lists principles such as shared understanding, commander's intent, mission orders, disciplined initiative and accepting prudent risk, with competence and mutual trust as preconditions; editions differ on whether there are six or seven [2].

What transfers to equerry: the unit of delegation is a *purpose and end state*, not an instruction. An agent that knows why the principal prefers "ship small and reversible" can settle cases the principal never saw. What does not transfer for free: military intent presumes shared training and trust built over time. An agent fleet has neither, so intent must be written down, versioned and tested against cases (section 4).

### 2.2 Standing orders and rules of engagement

Standing orders and rules of engagement (ROE) are pre-authorized permissions and prohibitions that apply until revoked, usually with explicit escalation for cases outside them. I did not retrieve a primary ROE source this session, so the structure (permit, prohibit, escalate, with a named authority able to change them) is **[memory]**. The mapping is direct: equerry's `allowed / ask_first / off` consent classes are an ROE card, with `ask_first` as "request authority" and `off` as "weapons hold".

### 2.3 Decision-rights frameworks (RAPID, DACI, RACI)

RAPID (Bain, 2006) separates Recommend, Agree (the only veto role, to be used sparingly), Perform, Input and Decide (one person) [10]. DACI (popularized by Atlassian) uses Driver, Approver, Contributors, Informed, with exactly one Approver [11]. RACI is the older responsible / accountable / consulted / informed matrix **[memory]**. Critics note the evidence that role charts improve outcomes rests largely on the vendor's own surveys [10] **[secondary]**.

These frameworks allocate *who* decides a *named decision*. They are not policies, but they show the gap equerry fills: they assume decisions are few and large enough to name. With dozens of sessions producing many small decisions, the allocation has to be made once per **decision class**, which is what a delegation board does (next).

### 2.4 Delegation levels

Appelo's seven levels, from Management 3.0, run: tell, sell, consult, agree, advise, inquire, delegate; they are applied with Delegation Poker, where each person privately picks a level for a decision, then the differences are discussed, and the result is recorded on a **delegation board** listing decision areas against levels [12] **[secondary: wording of levels 5-7 differs between summaries; check the book]**.

This is the closest published analogue to the consent table, and it is richer: equerry has three settings (allowed / ask_first / off), Appelo has seven. Levels 5 to 7 are the interesting ones for agents: advise (the agent decides, the principal has already said what they would pick), inquire (the agent decides, then justifies afterwards) and delegate (the agent decides, no report). **Inquire is the level equerry lacks**: "allowed, but listed in the digest so the principal can overturn it". The consent table's `allowed` currently has no after-the-fact review leg beyond logging.

### 2.5 Management by exception

Management by exception reserves senior attention for deviations beyond a threshold. The leadership-research version (Bass and colleagues, active vs passive forms) is **[memory]**; the engineering version (control charts, alerts on thresholds) is the same idea. For equerry the exception test is the gate itself. The design point from this literature: the *threshold* is the policy, so it deserves the same elicitation care as any other (section 5), and passive monitoring (waiting for a bad outcome) is weaker than active monitoring (sampling) **[memory]**.

### 2.6 Policy versus case decisions in law and administration

Law has studied this for a century.

- **Rules versus standards.** Rules cost more to write and less to apply; standards cost less to write and more to apply case by case; standards win when behaviour is varied enough that case-by-case adjudication is more accurate than a rule's over- and under-inclusion [13] **[secondary: the cost framing is credited to Kaplow, Posner and Ehrlich in the results; I could not confirm a single canonical source]**.
- **Rules as entrenched generalizations.** Schauer treats a rule as a generalization that is entrenched, so it is applied even where the generalization's reason does not hold; it is therefore over- and under-inclusive, and he argues for "presumptive" force [6] **[secondary]**. This is the exact structure of a saved "always allow" rule.
- **Second-order decisions.** Sunstein and Ullmann-Margalit name the choice of a strategy for making first-order choices (rules, standards, delegation, presumptions, small steps), usually to cut decision burden or error risk [1] **[secondary: full text not opened]**. This is the right standard term for the whole topic.
- **Precedent and case-based reasoning.** Common-law reasoning decides by analogy to prior cases, and the principle is whatever the earlier decision needed to rest on **[memory: Levi, Schauer]**. Case-based reasoning in AI formalizes this as retrieve, reuse, revise, retain [14] **[secondary: the bibliographic record was confirmed, the cycle text was not]**. For equerry this is the second mechanism besides rules: instead of (or before) writing a rule, **retrieve the principal's nearest past decisions and show them**. Precedent is softer than a rule and fails more gracefully.
- **Street-level discretion.** Lipsky's work on frontline bureaucrats shows that where rules are ambiguous or contradictory, frontline workers' discretion fills the gap, and more rules can create more discretion [15] **[secondary]**. Expect the same from agents: a policy full of ambiguity will be resolved by the agent's own habits, silently.

## 3. Decision science: capturing trade-off preferences once

### 3.1 The toolkit

- **Value-focused thinking** (Keeney) starts from fundamental objectives, the bottom-line goals a decision serves, and generates alternatives from them rather than the reverse [16] **[secondary: catalogue and table of contents; no passage on the formal definition was found]**. It is the right first question for a policy: "what are you actually trying to protect?" so that rules can be derived and re-derived when circumstances change.
- **Multi-attribute utility / value theory (MAUT, MAVT) and MCDA** score alternatives on several attributes and combine them with weights. **Swing weighting** asks the person to compare *swings from worst to best on each attribute's realistic range*, so that a weight cannot be stated without the range it applies to. SMART and SMARTER (Edwards and Barron, 1994) simplify elicitation; SMARTER replaces the second elicitation step with weights computed from a rank order (rank-order-centroid), reported as performing about 98% as well as SMARTS with easier judgments [17] **[secondary]**.
- **Lexicographic and satisficing rules** replace weights with an ordered list ("never trade A for B; among options meeting A, prefer by B") or a threshold ("good enough"). Tversky's lexicographic rules and Simon's satisficing are the standard sources **[memory]**. They are far easier for a busy person to state than weights, and they map onto a gate: hard constraint first, then preference.

### 3.2 How stable are elicited weights?

Not very, and the evidence says why it matters for how equerry asks.

- Preferences are often **constructed in the moment of elicitation**, not retrieved; normatively equivalent elicitation methods yield systematically different answers (preference reversals) [4] **[secondary: abstract-level]**. Adaptive-decision-maker work (Payne, Bettman, Johnson, 1993) supports this but was not retrievable [5] **[memory]**.
- **Range sensitivity.** In Fischer's 1995 experiments, direct importance weights were nearly insensitive to the attribute range (index about 0.12), swing weighting intermediate (about 0.62), and trade-off procedures highest (about 0.63 to 0.78), all below the normative value of 1 [18] **[secondary: figures via a summary of the paper]**. Practical reading: asking "how important is cost?" gives a number that does not move when the cost range changes; asking "which is worth more, saving this much or gaining that much?" does.
- **Splitting bias.** Showing an attribute in more detail can raise its weight, and the bias was not removed by instruction or feedback in one environmental-decision study [19] **[secondary; evidence on training is mixed]**. Practical reading: do not break one concern into many sub-questions when asking.
- **Noise.** Jia, Fischer and Dyer (1998) simulate the effect of random response error on decision quality across weighting methods [20] **[secondary]**.

My inference **[estimate]**: expect a principal's stated trade-off to flip on some fraction of borderline cases when asked again in a different frame. I would plan on "policy plus the 10 to 20 cases it was derived from, re-shown at review" rather than "a weight vector", and treat any policy not confirmed by a case within about a month as provisional. These numbers are guesses to be tested by the pilots in section 8, not findings.

## 4. AI practice

### 4.1 Constitutions and model specs

- **Constitutional AI** trains a model to critique and revise its own outputs against a short written list of principles, then uses AI preference labels (RLAIF), so a few principles settle many cases without a human label per case [21] **[verified]**.
- **Claude's constitution** states a general preference for cultivating judgment over rigid rules, explains the rules it does impose, argues that a minimal set of well-understood rules plus judgment generalizes better than unexplained constraints, and keeps a few hard lines not subject to case-by-case weighing [22] **[secondary: search summaries of the document]**. The same document is a worked example of the rules-versus-standards trade-off in section 2.6.
- **OpenAI Model Spec** is a layered document: a chain of command over instruction sources, mostly overridable *defaults*, and a few fixed rules; later versions also cover agent autonomy [23] **[secondary]**. Its structure (defaults, overrides, fixed rules, an order of authority) is the shape a principal's policy set takes.
- **Deliberative alignment** teaches the model the written specification and trains it to recall and reason over the relevant text *before answering*, and reports better adherence and out-of-distribution generalization than supplying the spec only at deployment [24] **[verified: abstract]**. For agents: policy text is more effective when the agent has to cite which principle applies and why, than when it is merely in context.

### 4.2 Extracting principles from examples, and clustering

- **Inverse Constitutional AI** compresses pairwise preference data into a short list of natural-language principles that lets an LLM annotator reconstruct the original labels; it was validated on synthetic data with known principles and on real preference data [25] **[verified]**. A follow-up argues a flat list of principles is not yet an executable rule because principle composition is left implicit [25] **[secondary]**. This is the closest published method to "the principal answered 40 questions; what policy explains them?", and its test (reconstruct the held-out labels) is a ready-made success measure.
- **PRELUDE / CIPHER** infers a text description of a user's latent preference from their edits to agent output, then retrieves inferred preferences from the k nearest past contexts to build the prompt for the next response [26] **[verified: abstract; results on a simulated user only]**. It is clustering by context similarity plus principle inference, applied to corrections, which is the learning-from-corrections loop the principal-model report already names.
- **Learning to defer** trains a model to decide per case whether to answer or to hand to the human, jointly optimizing accuracy and the human's workload [27] **[verified: abstract]**. This is the formal frame for the gate: the representative is a deferral policy.

I found no published system that *proposes a batch question to a human by clustering pending decisions*. The pieces exist (embedding-based clustering, principle induction [25], nearest-context retrieval [26]) but I could not verify a product doing the composition. Treat this as a gap.

### 4.3 Permission modes and "always allow" in current agent products

Claude Code has permission modes (manual default, accept-edits, plan, auto, dontAsk, bypass), allow / ask / deny rules evaluated in the order deny, ask, allow (so a broad deny beats a narrower allow), and in auto mode a classifier with hard-deny and soft-deny tiers plus allow exceptions; auto mode suspends broad allow rules that could run arbitrary code (for example wildcard shell patterns) while narrow rules such as an exact test command still apply [28][29] **[verified via search summaries of the official pages; the exact "always allow" wording was not found]**. Anthropic's engineering post reports that users accept 93% of manual permission prompts anyway, which motivated auto mode [30] **[secondary: figure traced to the post by search; secondary sites quote a different later figure, unconfirmed]**.

Lessons for equerry, in order of confidence:

1. **Approval fatigue is the default outcome of per-case prompts**, so the policy has to take the approving-by-habit decisions out of the queue; the 93% figure [30] is exactly the population a policy should absorb.
2. **Rule precedence matters and should be fixed once**: deny over ask over allow (equerry's "stricter wins" is the same design) [28].
3. **Broad allow rules are dangerous** (the product itself suspends them in auto mode) [28]: a saved policy should be narrow in its match condition and wide only in its reason.
4. A classifier substituting for the human (auto mode) is the *representative* idea in a product; it has a published residual risk, which the vendor itself states is not eliminated [30].

## 5. Presenting a policy-level question to a non-expert principal

The evidence base here is thin and mostly inferential: constructed preferences [4], range sensitivity [18], splitting bias [19] and the rules-versus-standards cost picture [13] constrain the design; I found no study of the exact setting (a principal reviewing agent decisions). The design below is therefore **[estimate]** except where cited.

### 5.1 Principles for the question

1. **One trade-off, two or three named things**, each a thing the principal already cares about (speed, cost, safety of the public record, reversibility). Do not decompose a concern into sub-attributes (splitting bias [19]).
2. **State the realistic range for each**, as a swing ("a day faster versus a one-in-twenty chance of a rollback"), not "how important is speed" [18].
3. **List the concrete cases the answer would settle**, at least three, with one marginal case at the boundary and one counter-case the policy would *not* cover. Showing consequences counters the construction effect [4].
4. **Offer answers as policies with a default**, never a blank: "A: always do X; B: do X unless Y, otherwise ask; C: ask each time". The middle option (a rule with an exception trigger) should usually be the recommended one, since it carries its own break condition.
5. **Say what the answer costs to undo**, and when it will be shown again ("I'll sample five cases under this in two weeks").
6. **Cap the batch**: a question should settle at least about five cases or it is not worth the principal's attention **[estimate]**.

### 5.2 Examples of framings (invented for illustration)

- *Spend versus speed.* "Eleven sessions want to run paid model calls to finish faster; the largest is about the cost of a coffee, the sum about the cost of a lunch. Choose: A) allow up to a daily cap and report spend in the digest; B) allow only under a per-call cap, ask above; C) ask each time. Cases this settles: [list]. Not covered: any change to a subscription plan (stays ask-first)." This is a threshold (satisficing) with an escalation leg.
- *Public record versus momentum.* "Six agents want to push to branches that are visible on a public repository. The trade-off is moving without waiting against anything unfinished or sensitive appearing in public history. Choose: A) push freely to private repositories only; B) public too, but only after a leak check passes; C) ask each time." This is a lexicographic rule: a hard constraint first, preference second.
- *Tidiness versus risk.* "Four agents want to delete stale branches. Choose: A) delete only after merge; B) delete after a week unmerged and report; C) never." Option B is the Appelo "inquire" level: act, then report.
- *Intent, not rule.* "When two of your goals conflict and nobody asked, which should the agents protect first: finishing what is already half-built, or keeping new work small? Seven pending cases hinge on this." Ordering of two objectives, from value-focused thinking [16].

### 5.3 What the principal should not be asked

Not "rate importance from 1 to 10" (range-insensitive [18]); not a rule in the agent's own jargon; not a question whose cases the principal cannot see; not a question with no stated way to undo.

## 6. Failure modes

| Failure | Mechanism | Source | Control |
|---|---|---|---|
| Over-general policy | A rule's generalization is applied where its reason fails; broad allow rules widen blast radius | [6]; product suspends broad allow rules in auto mode [28] | Narrow match condition; store the reason; escalate when a case matches the rule but not its reason |
| Under-general policy | Rule matches too little, so the principal is asked the same thing repeatedly | [13] (rules cost more to write) | Count repeat questions per cluster; propose widening |
| Silent drift | The written rule stays while practice moves; repeated non-events normalize the shortcut | Normalization of deviance [31]; discretion fills ambiguity [15] | Policy age and last-checked date; sample audit of cases settled; re-ask on a schedule |
| Exceptions that should break the policy | A case satisfies the policy's wording but a tripwire fires (new money, irreversibility, a third party, public exposure) | Delegation and ROE structure [12]; the gate's coverage test | Each policy lists its break conditions; the gate checks them before applying the policy |
| Goodhart | Optimizing a proxy (approval rate, minutes saved, questions avoided) degrades the underlying aim; four variants: regressional, extremal, causal, adversarial | [8]; reward over-optimization [9] | Never reward agents for fewer escalations; measure outcome and overturn rate, not just volume |
| Unstable stated preference | Answer depends on the frame | [4][18][19] | Re-show cases; confirm with a different framing before making a policy durable |
| Approval fatigue | Prompts approved by habit | [30] | Policies and a digest, not more prompts |
| Authority laundering | A policy induced from a handful of cases is treated as though the principal had stated it | my inference **[estimate]** | Mark origin: stated by the principal versus induced and confirmed versus induced only |

## 7. Standard terminology, and what was not asked

- **Second-order decision** (decision about how to decide) [1]; **rules versus standards** [13]; **presumption**; **precedent / stare decisis / case-based reasoning** [14]; **incompletely theorized agreements** (agreeing on the outcome without agreeing on the reason; Sunstein) **[memory]**.
- **Commander's intent, mission orders, disciplined initiative** [2]; **standing order, rules of engagement, delegation of authority, decision rights, escalation criteria, tripwire** **[memory]**.
- **Delegation board, Delegation Poker, levels of delegation** [12]; **RAPID, DACI, RACI** [10][11].
- **MAUT / MAVT, MCDA, swing weights, SMART / SMARTER, value-focused thinking, fundamental versus means objectives** [16][17]; **lexicographic and satisficing rules** **[memory]**; **constructed preferences** [4].
- **Constitution, model spec, instruction hierarchy, deliberative alignment** [21][23][24]; **principle induction / inverse constitutional AI** [25]; **learning to defer, selective prediction, reject option** [27].
- **Policy as code, guardrail, bounded autonomy, approval fatigue** [28][30].

Things not asked that matter:

1. **Policy conflict and precedence.** Policies will collide. The mature systems fix a precedence once: deny over ask over allow [28]; a chain of command [23]; stricter wins. equerry has the last; it needs a written rule for two *allowed* policies that disagree.
2. **Policy scope and ownership.** A policy for one project may be wrong for another. Record scope (project, action class, time) as a field, not prose.
3. **Who may *propose* a policy.** An agent that proposes policies that expand its own permissions has a conflict of interest; the approval-is-a-security-boundary point in the existing consent design applies, and the agent's proposal should be shown with the strongest case against it (the contradictor role already exists in the repository).
4. **Principal change.** The principal's views move; a policy is a view at a date. Expiry or review dates are part of the object.
5. **Second principal or delegate.** If the principal ever delegates to a human deputy, policies need an author and a scope of who may amend them **[estimate]**.
6. **Evidence on decision frameworks is weak.** The RAPID-style claims rest on vendor surveys [10]; equerry should measure its own policy outcomes rather than assume they help.

## 8. Five approaches equerry could pilot in a week

Each uses what exists (the Item record, the gate, the consent table, saved rules, the principal-model report's correction log). Success numbers are **[estimate]** thresholds to be adjusted after the first run.

**A. Policy card with break conditions and origin.** Extend the saved-rule record with: reason (one line), scope, break conditions (tripwires), origin (stated / induced-and-confirmed / induced), the case ids it was derived from, created and last-checked dates. Convert the existing saved rules to it by hand.
*How to tell it worked:* in a week, at least 80% of auto-applied items cite a policy card; a replay of the week's items against the break conditions flags at least one case the old rule would have wrongly auto-approved, or zero such cases (then the rules were already tight, which is itself a finding).

**B. Batch the queue by cluster and ask one question per cluster.** Take a week of pending items, cluster them by action class and nearest-context (as in [26]) with a plain embedding or even by hand-labelled class, and render the top cluster as a framed question per section 5. Send three such questions to the principal.
*How to tell it worked:* each answer settles at least five pending items; the principal's median time per settled item falls by at least half versus per-item review; at least 70% of the settled items, shown back a week later, are still agreed with.

**C. Retrospective induction test (the inverse-constitution check).** Hold out 20% of the principal's logged decisions. Induce a principle list from the rest (method of [25], or an LLM prompt with the same aim), then predict the held-out decisions using only the list.
*How to tell it worked:* held-out agreement against two baselines (always-ask and the existing representative). If the principle list reaches at least the representative's agreement with far fewer tokens, principles are a viable compact record; if clearly below, stay with precedent retrieval (D).

**D. Precedent view instead of a rule.** When an item arrives, show the principal (or the representative) the three nearest past decisions with the reasons given [14][26]; do not create a rule yet. Promote to a policy card (A) only when the same precedent set has been confirmed three times.
*How to tell it worked:* reduction in repeat questions on near-identical items; share of items where the principal accepts the precedent-suggested answer (target at least 85%); fewer policies created than clusters seen (precedent absorbed the rest).

**E. The inquire leg and the audit sample.** Add a fourth consent setting beside allowed / ask_first / off: `allowed_report` (act, list in the digest, easy overturn). For every active policy, put two random auto-settled cases in the digest each week with a one-tap "still agree".
*How to tell it worked:* overturn rate per policy over four weeks; any policy above about 10% overturn is re-asked with the overturned cases listed; policies never sampled are flagged stale. Failure signal: sampling is ignored (taps near zero), which means the digest has become the new source of fatigue, and the sample size should drop to one.

Suggested order: A (needed by the rest), then C and D in parallel (they test whether to compress to principles or to retrieve precedents), then B, then E.

## REFERENCES

1. [Sunstein CR, Ullmann-Margalit E. Second-Order Decisions. Ethics 110(1):5-31, 1999 (search results and working-paper PDF)](https://chicagounbound.uchicago.edu/cgi/viewcontent.cgi?article=1268&context=public_law_and_legal_theory)
2. [US Army. ADP 6-0 Mission Command (2012 and later editions); Army article "Understanding mission command"](https://www.army.mil/article/106872/understanding_mission_command)
3. [Army University Press, NCO Journal: Mission Command, a Senior Enlisted Leader's Perspective, 2020](https://www.armyupress.army.mil/Journals/NCO-Journal/Archives/2020/May/Mission-Command/)
4. [Slovic P. The construction of preference. American Psychologist 50:364-371, 1995](https://scholarsbank.uoregon.edu/items/8bfbe1ef-a008-470a-a730-625bfc00c192)
5. Payne JW, Bettman JR, Johnson EJ. The Adaptive Decision Maker. Cambridge University Press, 1993 (not retrieved; cited at second hand via [Weber and Johnson, Constructing Preferences from Memory](https://business.columbia.edu/sites/default/files-efs/imce-uploads/CDS/PAM.slovicbookclean.pdf))
6. [Schauer F. Playing by the Rules (1991), reviews and commentary](https://larc.cardozo.yu.edu/cgi/viewcontent.cgi?article=1786&context=clr)
7. [Casey AJ, Niblett A. The Death of Rules and Standards, University of Chicago](https://chicagounbound.uchicago.edu/cgi/viewcontent.cgi?article=2444&context=law_and_economics)
8. [Manheim D, Garrabrant S. Categorizing Variants of Goodhart's Law. arXiv:1803.04585](https://arxiv.org/abs/1803.04585)
9. [Gao L, Schulman J, Hilton J. Scaling Laws for Reward Model Overoptimization. arXiv:2210.10760](https://arxiv.org/pdf/2210.10760)
10. [Bain and Company: Building a Repeatable System for Effective Decisions (RAPID); summary at Umbrex](https://www.bain.com/how-we-help/building-a-repeatable-system-for-effective-decisions/)
11. [Atlassian: How to Make Effective Decisions (DACI)](https://www.atlassian.com/work-management/team-management-and-leadership/decision-making)
12. [Management 3.0: Delegation Poker (Appelo, seven levels of delegation)](https://management30.com/practice/delegation-poker/)
13. [Bodansky D. Rules and Standards in International Law (ex ante versus ex post framing); Kaplow L. Rules versus Standards: An Economic Analysis, Duke Law Journal](https://scholarship.law.duke.edu/cgi/viewcontent.cgi?article=3207&context=dlj)
14. [Aamodt A, Plaza E. Case-based reasoning: foundational issues, methodological variations, and system approaches. AI Communications 7(1):39-59, 1994](https://www.iiia.csic.es/~enric/papers/AICom.pdf)
15. [Lipsky M. Street-Level Bureaucracy (1980), overview; AI and the Transformation of Accountability and Discretion in Urban Governance, arXiv:2502.13101](https://arxiv.org/pdf/2502.13101)
16. [Keeney RL. Value-Focused Thinking: A Path to Creative Decisionmaking. Harvard University Press, 1992](https://www.hup.harvard.edu/catalog.php?isbn=9780674931985&content=toc)
17. [Edwards W, Barron FH. SMARTS and SMARTER. Organizational Behavior and Human Decision Processes, 1994; see the swing weight matrix overview](https://www.researchgate.net/publication/285402488_231_Using_the_Swing_Weight_Matrix_to_Weight_Multiple_Objectives)
18. [Fischer GW. Range sensitivity of attribute weights in multiattribute value models. OBHDP 62(3):252-266, 1995 (figures via secondary summary; see Baron on weight elicitation)](https://www.sas.upenn.edu/~baron/papers.htm/range.htm)
19. [The threat of weighting biases in environmental decision analysis. Ecological Economics (ScienceDirect)](https://www.sciencedirect.com/science/article/abs/pii/S092180090800253X)
20. [Jia J, Fischer GW, Dyer JS. Attribute weighting methods and decision quality in the presence of response error. J Behavioral Decision Making 11:85-105, 1998](https://onlinelibrary.wiley.com/doi/10.1002/(SICI)1099-0771(199806)11:2%3C85::AID-BDM282%3E3.0.CO;2-K)
21. [Bai Y et al. Constitutional AI: Harmlessness from AI Feedback. arXiv:2212.08073](https://arxiv.org/pdf/2212.08073v1)
22. [Anthropic. Claude's Constitution](https://www.anthropic.com/constitution)
23. [OpenAI. Model Spec (2025/12/18)](https://model-spec.openai.com/2025-12-18.html)
24. [Guan MY et al. Deliberative Alignment: Reasoning Enables Safer Language Models. arXiv:2412.16339](https://arxiv.org/abs/2412.16339)
25. [Findeis A et al. Inverse Constitutional AI: Compressing Preferences into Principles. arXiv:2406.06560 (ICLR 2025)](https://arxiv.org/abs/2406.06560)
26. [Gao G et al. Aligning LLM Agents by Learning Latent Preference from User Edits (PRELUDE, CIPHER). arXiv:2404.15269](https://arxiv.org/abs/2404.15269)
27. [Madras D, Pitassi T, Zemel R. Predict Responsibly: Improving Fairness and Accuracy by Learning to Defer. arXiv:1711.06664](https://arxiv.org/abs/1711.06664)
28. [Claude Code Docs: Choose a permission mode](https://code.claude.com/docs/en/permission-modes)
29. [Claude Code Docs: Configure auto mode](https://code.claude.com/docs/en/auto-mode-config)
30. [Anthropic Engineering: How we built Claude Code auto mode](https://www.anthropic.com/engineering/claude-code-auto-mode)
31. [Columbia Magazine: How the Challenger Disaster Became a Case Study of the Normalization of Deviance (after Vaughan, The Challenger Launch Decision, 1996)](https://magazine.columbia.edu/article/challenger-disaster-normalization-deviance)

Not located this session, cited from memory and to be checked before reuse: Tversky 1969 (lexicographic semiorders), Simon 1956 (satisficing), Levi 1949 (legal reasoning by example), Sunstein on incompletely theorized agreements, the US standing rules of engagement, Bass on management by exception, the RACI origin.
