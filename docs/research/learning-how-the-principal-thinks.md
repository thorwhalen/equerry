# Learning how the principal thinks: synthesis

*2026-11-18 · scope: decision policies, communication, decision saturation, learning meta-preferences · requested for: the part of equerry that learns how its principal decides, so agents ask less, ask better, and act alone where the principal would have agreed.*

## Summary and recommendation

**The problem.** A principal who runs dozens of agent sessions across several projects receives more decisions than they can think about. Each one arrives as "A or B?" with a rationale written in the agent's vocabulary. When the volume is high the principal approves without reading. The oversight then becomes a ritual, and it trains the principal to read less. Asking for more care does not fix this. The fix is to ask *fewer, better-framed* questions, most of them at the level of a principle, and to learn from every answer [1][3].

**What the four reports agree on:**

1. **Decide classes, not cases.** The durable unit is a *second-order decision*: intent plus bounds. That means the trade-off ordering, the hard floors, and the tripwires that force escalation. Each comes with a scope, an origin (stated, or induced and confirmed), and a review trigger [1]. Ask a policy question as one trade-off between things the principal already understands, with the concrete cases it would settle listed under it [1][2].
2. **Model what the principal trades off, not only what they chose.** A small set of named axes (speed against safety, privacy and legality against velocity, polish against effort), each with a weight, a hard floor where it is not negotiable, and links to the cases behind it [4]. The record is a short, versioned document the principal can read and edit. Every line carries its provenance, its scope, its strength and when it was last confirmed [4].
3. **Write every decision twice, in one record.** A principal layer gives the situation in their terms, the tension, the recommendation and why it is safe, what each alternative would cost in things they can observe, and the standing rule that would stop the question recurring (the meta-solution). An agent layer carries the full rationale for other agents, for reading on demand, for an audio overview or for a discussion with the secretary. Both are rendered from the same structured record. A per-reader lexicon of known and unknown terms decides what gets a gloss and what technical detail can stay [2].
4. **Measure blind approval directly.** A high acceptance rate cannot tell "the recommendations were right" from "nobody read them" [3]. Only performance on items known to be wrong (seeded canaries, with the principal's prior consent) and behavioural signals measure the principal's detection ability. Those signals are decision latency against reading time, expansions opened, run length, and consistency on repeated items. Ground the design in vigilance and repetition effects, which replicate, not in "decision fatigue", whose evidence is weak [3].
5. **Widen autonomy per class, on evidence, reversibly.** Before any representative acts on its own, run it in shadow mode, citing the record lines it used. Calibrate on held-out real decisions and watch for drift. Promote one class at a time to "act, then report, with an audit sample". Demote it automatically after repeated rejections [3][4].

**The principle under all five:** protect what is not negotiable (security, privacy, legality) with automated checks that hold only what they flag, rather than with approvals the principal stops reading. Spend the principal's attention on decisions where they hold information or authority the system lacks [3].

## What already existed

- [principal-model-and-review-organisation.md](principal-model-and-review-organisation.md) [5]: the representative is a veto predictor, and the gate fires on reversible-by-rule plus low disagreement risk. It covers learning from corrections and the limits of digital twins. The four reports below extend it and do not repeat it.
- [briefs-and-attention-triage.md](briefs-and-attention-triage.md) [6] and [voice-as-a-layer.md](voice-as-a-layer.md) [7]: brief ranking, alert budgets, voice as an adapter.
- [../architecture.md](../architecture.md) and [../consent-table.md](../consent-table.md): the learning loop (overrides go to the record, and a reviewed consolidation updates it) and saved, revocable consent rules. A saved rule is already a policy. What is missing is the induction step (finding the cluster that one answer would settle) and the audit step (sampling what a policy settled).

## The four reports

| Report | Main finding |
|---|---|
| [principal-decision-policies.md](principal-decision-policies.md) [1] | A policy is intent plus bounds plus break conditions, with an origin, a scope and a review trigger. Ask it as one trade-off with its cases listed. Add an "act, then report" setting and an audit sample. |
| [communicating-with-the-principal.md](communicating-with-the-principal.md) [2] | Frame the problem, not the options. Model the reader's vocabulary, not their "level". Keep one record with two layers and several renderings. Check the short version against the full one, because simplification drops trade-offs and personalisation raises sycophancy. |
| [decision-saturation.md](decision-saturation.md) [3] | Acceptance rate is not a measure of attention. Use canaries and behavioural telemetry, a daily budget with an overflow digest, an explicit value-of-asking rule, and "decide first, then see the recommendation" for the few highest-stakes items. |
| [learning-the-principals-meta-preferences.md](learning-the-principals-meta-preferences.md) [4] | Use trade-off axes with floors over a precedent library. Treat induced principles as hypotheses, confirmed on cards. A 40-minute adaptive trade-off interview seeds the record. Score the representative in shadow mode with drift detection before widening autonomy. |

## Where the reports pull in different directions

- **Principles or precedents?** [1] and [4] both propose testing whether a compact principle list predicts held-out decisions as well as retrieving the nearest past cases. Neither can settle this in advance; the override log decides. Build the record so that it holds both (a principle line cites its cases).
- **Friction or volume reduction?** Cognitive forcing ("decide first") works, but it costs time and people dislike it [3]. Use it only on the two or three highest-stakes items a day and on canaries. Everywhere else, cut volume.
- **Personalise, or not?** Tailoring wording to the reader helps comprehension [2], but remembered profiles raised sycophancy the most in one study [4]. Keep the *framing* personal and the *recommendation* independent of the principal's last stated view, and probe for that (the same item, with and without that view).

## Approaches to try, in order

Each pilot takes about a week, and each has a stated test for whether it worked (details in the reports).

1. **Baseline, with no change to the interface** [3][4]. Log decision latency, expansions, run length and acceptance by class. Run the representative in shadow mode on every item, naming the record lines it used. Everything after this is measured against it.
2. **Decision card v0 and lexicon v0** [2]. The two-layer record for the three commonest decision types, phone rendering first, a standing-rule offer on every card, and a term linter fed by the reader's lexicon. It worked if the time to decide and the number of clarification questions fall against the old format.
3. **A 40-minute trade-off interview** [4]. 20 to 40 dilemmas made by changing one attribute of real recent items, chosen adaptively, plus an ordering of the axes and a set of "never" statements. It seeds the axes, weights and floors. It worked if it predicts held-out decisions at least as well as the same number of logged overrides.
4. **Policy questions over clusters** [1][2]. Group pending items by class, ask one trade-off question per cluster with its cases listed, and save the answer as a policy card (intent, bounds, break conditions, origin, scope, review date). It worked if each answer settles five or more items and they still hold a week later.
5. **Act then report, with audit and demotion** [1][3]. A fourth consent setting beside allowed / ask first / off. Promote one high-volume, reversible class. Put two settled cases per policy in the weekly digest for a one-tap "still agree". Demote after repeated rejections.
6. **Canaries and decide-first** [3]. Only with the principal's consent: one or two seeded known-bad items a day, revealed quietly afterwards, giving a weekly detection rate per class. "Decide first" applies to the top two or three items a day.
7. **Principle induction cards** [4]. Compress the last 100 to 200 corrections into at most 15 candidate principles. Each card shows the supporting cases, the counter-cases and the held-out test, and the principal answers them all in one 20-minute sitting.

## What this means for the build

- **The record** (the representative's source) gains a line schema: kind (precedent, principle, axis, floor, lexicon term), scope, strength, sources, supersedes, last confirmed, status. It lives where the principal's record already lives, and only the principal confirms a line.
- **The item** ([../item.md](../item.md)) gains a principal layer and an agent layer (`ask`, `why_now` and `decision.recommendation` are most of the first), a `standing_rule_offer`, and the record lines the representative cited.
- **The consent table** ([../consent-table.md](../consent-table.md)) gains policy cards as the form of a saved rule (adding origin, break conditions and review date), the act-then-report setting, and audit sampling. Floors that can be checked mechanically, such as what may go public, are enforced by a check that holds only what it flags, not by an approval.
- **The brief** gains a daily budget, an overflow digest, dedupe of repeats, and the weekly audit and canary lines.
- **New metric** beside the existing ones (false auto-approve rate, items shown per day, override rate): the principal's detection rate on canaries, per class.

## Open questions

- How many labelled decisions exist today? Under about 100, the interview (pilot 3) comes before the fitted models.
- Does the principal consent to canaries, and to which classes?
- What is the right daily budget? The reports give only order-of-magnitude anchors [3]; set it from the principal's own latency distribution.
- Is the representative's current record dense enough on trade-offs, or mostly about style? Pilot 1's cited-line relevance answers this.

## References

1. [Deciding at the level of a principle: how one standing decision settles many concrete ones](principal-decision-policies.md). equerry research, 2026-11-18.
2. [Communicating decisions to a time-poor principal in the language they know](communicating-with-the-principal.md). equerry research, 2026-11-18.
3. [Decision saturation: when the human approves blindly, and how to design against it](decision-saturation.md). equerry research, 2026-11-18.
4. [Learning one person's decision-making model: meta-preferences, principles from cases, scrutable models, elicitation and evaluation](learning-the-principals-meta-preferences.md). equerry research, 2026-11-18.
5. [A principal model and a review organisation for a solo operator of many coding agents](principal-model-and-review-organisation.md). equerry research, 2026-11-09.
6. [Executive briefs and attention triage from many sources](briefs-and-attention-triage.md). equerry research, 2026-11-09.
7. [Voice as a layer over a text organisation core](voice-as-a-layer.md). equerry research, 2026-11-09.
