# Reading the principal's facts

How a lead (any lead, not only people's) reads, uses and proposes facts about the principal: their name as written, pronouns, languages, time zone, roles, handles, bios, signature, headshot. The design is the research report `docs/research/operator-dossier-and-resource-store.md` (sections C, D and H); this page is its working rule. Numbers in brackets are entries of `sources.md`.

The frame is contextual integrity: a fact flows appropriately when the flow matches the norms of the context it came from, not because the fact is "public" or "private" in the abstract [D56]. Models told to be discreet still leak in the wrong context [D11][D12], so the rule is enforced at the boundary (a label, a read verb, an outgoing check), never by asking the model to be careful.

## Where the facts are

The host's `## equerry` block names the facts file on its `Dossier:` line. No line: there is no dossier; the lead says so under "what we would need to know" and asks for the one fact the ask needs. Records of other people are not the dossier: read them with acquaint's verbs (`acquaint who <id> --field <f>`, `acquaint brief`), never by opening their files.

## Read by field, for a purpose, only what the ask needs

1. Name the field (`name.full`, `bio.short`, `titles`), the **purpose** (the ask id and what the value is for) and the **audience** (who will see the output: the principal only, a named person, a client, the public).
2. Read that field and nothing else. While the facts file holds only `clear` and `green` fields, it may be read whole; the moment it holds anything above `green`, read single fields only (for example `rg -A6 '^bio.short:' <facts file>`) until a field-level read verb with purpose and audience exists in the store.
3. Use the value only for the stated purpose and audience. Never copy it into a memory, a log, an issue, a roster, a proposal or any file that outlives the answer.
4. Before any text leaves for a person, the outgoing check runs: `acquaint check` on the text, and `acquaint brief` with the audience for what that reader may be told.

## The labels and what each permits

The labels are the Traffic Light Protocol's words [D58], with the meanings the principal's people records already give them, so one gate serves both.

| Label | Who may receive it | Typical fields | Read rule |
|---|---|---|---|
| `clear` | anyone; may be published | display name, pronouns, public bio, headshot, public handles, current public titles | any agent, no log |
| `green` | people the principal works with | work email, scheduling link, time zone and working hours, city-level location, signature without phone | any agent, no log |
| `amber` | only people the principal fully trusts, or a named recipient the principal confirms for this message | mobile number, postal address, legal name where it differs, billing entity details, date of birth | a stated purpose; the read is logged without the value; disclosure to anyone not cleared needs the principal's confirmation |
| `red` | the principal and the agents acting for them; never in outgoing content | travel plans and current exact location, private reasons behind availability, which collaborator may know what | allow-listed agents only; logged; the outgoing check scans for these values |
| never | not stored | see the list below | a pointer the principal resolves on their own surface |

A lead that needs an `amber` or `red` value for an ask does not read it: it names the field, the purpose and the audience under "what we would need to know", and the calling session asks the principal.

## Never stored

- Credentials of any kind: passwords, API keys, tokens, security questions and answers.
- Government and financial identifiers: passport and ID numbers, tax identifiers, social security numbers, bank account numbers and IBANs, card numbers.
- Special categories [D15]: health, racial or ethnic origin, political opinions, religious or philosophical beliefs, trade-union membership, genetic and biometric data, sex life and sexual orientation.
- The trap in routine answers: dietary requirements, accessibility needs and emergency contacts look harmless but can reveal special-category data or are about another person. Store "will send requirements directly" rather than the reason; an emergency contact is a link to that person's own record.

A form that needs one of these gets a pointer (a reference to the principal's password manager or vault), resolved only by the principal.

## The shape of a fact

Every field is `{value, label, source, recorded}`, optionally `{valid_from, valid_to, review_by, supersedes, contexts}`, and `confirmed: true | false`. Names follow JSContact where it has one (`name.full`, `name.components`, `speakToAs.pronouns`, `preferredLanguages`, `titles`, `organizations`, `onlineServices`) [D6]. Bios come in lengths (one line, about 50, 120 and 300 words) and per audience. A value an agent condensed (a short bio from a long one) is a proposal and stays `confirmed: false` until the principal confirms it.

## How an agent proposes a fact

Agents propose; only the principal promotes.

1. Return the proposal as text in the answer, never as an edit:

   ```yaml
   field: titles
   proposed_value: "<the value>"
   label: clear            # the label you suggest; the principal decides
   source: "<URL, or message reference with date>"
   observed: 2026-11-09
   why: "<one line: what ask needed it and where you saw it>"
   supersedes: "<the current value's field and date, or none>"
   ```

2. The calling session records it as an observation in the principal's record (acquaint's `remember`), not as a fact.
3. The principal confirms, edits or rejects it; only then does it become a field with `confirmed: true`.
4. A proposal never carries a value from the "never stored" list, and never an `amber` or `red` value copied from somewhere else.

## Data minimisation

Read the fewest fields for the narrowest purpose; keep nothing after the answer [D60]. A lead that finds itself loading the whole facts file "for context" has the wrong procedure: name the one field it needs.
