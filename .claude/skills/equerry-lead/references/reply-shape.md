# Reply shapes

Every lead reply has one of three shapes after the coverage block: the **advice reply** (the work), the **escalation brief**, or, in review mode, **findings** in the opinion schema. The shared staff (contradictor, checker, navigator) have their own short shapes in their agent files. A reply that does not follow its shape is sent back.

## 1. The coverage block (every lead reply starts with it)

Judged from artefacts only: the roster, the coverage log, each primitive's last validation, the references' dates and jurisdictions, the dossier fields and venture pages the ask needs, the letter. Never from the lead's sense of its own competence.

```text
Coverage: <covered | covered-with-gaps | train | hire | escalate>
- Procedure: <the primitive that holds the steps, by roster id, or "none">
- Quality: <the primitive's last validation (date, model, score); or, for a human-seeded primitive never validated, the named checklist or test the checker will run at step 7; or "none">
- Knowledge: <the references used, with their dates and jurisdictions; what is stale (older than its re-verify interval) or missing>
- Context: <the dossier fields and venture pages needed: present, missing, or not needed>
- Authority: <the letter's right that applies (may decide / recommends / agree / never) and whether the escalation line is crossed>
Primitives: <roster ids, or none>
Similar: <coverage ids of earlier asks this one resembles, from the pasted lines, or none>
Candidate: <for covered-with-gaps: what would have closed the gap, one line (from "what bothers me"); otherwise none>
Consult: <team>: <one neutral question>     (zero or more lines; the session turns each into an ask to that team)
```

Five dimensions, one evidence line each. A dimension with no artefact behind it is a gap, whatever the lead believes. For quality, a human-seeded primitive (`origin: human`, never validated) with a named checklist counts as an artefact: the checker's pass against that checklist at step 7 is its validation, and a check still failing after two rounds leaves the work unverified (its entry in the coverage line's `quality` list shows `pass` below `total`). The verdict follows from the lines: all five hold, `covered`; a gap that research can fill once, `covered-with-gaps`; a gap a roster primitive could close with a delta, `train`; a gap no primitive covers, `hire`; authority crosses the escalation line or the letter's Never list, or the prompt says `Forced: escalate`, `escalate` (wins over all others). Another team's domain is a `Consult:` line, never an `escalate`.

## 2. The advice reply (the work)

At most 400 words unless the ask is for a document. In this order:

1. **My view**: the recommendation, plainly, in one to three sentences. A view, not a survey of options.
2. **What bothers me**: reservations and disagreements, including with the principal, another team or an earlier decision. If there are none, say why in one sentence; a view without reservations is rare.
3. **What we would need to know**: the missing facts or figures, and **at most two questions** for the principal, each answerable in a line.
4. **Sources**: numbered `[1]`, `[2]`, each with its title, URL or path, and the date checked. A claim with no source is marked interpretation; there is no "experience" source.

**Dossier facts are cited by field name, never by value**, unless the dossier labels the field `clear`: write `dossier: working_hours (present)`, not the hours. The session pastes your answer into deliverables that last and get published; a value there outlives the purpose it was read for. The same holds in the coverage block's Context line and in an escalation brief.

**Every claim carries one of three marks:**

| Mark | Meaning | Written as |
|---|---|---|
| certain | backed by a source checked today or within its re-verify interval | `(certain [2], checked 2026-11-09)` |
| to verify | plausible, not confirmed; says with whom or where to confirm | `(to verify: with the accountant)` |
| interpretation | the lead's own reading or judgment | `(interpretation)` |

In doubt between two marks, use the more prudent one. A number that comes from a calculation shows the calculation (a script or a one-line expression), and every estimate is labelled as one.

## 3. The escalation brief

When the ask crosses the escalation line, the deliverable is a brief for the principal and the professional, never an answer to the underlying question. Explaining, organising and drafting for the principal's own use stay allowed; acting as the professional of record does not.

```markdown
**Escalation brief: <one-line subject>**  (team: <team>, ask <id>)
- Facts: <what is known, each with its source; what is assumed, marked>
- Question for the professional: <one or two precise questions>
- Documents: <what to send, by path or name; what is missing and who holds it>
- Deadline: <the date and why; the consequence of missing it>
- Professional: <which kind (e.g. corporate lawyer, chartered accountant), in which jurisdiction; named if the letter names one, otherwise "to be named">
- Meanwhile: <what the principal may safely do or not do until the answer>
```

## 4. The opinion schema (review mode)

When a lead reviews a plan or a diff for its concern, it returns findings only, ranked, no summary of what it read and no rewrite:

| Field | Values |
|---|---|
| severity | `blocker` (must change before it ships), `major` (should change), `minor` (worth a look) |
| where | `file:line`, a section heading, or a plan step |
| rule | the checklist item it breaks, as `equerry-<team>/references/checklists.md#<item>` or the reference line |
| fix | one line: what would make it pass |

```text
major | docs/offer.md:§Pricing | equerry-finance/references/checklists.md#offer-pricing (price computed by script) | replace the hand-written total with the script and its inputs
```

No finding: say "no findings against <checklist>" and stop.
