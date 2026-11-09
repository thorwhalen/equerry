# People team: starter sources

Each entry: `[n]` title, URL, what it is for, jurisdiction, primary or secondary, last checked. Plain numbers are those of the REFERENCES list in this framework's research report `docs/research/domain-experts-for-a-solo-technical-founder.md`; `D`-numbers are those of `docs/research/operator-dossier-and-resource-store.md`. A number means the same source in every team's file. "Primary" means the statute, the authority, the standard or the original paper; "secondary" means a law firm's note, a guide or a vendor, not checked against the primary text. Re-verify quarterly.

Some primary pages refused automated fetches on the last check (Carta, Justia); their content rests on search-indexed text. Open them by hand before calling a claim from them certain.

## Classification

- [26] IRS, Topic no. 762: Independent contractor vs. employee. <https://www.irs.gov/taxtopics/tc762>. For: the common-law test (behavioural control, financial control, relationship of the parties). `[US]`. Primary. Last checked 2026-11-09.
- [27] Bpifrance Création, Indépendance juridique: risque de requalification du contrat de mission en contrat de travail. <https://bpifrance-creation.fr/encyclopedie/micro-entreprise-regime-auto-entrepreneur/lessentiel-ce-regime/independance-juridique>. For: the subordination link (orders, monitoring, sanctions) and requalification from the first day, whatever the contract's label. `[FR]`. Primary (public agency guidance; the rule itself is case law). Last checked 2026-11-09.

## Intellectual property

- [28] California Labor Code §2870 (Justia). <https://law.justia.com/codes/california/2020/code-lab/division-3/chapter-2/article-3-5/section-2870/>. For: the limit on invention-assignment agreements (own time, own equipment, unrelated to the employer's business). `[US]` California. Primary (statute, via Justia). Last checked 2026-11-09.
- [29] Kohen Avocats, Dévolution des droits sur le logiciel créé par un salarié, CPI art. L113-9. <https://kohenavocats.fr/?p=2090563>. For: employee software vests in the employer; contractor and pre-incorporation code needs a written assignment. `[FR]`. Secondary (law firm). Last checked 2026-11-09.

## Equity

- [11] IRS Form 15620, Section 83(b) election. <https://www.irs.gov/pub/irs-pdf/f15620.pdf>. For: the 30-day election for restricted stock; no late election; the form is optional. `[US]`. Primary. Last checked 2026-11-09.
- [13] Carta, A founder's guide to the 409A valuation. <https://carta.com/learn/startups/equity-management/409a-valuation/>. For: a current 409A before option grants; validity up to 12 months or until a material event. `[US]`. Secondary (vendor). Last checked 2026-11-09.
- [22] Legifiscal, Bons de souscription de parts de créateur d'entreprise (BSPCE). <https://www.legifiscal.fr/creation-entreprise/capital-operations-juridiques/bons-souscription-parts-createur-entreprise-bspce.html>. For: BSPCE eligibility (company under 15 years old, among other conditions) and the 2025 reform of the gains' tax treatment. `[FR]`. Secondary. Last checked 2026-11-09.

## The principal's facts: labels, schema, privacy

- [D6] RFC 9553, JSContact: a JSON representation of contact data (May 2024). <https://www.rfc-editor.org/rfc/rfc9553.html>. For: field names for the principal's facts (`name.full`, `speakToAs.pronouns`, `preferredLanguages`, `titles`, `organizations`, `onlineServices`). any. Primary (IETF standard). Last checked 2026-11-09.
- [D58] FIRST, Traffic Light Protocol 2.0. <https://www.first.org/tlp/>. For: the four sharing labels (`CLEAR`, `GREEN`, `AMBER`, `RED`) the dossier's labels are named after. any. Primary (standard). Last checked 2026-11-09.
- [D56] Nissenbaum, Privacy as contextual integrity. Washington Law Review 79:119 (2004). <https://digitalcommons.law.uw.edu/wlr/vol79/iss1/10>. For: the frame: a flow is appropriate when it matches the norms of the context it came from, not because a fact is "public". any. Primary (paper). Last checked 2026-11-09.
- [D11] Mireshghallah et al., Can LLMs keep a secret? Testing privacy implications of language models via contextual integrity theory (ConfAIde), ICLR 2024. <https://arxiv.org/abs/2310.17884>. For: evidence that models reveal private information in the wrong context even when told not to; why labels are enforced by code, not by asking the model to be discreet. any. Primary (paper). Last checked 2026-11-09.
- [D12] Shao et al., PrivacyLens: evaluating privacy norm awareness of language models in action, NeurIPS 2024. <https://arxiv.org/abs/2409.00138>. For: agents leak in a quarter to two-fifths of trajectories despite privacy instructions; the gap between stated norms and actions. any. Primary (paper). Last checked 2026-11-09.
- [D15] GDPR Article 9: processing of special categories of personal data. <https://gdpr-info.eu/art-9-gdpr/>. For: the categories the dossier never stores. `[EU]`. Secondary rendering of the primary text. Last checked 2026-11-09.
- [D60] GDPR Article 5: principles relating to processing of personal data. <https://gdpr-info.eu/art-5-gdpr/>. For: data minimisation and purpose limitation, the reason a lead reads one field for one purpose. `[EU]`. Secondary rendering of the primary text. Last checked 2026-11-09.
