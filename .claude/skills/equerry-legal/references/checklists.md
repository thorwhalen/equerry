# Legal team: checklists

Four procedures the legal lead runs and the `checker` verifies against. Each section ends with "Done when", the line the checker tests. Numbers in brackets are entries of `sources.md`. Client and venture names never appear in a public artefact; inside the instance they are referred to by the code of their venture page.

## Chain of title

Per asset: a product, a repository, a body of content (course material, docs, designs), a dataset, a trademark. Quarterly, and before any fundraise, sale, licence change or spin-off.

1. List the assets from the venture pages' sources (repositories, products, published material). One row per asset.
2. For each repository, list everyone who contributed: `git shortlog -sne` over its full history, plus authors of material outside git (designs, recorded courses).
3. For each author, record their status **at the time they wrote it**: founder before incorporation, employee of which entity, contractor, client staff, open-source contributor, the principal acting for a client.
4. For each author, the agreement under which the work was done, its date, and whether it contains an assignment clause (quote the clause location, never paraphrase from memory).
5. Apply the default rules, labelled by jurisdiction:
   - `[FR]` software written by an employee in the course of their duties vests in the employer automatically; contractor code and code written before the company existed do not, and need a written assignment [29]. The form an assignment must take is to verify with an avocat.
   - `[US]` an invention-assignment agreement transfers what it says, within state limits; California excludes inventions made on the employee's own time and equipment and unrelated to the employer's business [28]. Other states' limits: to verify with counsel.
   - Work done for a client: who owns it is whatever the MSA or SOW says; without a written term, mark it **to verify** with a lawyer, never assume.
6. Third-party code and content: licence of each dependency or asset that is shipped, and whether it is compatible with the asset's own licence (with the engineering team's licensing check).
7. Contributions from outsiders: under the DCO or a CLA (engineering's check), or neither.
8. Current owner of the asset, as the documents establish it, and the gap if they do not.
9. For each gap, the action: an assignment to obtain, a contribution to remove or relicense, a question for counsel.

Output: one table per asset with columns `author | status then | agreement and date | assignment (where) | jurisdiction | owner per documents | gap | action`.

Done when: every author of every listed asset has a row, every row names a document or says "none found", and every "none found" has an action.

## Conflict check

Before any new client, a new role in a venture, a venture entering a new field, or consulting work in a field where a venture operates. Yearly as a sweep.

1. From the venture pages, list the principal's roles: director, officer, employee, contractor, advisor, shareholder, client lead. Directors and officers owe duties to each company.
2. For each venture: its field and customers; any non-compete, exclusivity or non-solicitation binding the principal; the scope of the principal's IP assignment (does it reach side work in the same field?); whether its charter or board renounced classes of corporate opportunities, and where that is written `[US]` Delaware [30][31].
3. For the new engagement: its field, its likely customers, the IP it will produce, who will own it, and what background IP the principal brings.
4. Cross the two: for each pair (new engagement, existing role), mark `none`, `manageable with disclosure or consent`, or `blocking`, with the clause or duty that decides it.
5. List the disclosures and consents needed, and from whom.
6. Any question about a director's duty, or any `blocking` cell: escalate to the venture's company counsel.

Output: a matrix `engagement x role` with the deciding clause per cell, the list of disclosures and consents, and the recommendation.

Done when: every role on every venture page appears in the matrix, every non-`none` cell cites a clause, a charter provision or "to verify with counsel", and no `blocking` cell is left without an escalation.

## Contract register

One row per agreement, across every entity, venture and client. Swept monthly: notices falling due within 60 days become items.

| Field | What goes in it |
|---|---|
| `id` | a stable id, e.g. `<entity>-<yyyy>-<n>` |
| `entity` | which of the principal's entities (or the principal personally) is party |
| `counterparty` | the venture page code or an internal id; never a client name in a public artefact |
| `type` | MSA, SOW, NDA, licence (in or out), advisory, employment, contractor, SaaS terms, DPA, shareholder or partnership agreement |
| `signed`, `effective` | dates |
| `term`, `renewal` | end date; automatic renewal yes or no |
| `notice` | notice period and the computed last day to give notice |
| `exclusivity` | non-compete, exclusivity, non-solicitation: scope and duration |
| `ip` | who owns the work product; background IP listed or not; licence back |
| `confidentiality` | scope and how long it survives |
| `data` | personal data processed; controller or processor; DPA present |
| `liability` | cap, indemnities |
| `law` | governing law and venue |
| `money` | fees and payment terms (the finance team reads this) |
| `termination` | triggers and what survives |
| `location` | where the signed copy is (a path or a pointer in the instance) |
| `last_reviewed` | date and by whom |

Done when: every agreement the venture pages point to has a row, `notice` is computed for every agreement with a term, and every `ip` and `exclusivity` cell is filled or says "not addressed".

## Regulatory watch

Per product, quarterly and before each launch. Output: a one-page delta per product: what changed since the last check, which rule, its level (certain, to verify, interpretation), the action and its date.

1. **Profile the product**: users (adults, under-13 users in the US, students through schools), personal data processed, AI features (does it generate content, does it interact with users as an AI), open source (released, monetised by a venture), app stores, paid training.
2. **Personal data** `[EU]` `[FR]`: a register entry on the CNIL template [32]; a privacy notice that matches the flows; any new flow since the last check.
3. **Children** `[US]`: under-13 users trigger COPPA; the amended rule (compliance date 22 April 2026) adds separate parental consent for disclosure to third parties for targeted advertising, a written information-security programme and retention limits [33][34].
4. **Schools** `[US]`: a tool sold to US schools that handles student records meets FERPA [57].
5. **AI features** `[EU]`: AI Act Article 50: people are told they are interacting with an AI system, and generated content is marked, from 2 August 2026; systems already on the market have until 2 December 2026 for the labelling; Article 4 requires AI literacy from providers and deployers [37]. Marketing claims about AI go through LEG-7.
6. **Open source** `[EU]`: free software developed outside a commercial activity is out of CRA scope; a legal person supporting software intended for commercial use is an open-source steward (a cybersecurity policy, cooperation with authorities, reporting duties from 11 December 2027, no fines); a venture that monetises the software may be a manufacturer, with vulnerability reporting since 11 September 2026 [35][36]. Write a scope memo per project when a venture monetises it.
7. **Re-check the sources** for each rule used: fetch, compare with the last-checked text, date the check.

Done when: every product has a profile, every applicable rule above has a line with its level and source, and every source used was checked within the quarter.
