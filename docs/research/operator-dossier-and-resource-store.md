# The principal's dossier: a store of facts and resources about the one person an organisation of agents works for

Research report, deep tier, 2026-11-09. Design question only: no private material was used, and no fact about the actual principal appears here.

## Summary and recommendation

**The question.** An organisation of agents keeps asking the person it works for the same things: how their name is written, which photo to use, which bio, what time it is where they are, which title goes on this form, what the company's billing details are. Where should those answers live, in what shape, who may read which of them, and how do they stay current? This is the *facts and resources* layer. The principal's *preferences* (how they decide, what they object to) are already modelled in their acquaint record and are covered by the phase-1 report [1]; this report does not redo that.

**Verdict in one paragraph.** Do not build a new store. Make the principal's dossier **the one person record in the acquaint store that is marked as the operator's own**, and extend acquaint, not equerry, with three things every record can use: a **typed facts file** in which every field carries a disclosure label, a source and a date; an **asset manifest** whose entries point by key into a blob store behind a mapping interface, so the bytes can move from a folder to object storage without changing a reference; and a **field-level read verb** that returns one value for a stated purpose and audience, refuses what the audience may not see, and logs reads of the restricted labels. Every mainstream precedent keeps the owner's card in the same store as everyone else's but behind its own permission (Android's profile contact needs `READ_PROFILE` [2], Apple's Contacts designates one ordinary card as "My Card" [3], Solid keeps the owner's profile and a private preferences file in the same pod [4]); acquaint already has the identities, affiliations, sources, Traffic Light Protocol labels, audience ceilings and lint tripwires this layer needs [5]. Use JSContact (RFC 9553) property names wherever one exists, so export to vCard, h-card or schema.org is a mapping, not a design [6][7].

**The three strongest recommendations.**

1. **One record, typed fields, a label on every field.** A profile document (one current value per field, edited in place) rather than a collection of extracted memories, because the facts in question are few, stable, and must be exact; the agent-memory literature names this split and its trade-offs [8]. Each field carries `label` (`clear`, `green`, `amber`, `red`, reusing acquaint's words), `source`, `recorded`, and where it changes, `valid_from`/`valid_to` and `review_by`; the newest statement supersedes the old one, which is kept, not deleted (the bitemporal pattern Zep uses for facts [9]).
2. **Agents read one field for one purpose, never the whole dossier.** Minimal disclosure is the one mitigation with strong evidence: an agent that sees only the data its task needs resisted context-hijacking 97% of the time against 45% for one that saw everything [10], and models leak private information in contexts people would not in 26–57% of cases even when told to be careful [11][12]. The read verb takes a purpose and an audience, returns the value or a refusal with the reason, and logs every `amber` and `red` read.
3. **Agents propose; only the principal promotes.** Agents append sourced observations ("the conference form asked for a 50-word bio; this one was used") to the record's log; a fact enters or changes in the profile only by the principal's statement or confirmation. Persistent memory written from untrusted content is an attack path with working exploits [13][14].

**What never goes in.** Credentials, one-time codes and security answers; government, tax and financial account identifiers; special-category data (health, religion, politics, ethnicity, sexuality, union membership, biometrics, including voice samples) [15]; facts about other people (they get their own record). Where a form needs one of these, the dossier holds a **pointer** to the principal's password manager (for example a `op://vault/item/field` secret reference [16]) that only the principal's own surface resolves.

**Biggest risks.** Scattering (the same fact copied into many per-project memories and drifting), staleness (an old headshot or title used confidently), leakage through outgoing text, poisoning through agent-written memory, and scope creep into a life log.

## What already exists in the fleet

- **acquaint** holds records of people, projects, orgs and groups as hand-editable Markdown outside any repository: `PROFILE.md` with identity frontmatter, `identities.yaml` (handles with evidence), `links.yaml` (affiliations with role and period), `rules.yaml` (channel rules), `style.md`, `views.md`, `sources.md`, an append-only `log/`, and `trust.yaml` [5]. Every preference needs a source and `lint` fails without one; `POLICY.md` and `lint` tripwires already forbid special-category data, identifiers and credentials [5]. Its disclosure module orders labels `red > amber > green > clear`, gives each reader a clearance from the tier the operator granted them (`open` sees up to `amber`, `involved` up to `green`, everyone else `clear`), caps an audience by scope (`public` → `clear`), and fails closed on anything it cannot read (read from the package source, 2026-11-09). The `who NAME -f FIELD` verb already returns one frontmatter field.
- **What acquaint lacks for this theme**: a designation of which record is the operator's own (the disclosure module recognises an `is_self` reader in an audience, but the store does not record which entity that is); typed fields with per-field labels and dates in a structured file (labels exist per fact line in Markdown); a place for binary assets; and a log of reads.
- **The principal's preference model** (views and standing objections, writing style) is in the same record, per the phase-1 report [1]. The dossier references it and never duplicates it.
- **Claude Code's own memory** stores `user`-type notes ("your role, expertise, and working preferences") in auto memory, but auto memory is per repository and machine-local [17]. In a fleet of many repositories that means the principal's facts are re-learned and re-stored once per project and per machine: the scattering this report is meant to end. The user-level `~/.claude/CLAUDE.md` is loaded everywhere but is instructions, not a field store [17].
- The prior-art report found that the one open-source chief-of-staff kit keeps identity, style and constraints in `CLAUDE.md` [18]: workable at one machine and one repository, not at a fleet.

## A. What agents ask their principal for, again and again

No systematic study of repeated requests from agents to their principals was found; this section infers the set from what vendors and users built to stop the asking. That is evidence of demand, not a measurement of frequency.

- **Cross-project memory is a standing request.** Claude Code users have filed feature requests for memory across projects and at account level, listing "user preferences" among what is lost between sessions and describing manual files kept as a workaround [19][20][21].
- **Vendors converged on the same few profile fields.** ChatGPT's personalisation asks what it should call you, what you do, and anything else it should know, separately from saved memories and chat-history reference [22][23]. Claude's memory summary centres on "role, projects, and professional context", with a separate memory per project and an incognito mode that writes nothing [24][25]. OpenClaw splits a `USER.md` (stable preferences, communication style, relationships; dated active or superseded directives; its own 4,000-character budget, loaded every session) from `MEMORY.md` (durable non-profile facts), and loads `MEMORY.md` only in the main private session, never in shared or group contexts [26].
- **Forms are the heaviest repeat.** The browser's autofill vocabulary is the most battle-tested list of what forms ask for: name parts, honorifics, nickname, organisation and title, address, email, telephone, instant-messaging handle, URL, photo, language, birthday, sex [27]. Two open-source projects for agents re-derive it: one asks for a profile once and saves it to a Markdown file [28]; the other is a local broker that releases only the fields a form needs, scoped to an origin and purpose with expiring grants, asks once when a field is missing (use once, save, or skip), never passes secrets through that path, and records value-free audit events [29]. The second is the closest prior art to the read contract recommended here.
- **Users paste personal details into prompts when there is nowhere else to put them.** An analysis of a million real conversations with a commercial assistant found personal data in 48% of translation requests and 16% of code-editing requests [30]. Each paste is an unlabelled copy outside any store.
- **Agents over-share when they hold the data.** A December 2025 study of eight browser agents found three disclosing personal information in passive tests where the task did not need it, some reusing details from prior chats, personalisation settings or browser profiles, and one attempting to submit a card number (reported by Help Net Security; the paper, arXiv 2512.07725, was not read directly) [31].

**The working list** (estimate, from the above and from the brief): how the name is written and pronounced; pronouns; time zone, working hours and current location at city level; roles and titles per affiliation; links and handles; bios at several lengths; headshots and logos; brand colours; signature blocks; billing and shipping details; standard answers to recurring questions; and "who may be told this".

## B. Prior art: personal profiles readable by software

### B1. Contact and profile vocabularies

- **vCard 4 (RFC 6350)** is the base of every address book; **jCard (RFC 7095)** is its JSON form [32][33].
- **JSContact (RFC 9553, May 2024)** is the IETF's JSON contact model, designed for JMAP and as vCard's successor in JSON. It has the fields this dossier needs and gets the hard ones right: `name` with ordered `components`, `sortAs`, and a `phonetic` value per component with `phoneticSystem` (IPA, Jyutping, Pinyin); `nicknames`; `organizations` and `titles` (a title links to its organisation); `speakToAs` with free-form `pronouns` per context and a `grammaticalGender` used for salutations, which the RFC says does not indicate gender identity; `preferredLanguages`; `addresses` carrying an IANA `timeZone`; `onlineServices`, `links`, `media` with kinds `photo`, `logo` and `sound`; `localizations` per language; and `created`/`updated` timestamps [6].
- **RFC 9554 (May 2024)** brought the same concepts back into vCard: `PRONOUNS`, `GRAMGENDER`, `LANGUAGE`, `SOCIALPROFILE`, `CREATED`, and the parameters `PHONETIC`, `SCRIPT`, `AUTHOR`, `AUTHOR-NAME`, `CREATED` and `DERIVED`, so a value can say who wrote it, when, and whether it was derived [7]. Per-property authorship and creation dates are exactly the provenance a dossier needs, standardised.
- **SCIM (RFC 7643)**, the enterprise provisioning schema, carries `displayName`, `nickName`, `title`, `preferredLanguage`, `locale`, `timezone`, and `photos` typed `photo` or `thumbnail`, and requires a photo URL to point at an image, not a page [34].
- **schema.org `Person`** (with `jobTitle`, `worksFor`, `affiliation`, `sameAs`, `image`, `knowsLanguage`) is what search engines read from a personal site [35]; **FOAF** is its RDF ancestor, still used by Solid profiles [36]; **h-card** is the microformat equivalent of a vCard in HTML (`p-name`, `u-photo`, `p-note` for a short bio) [37], and **rel=me** links tie a site to other profiles so each can vouch for the other [38]. **humans.txt** is a plain-text credits file at a site root [39]: a convention, not a schema.

**Verdict B1.** Name the dossier's fields after JSContact; it is the newest, has the right granularity for names, pronouns, phonetics, media kinds and localisation, and maps to vCard 4 + RFC 9554, h-card and schema.org. Do not store RDF or vCard text; generate them from the dossier when a site, a signature or an address book needs them.

### B2. Personal data stores and "me" cards

- **Solid** gives each person a pod and a WebID; the WebID profile document is public and links to a private preferences file "exclusively for the WebID owner", and to public and private *type indexes* that tell an app where data of each kind lives [4][40]. Stewardship moved to the Open Data Institute in October 2024, with standardisation work continuing at W3C [41]. The idea worth borrowing is the split itself, public profile and private preferences in one store, plus an index by kind; the RDF stack, access-control machinery and server are far more than one person's dossier needs.
- **Android** has kept the device owner's own contact since Android 4.0 as `ContactsContract.Profile`, served by the same contacts provider as every other contact but behind separate `READ_PROFILE` and `WRITE_PROFILE` permissions, with a 2012 note to developers that the permission exposes personally identifying information [2][42]. This is the closest precedent for the recommendation: same store and same API, separate permission.
- **Gravatar** turned a hashed email address into a public avatar and, since 2024, a profile API with job title, company, pronouns, pronunciation, time zone and verified accounts, fuller in authenticated requests [43]. **Libravatar** is the open, federated equivalent: a domain names its own avatar server through a DNS SRV record, falling back to a central service [44]. Both are publication targets for the `clear` headshot, not stores.
- **Keybase** made identity *proofs*: signed statements posted on each platform, checked by the client, linking accounts to one key [45]. It has had little visible development since Zoom bought it in May 2020 [45]. W3C Verifiable Credentials 2.0 became a Recommendation in May 2025 [46] and is the standard route for signed claims today. For a dossier, the lesson is narrower: store *evidence* for each handle (acquaint already does), and treat signed proofs as optional.
- **Press kits** are the pattern for "everything someone needs to write about you, in one place, in several sizes": presskit() by Rami Ismail packaged a studio's facts, descriptions and images on one page for journalists; its development stalled around 2014 and a static re-implementation followed in 2017 [47]. The pattern, not the tool, is the point: assets in named variants with usage notes.

### B3. Agent memory systems and their user profiles

- **Profile versus collection.** LangMem distinguishes semantic memory kept as a *profile* (one document of current state, updated in place, quick to read and easy for the user to edit, but holding only what its schema defines) from a *collection* (many extracted records, no loss but hard to reconcile, with precision falling as extraction over-fires) [8]. A dossier is a profile; the record's log is the collection behind it.
- **Letta (MemGPT)** keeps a `human` block about the user and a `persona` block about the agent in context, each with a character limit (2,000 by default), and moves everything else to searchable archival memory [48]. Right about budget: the always-loaded part must be small.
- **Mem0** extracts candidate facts and lets a model choose `ADD`, `UPDATE`, `DELETE` or `NOOP` against similar memories; its paper reports a 26% relative gain on an LLM-judge score over OpenAI's memory on the LOCOMO benchmark (vendor-authored) [49]. Right about reconciliation; wrong for this layer in that a model may delete a fact on its own judgement.
- **Zep / Graphiti** stores facts as graph edges with two timelines, when a fact held and when the system learned it, and on contradiction sets the old edge's end time instead of deleting it [9]. This is the right answer to "the newest statement wins" without losing history.
- **The reference MCP memory server** stores entities, relations and free-text observations in one JSON file [50]: no labels, no sources, no dates, and the whole graph is readable by any caller.
- **ChatGPT and Claude memory** separate explicit saved facts from a synthesised summary and give the user a view to edit or delete them [22][24]. ChatGPT notes that deleting a memory does not remove it from past chats [22]: a copy outside the store outlives the store's deletion.
- **Rewind/Limitless and Apple's personal context** took the opposite route: capture everything, index it, retrieve by query. Meta acquired Limitless in December 2025 and the Rewind desktop recorder is being phased out [51]. Apple's personal-context Siri, announced in 2024, was delayed in March 2025 and was reported as part of iOS 27 in 2026; its shipped state was not verified for this report [52][53]. Neither is a model for a curated dossier; both show the cost of capturing first and deciding later.
- **Benchmarks** point the same way: LongMemEval tests "knowledge updates" and "abstention" as distinct abilities, and reports commercial assistants at roughly 30–70% accuracy in a simplified setting and simple memory designs doing poorly on time-dependent questions [54]. Personal knowledge graphs, as a research agenda, name the same problems: personal entities that no public graph knows, population from the user's own data, and evolution over time [55].

**Verdict B3.** What they got right: a small always-loaded profile, a user who can see and edit it, dated supersession instead of deletion, and private-only loading for sensitive memory (OpenClaw). What they got wrong for this use: no field-level labels, no sources, models that rewrite or delete facts on their own, and profiles scoped per project or per product so the same person is learned many times.

## C. Privacy tiers and consent

- **Contextual integrity** is the frame the agent-privacy literature uses: a flow of information is appropriate when it matches the norms of the context it came from, not when the information is "public" or "private" in the abstract [56]. ConfAIde found GPT-4 and ChatGPT revealing private information in contexts where people would not, 39% and 57% of the time, with privacy-inducing prompts and chain-of-thought not fixing it [11]; PrivacyLens found agents leaking in about a quarter to two-fifths of trajectories even with privacy instructions, and a gap between what models say about privacy norms and what they do when acting [12]. Implication: a label must be enforced by code at the boundary, not by asking the model to be discreet.
- **Permission decisions are predictable from context.** Users' data-access decisions for agents were consistent within a communication context and similar across people; a model predicting them reached 85.1% accuracy, 94.4% on its confident predictions, and 66.9% with no history (accepted at IEEE S&P 2026) [57]. This supports a small fixed label set plus a per-request confirmation for the rest, and suggests later learning of a default from the principal's own approvals.
- **Traffic Light Protocol 2.0** (FIRST, 2022) is the field's standard four-word sharing scale: `CLEAR` (no limit), `GREEN` (community), `AMBER` (recipient's organisation and clients, with `AMBER+STRICT` for the organisation only), `RED` (named recipients only) [58]. acquaint already uses its four words [5].
- **The W3C Data Privacy Vocabulary** (a community-group work, not a W3C standard; v2.3) offers a taxonomy of purposes and personal-data categories [59]. Useful as a source of purpose words for the read log; too large to adopt whole.
- **GDPR** names the special categories (Article 9) and the principles of data minimisation and purpose limitation (Article 5) [15][60]. A person's own data kept for their own use is probably within the household exemption (not a legal opinion), but the categories and principles are the right checklist.

**Recommended tiers for the principal's own facts.** Reuse acquaint's labels with the meanings its disclosure lattice already gives them, so one gate serves both:

| Label | Who may receive it | Typical fields | Read rule |
|---|---|---|---|
| `clear` | anyone; may be published | display name, pronouns, public bio, headshot, public handles, current public titles, logos, brand colours | any agent, no log |
| `green` | people the principal has marked `involved` or `open` | work email, scheduling link, time zone and working hours, city-level location, signature with phone omitted | any agent, no log |
| `amber` | only people marked `open`, or a named recipient the principal confirms for this message | mobile number, postal address, legal name where it differs, billing entity details, date of birth | any agent may read for a stated purpose; logged; disclosure to anyone not cleared needs the principal's confirmation |
| `red` | the principal and the agents acting for them; never in outgoing content | travel plans and current exact location, private reasons behind availability, the record of which collaborator may know what | allow-listed agents only; logged; the outgoing gate scans for these values |
| never | not stored | credentials, security answers, government and financial identifiers, special categories, biometrics | a pointer to the password manager, resolved only on the principal's surface |

The mapping of `amber` to the `open` tier and `green` to `involved` is how acquaint's disclosure module computes clearance today (read from source, 2026-11-09); if that lattice changes, the dossier follows it, because the dossier owns no rules of its own.

## D. The retrieval contract

1. **Read by field, through a verb.** An agent asks for `name.full`, `bio.short`, `asset:headshot.square` with a `purpose` and an `audience` (the same audience record correspond already produces for a conversation, which acquaint's `brief` and `disclosure` accept [5]). It never loads the facts file into context. This is the AirGapAgent principle [10] and the selected-field broker's design [29].
2. **The answer is a value, a refusal, or a pointer.** A refusal names the label and the reason ("amber; audience is public"), and what would unlock it (a confirmation item on the principal's desk). A `never` field returns its pointer and the instruction that only the principal resolves it.
3. **Log restricted reads.** Every read of an `amber` or `red` field appends `{when, agent or session, field, purpose, audience, result}` to an append-only read log without the value (the value-free audit of [29]). Unrestricted reads are not logged: a log nobody reads is noise.
4. **Gate on the way out, not only on the way in.** Before a message leaves, the outgoing check that acquaint already runs for names (`check`, `disclosure`) also scans for the literal values of `red` fields and of `amber` fields the audience is not cleared for. Code enforces it; the model is not asked to be careful [11][12].
5. **Read-then-act taint.** A value read from the dossier may be written into a form only when the form's destination is the declared audience; text fetched from the web or a message cannot change the audience (prompt injection is how memories and data leave [13][31]).
6. **Default loading.** At most the `clear` and `green` "card" (name, pronouns, time zone, current role, a one-line bio, under about 1,000 characters) is loaded into every session; everything else is fetched on demand. The size follows the budgets Letta and OpenClaw set for always-loaded profile blocks [48][26] (an estimate; tune by measurement).

## E. Resources: images, documents, templates

- **Address by key, not by path.** Each asset is a manifest entry with a stable logical key (`headshot.square`, `logo.<org>.dark`, `signature.html.formal`, `cv.long`) and variants (size, format, background), and the bytes live in a blob store addressed by content hash. References in other records and in skills use the key; the manifest maps key to current hash; the blob store maps hash to bytes. Replacing a headshot changes one manifest line; moving from a folder to object storage swaps the blob store's backend. In the fleet this is a `MutableMapping` from the dol package [61]; outside it, any key-value store with the same verbs.
- **Every asset entry carries**: `key`, `kind` (JSContact's `photo`, `logo`, `sound`, plus `document` and `template`), `variants` (width, height, format, background), `label`, `alt` text, `usage` (rights and where it may be used), `source`, `recorded`, `review_by`, `sha256`, and optional `published_at` (the URL where a `clear` asset is already public: personal site, Gravatar or Libravatar [43][44]). A `sound` asset recording how the name is said complements the IPA phonetic field [6].
- **Keep bytes out of the synced Markdown repository.** acquaint syncs records through a private git repository [5]; binary images in git grow history forever and survive deletion in every clone (acquaint's README says the same of names). The manifest syncs with the record; the blob store syncs separately and can be public for `clear` assets.
- **Generated, not stored.** vCard, h-card, schema.org JSON-LD, a press-kit page and signature blocks in each format are renderings of the dossier, generated on demand, so they never drift from it.

## F. Keeping it current

- **Who writes.** The principal (source `self: "…"` or `operator`), and agents, which may only append dated, sourced observations or proposals to the record's log. A proposal becomes a fact when the principal confirms it: one tap on the desk, the same "accept / edit / ignore" shape the other reports recommend for items [62]. This is acquaint's existing rule that only the operator grants trust and sets labels [5], extended to facts.
- **Provenance per field.** `source`, `recorded`, and the author (RFC 9554's `AUTHOR` and `CREATED` parameters standardise the same idea for vCard [7]; W3C PROV is the general model [63]).
- **Newest statement wins, history kept.** A new value sets the old one's `valid_to` and links `supersedes`; reads return the value valid now, and a past date can be asked for (the bitemporal model [9]). Conflicts between two current statements are never resolved by a model: both are shown to the principal (Mem0 lets the model delete; this layer should not [49]).
- **Review dates on what ages.** Headshots, titles, bios and affiliations get `review_by`; acquaint's `lint` already warns when a record passes its re-verification age and when an affiliation ends while a permissive tier is still in force [5]. A lapsed field is still returned, marked stale, so an agent can say "this bio is from 2024".
- **One copy.** Agents and skills reference the dossier; they do not copy facts into per-project memories. The concrete step is a line in the user-level instructions telling every session where the principal's facts live and that `user`-type auto memories about them belong there instead [17].

## G. Same store as other people, or its own?

**Recommendation: the same store, as one record marked as the operator's own, with field-level labels and its own read permission.**

Arguments for the same store:

- **Identity resolution needs it.** The principal appears in every conversation acquaint digests and every audience correspond builds; `resolve github:<handle>` must return the principal, and a second store would duplicate `identities.yaml` and `links.yaml` and let them drift.
- **The disclosure machinery is the tier machinery.** Labels, seals, audience ceilings, source rules, lint tripwires and `POLICY.md` are what this layer needs, already tested [5]. A separate store would rebuild them or go without.
- **The preference model already lives there** [1]; facts and preferences of the same person in two stores invites contradictions.
- **The general features help every record.** Orgs need logos and brand colours, projects need slide templates, collaborators need correctly spelled names with pronunciation. A typed facts file and an asset manifest belong to acquaint's record model, with the principal as their first customer.
- **Precedent.** Android's profile contact, Apple's "Make This My Card" and Solid's profile and preferences in one pod all keep the owner with everyone else, distinguished by a flag and a permission [2][3][4].

Arguments for a separate store, and why they do not hold today:

- *Different readers.* Many more agents read the principal's facts, and more often. Answered by the field-level read verb and the default card, not by a second store.
- *Different threat model.* A leak of the principal's facts is worse than a leak of a stranger's. Answered by labels, the outgoing gate and the read log.
- *Sharing.* If the acquaint store ever becomes shared with collaborators (acquaint's `auto_commit` already checks that the sync repository has no other collaborator [5]), the principal's `red` and `amber` fields would need a separate, unshared store. That is the condition under which to split, and the facts file should be written so it can be moved out without changing its keys.

**What acquaint needs** (as features, first customer equerry): `self: <id>` in its config, so every tool knows which record is the operator; a structured `facts.yaml` with per-field label, source and dates, read by `who` with label enforcement; an `assets.yaml` manifest plus a pluggable blob store; a read log for `amber` and `red`; and a `card` output (the always-loaded summary). equerry owns none of the data; it calls these verbs.

## H. Recommended schema

Every field is `{value, label, source, recorded}` and optionally `{valid_from, valid_to, review_by, supersedes, contexts}`. Names follow JSContact where it has one [6]. Defaults below are starting labels the principal confirms field by field; "source" is where the value normally comes from.

| Group | Field (JSContact name where one exists) | Default label | Usual source | Review |
|---|---|---|---|---|
| Name | `name.full`, `name.components` (given, surname, title, credential), `name.sortAs` | clear | self | rarely |
| Name | `name.components[].phonetic` (IPA), `media.sound` (how it is said) | clear | self | rarely |
| Name | `nicknames`, accepted short forms, **forms to avoid** (misspellings, wrong diacritics) | clear | self | rarely |
| Name | legal name, where it differs | amber | self | rarely |
| Address | `speakToAs.pronouns` (per language), `speakToAs.grammaticalGender` (for salutations only) | clear | self | rarely |
| Language | `preferredLanguages` (with order and context) | clear | self | yearly |
| Time | IANA time zone (home), working hours, quiet hours (as RRULE + zone) | green | self | quarterly |
| Time | current location while travelling (city), with `valid_to` | red | self or calendar | expires |
| Roles | `titles` and `organizations` per affiliation, with period (mirrors `links.yaml`) | clear or as the org's label | self, org page | yearly |
| Roles | how to describe each affiliation, and which may be named together | per affiliation | self | yearly |
| Reach | public handles and website (`onlineServices`, `links`), with evidence (`identities.yaml`) | clear | evidence links | yearly |
| Reach | work email, scheduling link | green | self | yearly |
| Reach | mobile number, postal address | amber | self | yearly |
| Bios | one line (≤ 15 words), short (≤ 50), medium (≤ 120), long (≤ 300); first and third person; per audience (technical, business, academic) | clear | self | 6 months |
| Assets | headshots (square, portrait, light and dark backgrounds), avatar | clear | self | yearly |
| Assets | logos and brand colours per org or project (on that org's record) | per org | org | yearly |
| Assets | signature blocks per context (plain, HTML; formal, short) | green | self | yearly |
| Assets | CV, slide and letter templates, press-kit page | clear or green | self | 6 months |
| Answers | standard answers to recurring questions (`question pattern → answer`), e.g. billing entity, invoice address, VAT or company number, shipping size, "how did you hear" | amber unless stated | self | yearly |
| Pointers | `secret_ref` for any identifier a form may need (passport, tax, bank) | never stored | self | n/a |
| Preferences | pointers to `style.md`, `views.md`, `rules.yaml` [1] | as those files | self | as those files |

A trap in "standard answers": dietary requirements, accessibility needs and emergency contacts look routine but can reveal special-category data or are about another person. Store an answer such as "will send requirements directly" rather than the reason; an emergency contact is a link to that person's own record.

## I. Standard terminology

- **Principal**: the person the agents act for (agency theory; used in the phase-1 report [1]).
- **Dossier**: here, the principal's facts and resources; in the field, a **profile** (one current-state document) as opposed to a **collection** of extracted memories [8]; **semantic** versus **episodic** memory [8].
- **Personal data store (PDS)**, **pod**, **WebID**, **type index** (Solid) [4][40]; **personal knowledge graph** [55].
- **Me card / My Card / profile contact**: the owner's own card in an address book [3][2].
- **vCard, jCard, JSContact**; **h-card**, **rel=me** (IndieWeb) [6][37][38].
- **Press kit / media kit / boilerplate bio**: assets and descriptions packaged for others to use [47].
- **Contextual integrity**, **data minimisation**, **purpose limitation** [56][60].
- **TLP labels** (`CLEAR`, `GREEN`, `AMBER`, `AMBER+STRICT`, `RED`) [58].
- **Bitemporal**: valid time (when a fact held) versus transaction time (when it was recorded) [9]; **supersession**.
- **Provenance** (W3C PROV) [63]; **secret reference** (a pointer resolved by a vault) [16].
- **Selected-field access**, **origin-bound grant**, **use once / save / skip** [29].

## J. Things worth knowing that were not asked

- **The scattering is happening now.** Per-repository, machine-local auto memory [17] means the principal's facts already exist as many unlabelled, unsourced copies; the first act of the dossier is to absorb them, and the instruction to stop making new ones.
- **Deletion does not reach copies.** Any fact pasted into a prompt, a message or a memory survives the dossier's correction [22][30]. Supersession and review dates help only for agents that read through the verb.
- **Voice samples are biometric data.** A voice-cloning sample of the principal is not a "resource" for this store; it belongs with the voice layer's own controls, under the special-category rule [15].
- **A handwritten signature image is a forgery kit.** If one is kept, it is `red` and released only on the principal's confirmation per document.
- **Calendar-derived facts are not dossier facts.** Working hours and quiet hours are stated by the principal; current location and availability derived from a calendar are episodic and expire. Keep only the stated rule plus a `valid_to` entry for travel.
- **Field-level labels can be learned later.** Given the evidence that permission decisions are predictable from context [57], the read log and the principal's confirmations are training data for proposing labels on new fields; never for setting them.
- **Publishing is a separate act.** `clear` means "may be published", not "is published". A generated public card (h-card on a personal site, Gravatar or Libravatar avatar) is the one place the dossier pushes outward, and it should be regenerated from the dossier, never edited by hand [37][43][44].

## Minimum viable version

1. In acquaint: `self: <id>` in the config; a `facts.yaml` in that record with about twenty fields (name and phonetics, pronouns, time zone and hours, roles, handles, four bios, a signature), each with label, source and date; `who self -f <field> --purpose --audience` returning a value or a refusal; lint for unlabelled or unsourced fields.
2. An `assets.yaml` manifest for three assets (square headshot, one logo, one signature block), bytes in a folder-backed key-value store keyed by hash.
3. A `card` output under about 1,000 characters, referenced from the user-level instructions so every session finds it, plus the line telling sessions not to store user facts elsewhere.
4. An append-only read log for `amber` and `red`, and the outgoing gate extended to scan for `red` values.
5. Later, not first: answers to recurring questions, generated vCard and h-card, learned label proposals, proposals from agents confirmed on the desk.

## What this means for the build

- equerry stores no facts about the principal; it reads them from acquaint through a field-level verb with purpose and audience.
- File acquaint features (first customer equerry): `self` designation, `facts.yaml` with per-field labels and dates, asset manifest plus blob store behind a mapping, read log, `card` output.
- Field names follow JSContact; vCard, h-card and schema.org are generated exports.
- Labels are acquaint's `clear/green/amber/red`, plus "never stored, pointer only"; enforcement is code at the read and at the outgoing gate, never a prompt.
- Agents propose facts in the log; only the principal promotes them, one tap on the desk.
- Newest statement supersedes with dates kept; `review_by` on headshots, titles and bios.
- MVP: twenty fields, three assets, the card, the read log; answers and exports later.
- Split into a separate store only if the acquaint store ever gains another collaborator.

## REFERENCES

1. [equerry research: A principal model and a review organisation for a solo operator of many coding agents (2026-11-09)](principal-model-and-review-organisation.md)
2. [Android Developers: ContactsContract.Profile](https://developer.android.com/reference/android/provider/ContactsContract.Profile)
3. [Apple Support: Set up your My Card in Contacts on Mac](https://support.apple.com/guide/contacts/adrb3525ca49)
4. [Solid WebID Profile (draft)](https://solid.github.io/webid-profile/)
5. [acquaint: README and source (trust and disclosure modules), read 2026-11-09](https://github.com/thorwhalen/acquaint)
6. [RFC 9553: JSContact, a JSON Representation of Contact Data (May 2024)](https://www.rfc-editor.org/rfc/rfc9553.html)
7. [RFC 9554: vCard Format Extensions for JSContact (May 2024)](https://www.rfc-editor.org/rfc/rfc9554.html)
8. [LangMem: Conceptual guide (profile vs collection; semantic, episodic, procedural memory)](https://langchain-ai.github.io/langmem/concepts/conceptual_guide/)
9. [Rasmussen et al. Zep: A Temporal Knowledge Graph Architecture for Agent Memory. arXiv 2501.13956 (2025)](https://arxiv.org/abs/2501.13956)
10. [Bagdasarian et al. AirGapAgent: Protecting Privacy-Conscious Conversational Agents. arXiv 2405.05175 (2024)](https://arxiv.org/abs/2405.05175)
11. [Mireshghallah et al. Can LLMs Keep a Secret? Testing Privacy Implications of Language Models via Contextual Integrity Theory (ConfAIde). ICLR 2024](https://arxiv.org/abs/2310.17884)
12. [Shao et al. PrivacyLens: Evaluating Privacy Norm Awareness of Language Models in Action. NeurIPS 2024 D&B](https://arxiv.org/abs/2409.00138)
13. [The Hacker News: ChatGPT macOS flaw could have enabled long-term spyware via memory function (Sep 2024)](https://thehackernews.com/2024/09/chatgpt-macos-flaw-couldve-enabled-long.html)
14. [Dong et al. Memory Injection Attacks on LLM Agents via Query-Only Interaction (MINJA). arXiv 2503.03704](https://arxiv.org/abs/2503.03704)
15. [GDPR Article 9: Processing of special categories of personal data](https://gdpr-info.eu/art-9-gdpr/)
16. [1Password Developer: Secret references](https://www.1password.dev/cli/secret-references/)
17. [Claude Code docs: How Claude remembers your project (CLAUDE.md and auto memory)](https://code.claude.com/docs/en/memory)
18. [equerry research: Prior art for AI secretaries (2026-11-09)](prior-art-ai-secretaries.md)
19. [anthropics/claude-code issue 21854: Memory features: project-level, cross-project, and account-level persistence](https://github.com/anthropics/claude-code/issues/21854)
20. [anthropics/claude-code issue 36561: Global/shared memory across projects](https://github.com/anthropics/claude-code/issues/36561)
21. [anthropics/claude-code issue 39195: Support shared memory across multiple projects](https://github.com/anthropics/claude-code/issues/39195)
22. [OpenAI Help Center: Memory FAQ](https://help.openai.com/en/articles/8590148-memory-faq)
23. [TechRadar: ChatGPT's new customization options](https://www.techradar.com/computing/artificial-intelligence/chatgpts-new-customization-options-are-exactly-what-ive-been-waiting-for-to-make-my-chats-more-personal)
24. [Anthropic: Memory (announcement)](https://www.anthropic.com/news/memory)
25. [Anthropic Help Center: How does Claude's memory work?](https://support.anthropic.com/en/articles/11817273-how-does-claude-s-memory-work)
26. [OpenClaw docs: Agent workspace](https://docs.openclaw.ai/concepts/agent-workspace)
27. [WHATWG HTML Living Standard: Autofill field names](https://html.spec.whatwg.org/multipage/form-control-infrastructure.html#autofill)
28. [jayzuccarelli/autofill: form-filling agent on browser-use](https://github.com/jayzuccarelli/autofill)
29. [lifan-builds/agent-autofill: a local information broker for AI agents (Apache-2.0)](https://github.com/lifan-builds/agent-autofill)
30. [Mireshghallah et al. Trust No Bot: Discovering Personal Disclosures in Human-LLM Conversations in the Wild. COLM 2024](https://arxiv.org/abs/2407.11438)
31. [Help Net Security: Browser agents don't always respect your privacy choices (22 Dec 2025), on arXiv 2512.07725](https://www.helpnetsecurity.com/2025/12/22/browser-agents-privacy-risks-study/)
32. [RFC 6350: vCard Format Specification](https://www.rfc-editor.org/rfc/rfc6350)
33. [RFC 7095: jCard, the JSON Format for vCard](https://www.rfc-editor.org/rfc/rfc7095)
34. [RFC 7643: SCIM Core Schema](https://www.rfc-editor.org/rfc/rfc7643.html)
35. [schema.org: Person](https://schema.org/Person)
36. [FOAF Vocabulary Specification](http://xmlns.com/foaf/spec/)
37. [microformats wiki: h-card](https://microformats.org/wiki/h-card)
38. [IndieWeb: rel-me](https://indieweb.org/rel-me)
39. [humans.txt](https://humanstxt.org/)
40. [Solid Type Indexes](https://solid.github.io/type-indexes/)
41. [Open Data Institute: Solid's next chapter (2024)](https://theodi.org/news-and-events/blog/solids-next-chapter-returning-the-web-to-its-people-first-roots/)
42. [Android Developers Blog: New Social APIs in Android ICS (2012)](https://android-developers.googleblog.com/2012/02/new-social-apis-in-android-ics.html)
43. [Gravatar REST API: data models](https://docs.gravatar.com/rest/api-data-specifications/data-models/)
44. [Libravatar: Simple API](https://wiki.libravatar.org/api/)
45. [Wikipedia: Keybase](https://en.wikipedia.org/wiki/Keybase)
46. [W3C: Verifiable Credentials Data Model v2.0, Recommendation 15 May 2025](https://www.w3.org/TR/2025/REC-vc-data-model-2.0-20250515/)
47. [Pixelnest: Announcing presskit.html (2017), on presskit() by Rami Ismail](https://pixelnest.io/2017/03/presskit-html/)
48. [Letta docs: Understanding memory management](https://docs.letta.com/concepts/memory-management)
49. [Chhikara et al. Mem0: Building Production-Ready AI Agents with Scalable Long-Term Memory. arXiv 2504.19413 (2025)](https://arxiv.org/abs/2504.19413)
50. [Model Context Protocol reference servers: Knowledge Graph Memory Server](https://github.com/modelcontextprotocol/servers/tree/main/src/memory)
51. [BetaNews: Meta has acquired Limitless (8 Dec 2025)](https://betanews.com/2025/12/08/meta-has-acquired-limitless-a-startup-that-produces-ai-powered-wearables/)
52. [MacRumors: Apple delays Apple Intelligence Siri features (7 Mar 2025)](https://www.macrumors.com/2025/03/07/apple-intelligence-siri-features-delayed/)
53. [MacRumors: Siri in iOS 27, every new feature and change to expect (2 Jun 2026)](https://macrumors.com/2026/06/02/ios-27-siri-features)
54. [Wu et al. LongMemEval: Benchmarking Chat Assistants on Long-Term Interactive Memory. ICLR 2025](https://arxiv.org/abs/2410.10813)
55. [Balog, Kenter. Personal Knowledge Graphs: A Research Agenda. ICTIR 2019](https://research.google/pubs/personal-knowledge-graphs-a-research-agenda/)
56. [Nissenbaum. Privacy as Contextual Integrity. Washington Law Review 79:119 (2004)](https://digitalcommons.law.uw.edu/wlr/vol79/iss1/10)
57. [Wu et al. Towards Automating Data Access Permissions in AI Agents. arXiv 2511.17959, IEEE S&P 2026](https://arxiv.org/abs/2511.17959)
58. [FIRST: Traffic Light Protocol 2.0](https://www.first.org/tlp/)
59. [W3C DPVCG: Data Privacy Vocabulary v2.3](https://w3c.github.io/dpv/2.3/dpv/)
60. [GDPR Article 5: Principles relating to processing of personal data](https://gdpr-info.eu/art-5-gdpr/)
61. [dol: Python data-object layer (storage as Mapping interfaces)](https://github.com/i2mint/dol)
62. [equerry research: Synthesis (2026-11-09)](synthesis.md)
63. [W3C: PROV-O, the PROV Ontology](https://www.w3.org/TR/prov-o/)
