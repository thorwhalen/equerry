---
name: equerry-council
description: "Convene several team leads of the equerry organisation on one question, always with the contradictor: neutral framing, a blind parallel first round, a confrontation round, and a synthesis that keeps the disagreements, names the strongest objection and the missing facts, and leaves the decision to the principal. Use for questions that touch several domains or commit money, time, rights or people: 'ask the council', 'what do the teams think', 'get the experts together', 'make them argue', 'second opinion from everyone', launching an offer or product, taking a role, a fundraise, a pivot, any decision that is hard to undo."
metadata: {equerry: {team: all, class: mechanic, origin: human, origin_ask: "docs/teams.md", version: "1", status: active}}
---

# equerry-council: several leads, one question, the disagreements kept

The council is several leads answering the same question, independently first, then against each other. It exists because a single lead misses what another domain sees, and because several leads simulated by one model easily become one voice in several costumes. The procedure below is built against that.

The leads are points of view with their domain skills and references, not licensed professionals. Never present the synthesis as professional advice.

## Procedure

1. **Frame neutrally.** Three lines: what is to be decided, the known constraints, what is already decided (pointers); then name the **owner team** (the one whose domain the decision mostly sits in; its coverage log gets the line at step 6) and give the question an ask id in that team's format (`equerry-team`, step 1). If the question matches any chosen lead's escalation table or is a one-way door, write `Forced: escalate` for that lead, as `equerry-team` does. Phrase it as a question ("does this offer hold economically?") or as a third party's proposal, **never with the enthusiasm it was born in**: the leads receive the framed question, not the conversation where the idea came up. This is the strongest single protection against complacent answers.
2. **Pick two to four leads, plus the `contradictor`, always.** Choose the leads that have something specific to say, not every team. **`legal` is always on the council when any candidate involves selling to people, children, personal data or a licence**, whoever drew up the member list: a brief or plan that names the members without legal does not override this. Include `legal` also as soon as something is promised, signed, published or crosses a border; include `finance` as soon as money or tax is involved. More leads is not a better answer.
3. **Blind first round, in parallel.** One Agent call per lead and one for the contradictor, all in the same message, each with the same framed question and the same pointers (paths, venture pages, issue URLs). Each lead returns the coverage block and the advice reply of `equerry-lead/references/reply-shape.md`; the contradictor answers in its own shape (its agent file: my view, the three best objections, what we would need to know, sources), with no coverage block, since it has no letter or roster. They do not see each other's answers: that is the point. Leads that judge the ask outside their letter say so in their coverage block; drop them from round two.
4. **Confrontation round.** Give the `contradictor` a summary of the first-round answers (each lead's view and main reservation, one line each) and ask what they all left out and where their agreement is too easy. If two leads contradict each other on a point that matters, send each the other's argument, once, with the fact it named in round one as the one that would change its view, and ask whether that fact is now present, and whether the argument changes its view and on what evidence. A concession that cites neither that fact nor new evidence is reported as **conceded without new evidence**. When both leads concede to each other that way (the sides swap), the synthesis keeps both first-round positions; a one-sided concession is kept, labelled as moved by argument. One exchange; no further rounds.
5. **Synthesis**, shorter than the opinions put together:
   - where the leads agree, and on what evidence;
   - **the disagreements**, named, with each side's argument; never smoothed into a compromise nobody proposed;
   - **the strongest objection** (usually the contradictor's), stated in its strongest form;
   - the facts or figures missing, and **at most two questions** for the principal;
   - the session's recommendation, labelled as such, and what remains to verify;
   - for a heavy decision (significant money, legal status, rights, health or safety, anything hard to undo), **whom to consult for real**: which licensed professional or experienced peer, because leads simulated by one model share blind spots.

   **A council that does not converge is a valid outcome.** Do not force a recommendation the leads did not reach: record the strongest objection as the deliverable's first line, then the disagreements, and leave the choice to the principal.
6. **Nothing is decided until the principal decides.** Present the options; record the decision with the disagreements where the host keeps decisions. Append one coverage line for the owner team named at step 1, per `equerry-team` (its `cost` counts every call of the council); each other lead's answer is not logged separately.

## Prompt for each lead (round one)

```text
Council question <id>: <three neutral lines>
Owner team: <team>. Forced: <escalate | none> (per lead).
Already decided: <pointers or none>. Pointers: <paths, pages, URLs>.
Instance: Teams <path>; Team records <path>; Dossier <path or none>; Venture pages <path or none>.
Pasted: <that lead's team's last 20 coverage lines, and any read-only output the question needs>
You are one of several leads answering independently; you will not see the others in this round.
Reply per equerry-lead/references/reply-shape.md (coverage block, then the advice reply), at most 300 words. Under "What we would need to know", name the one fact that would change your view.
```

The contradictor gets the first four lines and: "Answer in your own shape, at most 300 words."

## Signs the council went wrong

- **All agree, without reservation.** Several leads on one model often converge; treat unanimity as a reason to re-run the contradictor with "what would have to be true for this to fail?"
- **Generic answers** that would fit any founder's question: the framing lacked pointers, or the leads did not read their references. Re-frame with the facts and run again.
- **The synthesis is longer than the opinions**, or it introduces a view no lead held: cut it back to what was said.
- **Two leads swapped positions** in the confrontation round without new evidence: no movement (step 4).
- **A lead answered outside its domain** and the others deferred to it: note it; the owner team's view on its own domain wins.
- **The question changed during the council**: stop and re-frame; do not answer a question the principal did not ask.

Note any of these in the reply to the principal, in one line, so the next council can be run better.
