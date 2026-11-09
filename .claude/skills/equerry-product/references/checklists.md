# Product checklists

Each checklist is something the `checker` sub-agent can verify a deliverable against: every item is a yes or no with the evidence that makes it yes. Numbers in brackets point to `sources.md`. Values marked "default" are the framework's; an engagement letter may set its own. Last checked 2026-11-09.

## 1. Bet pitch with appetite and kill-or-continue date

After Shape Up's pitch [43], with the kill line added for a portfolio founder whose hours are the scarcest resource.

- [ ] **Problem**: one situation a specific user is in, with evidence of what they do today (an interview, usage data, a support thread), dated.
- [ ] **Appetite**: how much time this is worth, as a fixed time box (default: a small batch of two weeks or a big batch of six), chosen before the solution.
- [ ] **Solution**: the elements at the level of a sketch (breadboard or fat-marker), enough to see it fits the appetite, not a specification.
- [ ] **Rabbit holes**: the parts that could blow the time box, each with how the pitch avoids it.
- [ ] **No-gos**: what is deliberately left out of this bet.
- [ ] **Riskiest assumption** and the cheapest test of it, run before or at the start of the build.
- [ ] **Success at the date**: the observable result that means "continue", stated as a number or a user behaviour.
- [ ] **Kill-or-continue date** written, and the **kill signals** that end the bet before the date.
- [ ] **Cost**: the principal's hours and any money, computed with `python -c`, inputs labelled estimate.
- [ ] **Odds, fit and enjoyment** scored when bets compete (each 1 to 5, a reason per score, labelled interpretation), so the principal can compare on the same axes.
- [ ] **Live bets**: this one fits under the letter's limit on concurrent bets (default: two).
- [ ] **Routed**: legal for users' data and children; engineering for the dispatch brief; bizdev for who would pay.

## 2. Interview guide on past behaviour

After The Mom Test [46]: talk about their life, not the idea; ask about specifics in the past, not generalities or the future; listen more than talk.

Before:

- [ ] The learning goal: the one or two things this interview must find out, tied to an opportunity on the tree.
- [ ] Who: a person in the problem's situation, by role; consent to take notes (and to record, only when legal has cleared it).

Questions (adapt; never pitch):

- [ ] "Tell me about the last time you <did the thing / met the problem>." Then: what happened, what did you try, what did it cost you (time, money, risk).
- [ ] "What else have you tried? What do you use now?" (the competitive alternatives).
- [ ] "Why was that a problem? What did it stop you doing?" (the size of the pain).
- [ ] "Who else is involved when this happens? Who decides about spending on it?"
- [ ] "Is there anything else I should have asked?" and "Who else should I talk to?"

Avoid, and mark in the notes when it slipped in:

- [ ] Opinions about the idea ("would you use", "do you like"), hypotheticals about the future, compliments, and leading questions.

After:

- [ ] Notes separate **facts** (what they did, with specifics), **opinions** (what they think or would do) and **commitments** (time, money, an introduction, a pilot); only facts and commitments count as evidence.
- [ ] The tree updated (checklist 3); the evidence dated and linked to the person's record in the instance, never in a public file.

## 3. Opportunity solution tree

After Teresa Torres's method [44].

- [ ] **Outcome** at the root: one measurable outcome the product team can influence, with its current value.
- [ ] **Opportunities** under it: user needs, pain points and desires, each found in interviews or data (with the source), worded from the user's side, not as features.
- [ ] Opportunities broken into smaller ones until one can be addressed within an appetite.
- [ ] One **target opportunity** chosen, with the reason (size, frequency, fit with the principal's strengths).
- [ ] At least three **solutions** considered for the target, so the first idea is compared, not assumed.
- [ ] **Assumption tests** under each candidate solution (desirability, viability, feasibility, usability), the riskiest first, each with the cheapest test.
- [ ] The tree dated and revised after each round of interviews; branches pruned when evidence kills them, with the reason kept.
