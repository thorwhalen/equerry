# Domain experts for a solo technical founder with a portfolio of ventures

Research report, deep tier, 2026-11-09. Design question only: no private material was used. Companion to [the phase-1 report on the principal model and the review organisation](principal-model-and-review-organisation.md) [1], which settled decision rights (RAPID, reversibility, management by exception) and how reviewer agents should be organised (one strong model per concern, checklists, a meta-judge, exception reports). This report does not repeat that; it asks which concrete domains need an expert, and what each expert does month to month.

Jurisdiction labels: **[US]** federal United States, with Delaware as the default state of incorporation; **[FR]** France; **[EU]** European Union law that applies in France. Dates are for the calendar year and a calendar-year fiscal close unless stated. Nothing here is legal, tax or accounting advice; section D names the line where a licensed professional is required.

## Summary and recommendation

**The question.** One technical founder is, at the same time, a co-founder or senior contributor in several start-ups, the lead on client work that may become a company, a seller of expert services, and the builder of their own products (apps and open-source libraries). Which business domains need an expert watching them, what does a very experienced generalist in each domain actually do every month, and how should an agent organisation hold that?

**Verdict in one paragraph.** Model each domain expert on the **fractional executive**, not on a chatbot persona or a department. Fractional CTOs, CFOs and CMOs are hired for a bounded number of hours, own named deliverables on a fixed cadence (weekly check-in, monthly pack, quarterly plan), hold explicit decision rights, and say in writing what is out of scope [3][4][5][6]; that is exactly the contract an agent expert needs. Eight teams cover a portfolio founder: **Legal, IP and compliance; Finance, tax and administration; People and contracting; Sales and business development; Marketing and positioning; Product and research; Engineering and open source; Ventures, investors and boards.** Personal effectiveness is not a ninth team: it is the secretary's own job. Most of the value in the first months is not judgement but **memory**: a compliance calendar that never misses a statutory date in either country, a dossier per venture, and a cross-domain checklist that fires when an event (an equity grant, a launch, a new client, a fundraise) touches several teams. The domains where a portfolio founder is most exposed are the ones a single-company founder never meets: **intellectual property crossing between ventures and clients, conflicts of interest and corporate opportunities between companies, and two countries' filing calendars at once** (sections C and K).

**The three strongest recommendations.**

1. **Write each team as a fractional-executive engagement letter**: mandate in one line, recurring deliverables with cadence, decision rights (may decide / recommends / never), escalation line to a licensed professional, and a written out-of-scope list. The market's own scoping documents converge on this shape [4][5][6], and it maps one-to-one onto the phase-1 RAPID roles: the principal holds D, a team is Recommend, and only Legal and Finance hold Agree on named one-way doors [1].
2. **Build the deterministic layer first: a two-country compliance calendar plus one page per venture.** Most first-year harm in this profile comes from missed dates with no cure (a late 83(b) election cannot be filed [11]; a missed Form D or Delaware report has penalties and good-standing consequences [9][12]) rather than from bad strategy. Dates are rules, not model output, and they belong in the reminder machinery the synthesis already specifies [2].
3. **Make "I am not your lawyer / accountant" a typed outcome, not a disclaimer.** Each team has an escalation list (section D). When a request crosses it, the team produces a brief for the professional (facts, question, documents, deadline) instead of an answer. France reserves paid, habitual legal consultation and drafting for others to qualified persons and keeps accounting for others as a monopoly of chartered accountants [61][62]; the US limits representation before the IRS to attorneys, CPAs and enrolled agents [63].

**Biggest risks.** IP ownership that is silently split between ventures, clients and the founder personally (the default rules differ by country and by employee versus contractor) [28][29]; conflicts between a consulting client and a venture in the same field [30][31]; an agent giving confident tax or legal answers that are only right in one of the two countries; alert fatigue if eight teams each report monthly (the phase-1 budget and exception rules apply [1]); and stale regulation (several items below changed in 2025 and 2026 and will change again).

## What already exists

- **Decision rights and reviewer organisation**: the phase-1 report [1]. Its verdict (one strong reviewer model per concern with a checklist, fresh context, aggregation into exception reports under a daily budget, a second model only for one-way-door concerns) is the operating model for the teams below; this report supplies the checklists, cadences and reference packs those reviewers need.
- **The item, brief and reminder machinery**: the synthesis of the 2026-11-09 reports [2]. Each team's recurring task is an item producer; statutory dates are reminders with a trigger policy; monthly packs feed the brief. Nothing in this report needs new machinery.
- **acquaint** holds records of people, including the principal's own preference model; **openloops** holds obligations between the principal and agents. The per-team dossier (section F) should reference these rather than copy them.

## A. The functions of a small company, and which matter for a portfolio founder

The standard small-company frame is the EOS Accountability Chart, whose default seats are Visionary, Integrator, Sales/Marketing, Operations and Finance, each held by one named person and reviewed weekly against a scorecard of five to fifteen numbers and quarterly "Rocks" [7]. A portfolio career adds the frame of Charles Handy, who described a life made of several kinds of work (fee or wage work, study work, home work, gift work) whose balance is the thing to manage [8]. The principal's situation is both: several small companies, and one person whose time is split between them.

| Function | In a single small company | For this portfolio founder | Weight (estimate) |
|---|---|---|---|
| Legal | Formation, contracts, equity, IP, compliance | Multiplied by the number of entities and countries; IP between ventures and clients is the new risk | High |
| People / HR | Hiring, contractors, payroll, policies | Mostly contractors, co-founders and advisors; classification and IP assignment dominate | Medium |
| Marketing | Positioning, content, demand | Personal brand sells the consulting; products each need positioning | Medium |
| Sales / BD | Pipeline, deals | Consulting and training revenue is the cash engine; ventures need partnerships | High |
| Finance and administration | Books, cash, tax, payroll | Several sets of books, personal and company tax in two countries, estimated taxes | High |
| Product | Discovery, roadmap | Own products and advice to ventures | Medium |
| Research and development | Innovation, R&D credits | R&D credits on both sides; research feeds training content | Low to medium |
| Engineering | Delivery, quality, security | The principal's strength; open-source stewardship adds regulatory duties | Medium (low marginal value of an expert) |
| Operations | Tools, vendors, insurance, facilities | Subscriptions, domains, app-store accounts, insurance: an admin calendar | Low (fold into Finance) |
| Investor and board relations | Updates, board meetings, cap table | Per venture: different roles (founder, director, advisor, shareholder) | High |
| Personal effectiveness | The CEO's own system | Time allocation across ventures is the scarcest resource | High (the secretary's job) |

Weights are this report's judgement for the described profile, not a sourced ranking.

## B. The fractional executive as the template for an agent expert

What the market calls a fractional executive is a senior person hired part-time, with years of experience, working across several organisations rather than one [3]. Practitioner and vendor sources (labelled as such; they sell these services) agree on the shape:

| Element | Fractional CTO [4] | Fractional CFO [5] | Fractional CMO [6] | What the agent expert copies |
|---|---|---|---|---|
| Time | Advisory 8-16 h/month; hands-on 40-64 h/month | Retainer; first 30 days baseline, then 90-day plan | Up to about 35 h/month on a flat retainer | A token and run budget per month, not open-ended |
| First 30 days | Access; architecture diagram and top three risks; tech-debt list; draft roadmap and **signed decision rights** | Cash visibility becoming a 13-week forecast; close expectations; KPI definitions; 90-day roadmap | Goals, success metrics and strategy agreed in 90 days | An onboarding audit that produces the team's dossier and a ranked risk list |
| Cadence | Weekly contact with the team; written decisions | Weekly cash check-in, monthly KPI review and board pack, quarterly planning | Weekly founder sync, bi-weekly vendor review, monthly consolidated report | Weekly item(s) only if a tolerance breaks; one monthly pack; a quarterly plan |
| Decision rights | Decides architecture and senior hires; reports to the founder | Owns the forecast and board-pack content | Owns strategy and vendor accountability | "May decide" list (two-way doors only); everything else is Recommend [1] |
| Out of scope | Daily management, sustained coding; "accountability without authority sets the engagement up to fail" | Fundraising, systems projects, close clean-up, M&A scoped separately | Hands-on production, implementation, admin | A written out-of-scope list per team, plus the escalation line (section D) |

The lesson that transfers most directly is the CTO source's warning: an expert with accountability but no decision rights fails, and so does one with a "fix everything" scope [4]. For agents, the phase-1 gate already gives decision rights a form (reversibility class plus calibrated disagreement risk) [1]; each team's engagement letter says which action classes it may take alone.

**The engagement letter (one per team).** Mandate (one line); principal and ventures in scope; deliverables with cadence; may-decide list; recommend-only list; never list; escalation line and the named outside professional (if any); dossier location; monthly budget; review date. A team without a letter does not run.

## C. The recommended teams

| Team | Mandate in one line |
|---|---|
| **Legal, IP and compliance** | Every entity stays in good standing, every contract and IP chain is clean, and every statutory date is met in both countries, with a lawyer briefed before any one-way door. |
| **Finance, tax and administration** | Know cash, runway and tax exposure for each entity and for the principal personally, close the books monthly, pay on time, and keep the admin estate (subscriptions, insurance, accounts) under control. |
| **People and contracting** | Every person who works with a venture is correctly classified, papered, paid and assigned their IP, and equity promises are written down. |
| **Sales and business development** | A visible, qualified pipeline for consulting and training, with proposals, statements of work and rate cards that protect the principal's time and IP. |
| **Marketing and positioning** | Each offer and product has a stated position, an ideal customer and a small, steady publishing rhythm that compounds the principal's reputation. |
| **Product and research** | Each product has a current problem statement, evidence from users, a bounded next bet and a kill or continue date; research is turned into product and training material. |
| **Engineering and open source** | Shipped software is secure, licensed correctly and maintained to a stated standard, and open-source projects meet their stewardship duties without consuming the principal. |
| **Ventures, investors and boards** | For each venture: the principal's role, rights and obligations, the cap table position, the update and board cadence, and the time budget, on one page. |

The secretary (the front-desk agent in [2]) holds personal effectiveness: the weekly review, time allocation across ventures, and the brief. GTD's weekly review (get clear, get current, get creative) [58] and the maker's-schedule argument for protecting long blocks [59] are its reference pack.

### C1. Legal, IP and compliance

| Recurring task | Cadence | Output | Jurisdiction |
|---|---|---|---|
| Compliance calendar sweep: what falls due in the next 60 days per entity | Weekly | Items only for dates inside the warning window | US, FR |
| Delaware annual franchise tax report and payment (minimum $175 under Authorized Shares, $400 under Assumed Par Value Capital; $200 penalty, 1.5% monthly interest; no good-standing certificate while unpaid) [9] | Annual, 1 March | Filed report; check that the cheaper method was used | US |
| Form D within 15 calendar days of the first sale in a Reg D round, SAFEs included; state notice filings [12] | Per financing | Filing receipt; state notices | US |
| Beneficial ownership: domestic US companies exempt since the March 2025 interim final rule; foreign companies registered to do business in a US state still report [10] | On formation or foreign registration | Note in the entity dossier | US |
| SAS accounts approval (deadline set by the statutes; six months for a single-shareholder SASU) and filing at the greffe within one month of approval, two if filed electronically [16] | Annual | Minutes, filing receipt | FR |
| GDPR record of processing activities (Article 30), using the CNIL template; review on any new product or data flow [32] | Quarterly, and on change | Updated register | EU, FR |
| Contract register: renewals, notice periods, exclusivity and IP clauses across all ventures and clients | Monthly | Items for notices due; conflicts flagged | US, FR |
| IP chain check for each product and repository: who wrote it, under which agreement, assigned to whom [28][29] | Quarterly, and before any fundraise or sale | Chain-of-title table per asset | US, FR |
| Trademark maintenance (US: Section 8 declaration between years 5 and 6, renewal every 10 years) [56] | Annual check | Items when a window opens | US, FR |
| Product regulation watch: children's privacy for educational tools (amended COPPA Rule, compliance date 22 April 2026: separate parental consent for third-party disclosure for targeted advertising, a written security programme, retention limits) [33][34]; student records if sold to US schools (FERPA) [57]; AI Act transparency for AI features (Article 50 from 2 August 2026; labelling of generated content for systems already on the market by 2 December 2026) and AI literacy duties (Article 4) [37] | Quarterly | One-page delta per product | US, EU |

Frameworks and starter references: YC post-money SAFE and its user guide [38]; NVCA model financing documents [39]; Cooley GO document generators and guides [40]; CNIL record template and SME checklist [32]; the CRA open-source page [35]; the Delaware franchise tax page [9].

May decide: nothing that binds an entity. Recommends: contract mark-ups, which template to start from, filing drafts. Never: signs, files, or sends anything to a counterparty. Escalation line in section D.

### C2. Finance, tax and administration

| Recurring task | Cadence | Output | Jurisdiction |
|---|---|---|---|
| Cash and runway per entity and for the principal personally; 13-week cash view when cash is tight [5] | Weekly (light), monthly (full) | One line per entity: cash, burn, runway, change since last month | US, FR |
| Month-end close: reconciliations, categorisation, receivables chase, payables | Monthly | Closed month, variance note in plain sentences [5] | US, FR |
| Monthly pack: P&L, balance sheet, cash flow, KPIs, variance commentary | Monthly | Pack per entity that has investors or a board | US, FR |
| Individual estimated tax: 15 April, 15 June, 15 September, 15 January [25] | Quarterly | Payment reminder with estimate | US |
| Contractor reporting (Form 1099-NEC by 31 January, per secondary calendars; confirm on [25]) and W-9 / W-8 collection at onboarding | Annual, and per contractor | Forms on file | US |
| Corporate income tax instalments: 15 March, 15 June, 15 September, 15 December; balance by the 15th of the fourth month after close [17] | Quarterly | Instalment statement 2571 filed | FR |
| VAT: simplified regime instalments in July (55%) and December (40%), annual CA12 by the second working day after 1 May [19]; secondary sources report the simplified regime ends on 1 January 2027 (not verified) | Per regime | Returns, payments | FR |
| CFE (business property contribution) balance by 15 December [18] | Annual | Payment | FR |
| Payroll declaration DSN by the 15th of the following month for employers under 50 staff [20] | Monthly, if any employee | Filing | FR |
| R&D incentives: US domestic research expenditure, including software development, is deductible again under Section 174A [15]; French CII at 20% for SMEs from 2025, extended to the end of 2027 [21]; CIR and JEI status checked with an adviser | Annual planning | Claim or no-claim memo | US, FR |
| Admin estate: subscriptions, domains, app-store memberships (the Apple Developer Program renews yearly [55]), insurance renewals | Monthly | Renewal items, unused-tool list | Any |
| Personal foreign-account reporting if the principal is a US person with accounts abroad: FBAR when aggregate balances exceed $10,000, due 15 April with automatic extension to 15 October; Form 8938 is separate [65] | Annual | Reminder and account list | US |

Frameworks and starter references: the fractional CFO module list (cash, forecasting, KPIs and board reporting, fundraising, systems, controls and tax) [5]; the IRS deadlines page [25]; the French tax calendar and BOFiP pages [17][18]; Apple's tax page, which shows that Apple collects and remits sales taxes and VAT for app sales in most storefronts under its agency terms [54].

May decide: categorisation, reminders, which numbers go in the pack. Recommends: payment timing, estimates, budget changes. Never: moves money, files a return, picks a tax position.

### C3. People and contracting

| Recurring task | Cadence | Output | Jurisdiction |
|---|---|---|---|
| Classification check for every contractor: US common-law test (behavioural control, financial control, relationship) [26]; France: requalification risk when a legal subordination link exists (orders, monitoring, sanctions), regardless of the contract's label [27] | On engagement, then semi-annual | Risk note per person | US, FR |
| IP assignment in every agreement: US invention-assignment agreements (California limits them for inventions made on one's own time and equipment, unrelated to the employer's business) [28]; France vests employee-written software in the employer automatically, but not contractor or pre-incorporation code, which needs a written assignment [29] | On engagement | Signed agreement on file | US, FR |
| Equity promises: vesting, cliffs, acceleration, advisor grants written down; 409A valuation current before US option grants (valid up to 12 months or until a material event such as a SAFE or priced round) [13]; BSPCE eligibility in France (company under 15 years old, among other conditions; tax treatment of gains reformed for 2025) [22] | Per grant | Grant checklist (section G) | US, FR |
| 83(b) elections for restricted stock within 30 days of transfer; no late election; Form 15620 is optional [11] | Per grant | Mailed election with proof | US |
| Co-founder and advisor agreements: roles, time commitment, departure terms | On creation, annual review | Signed agreements | Any |
| Onboarding and offboarding: access, accounts, equipment, return of IP and data | Per person | Checklist completed | Any |

May decide: nothing with legal effect. Recommends: classification, agreement template, grant size bands. Never: hires, fires, promises equity.

### C4. Sales and business development

| Recurring task | Cadence | Output |
|---|---|---|
| Pipeline review: every open opportunity with stage, next step, date, value; qualification by a named method (MEDDICC: metrics, economic buyer, decision criteria, decision process, identify pain, champion, competition) [47] | Weekly | Top five next actions; stalled deals |
| Proposal and statement-of-work drafts from a template: scope, deliverables, cadence, out-of-scope, decision rights, IP retained by the principal | Per opportunity | Draft for the principal |
| Rate card and offer catalogue: projects, retainers, part-time roles, training formats | Quarterly | Updated catalogue |
| Training compliance in France: any training provider with an activity declaration number must file the annual pedagogical and financial report (BPF; campaign 1 April to 31 May in 2026) [23]; Qualiopi certification is required since 1 January 2022 to be paid from public or pooled funds (OPCO, CPF, France Travail, regions) [24] | Annual; per training contract | BPF filed; funding route chosen |
| Client portfolio health: utilisation versus the time budget, concentration risk, renewals | Monthly | One line per client, without names in any public artefact |
| Partnership and venture BD: introductions requested and owed | Monthly | Asks list for investor updates |

May decide: follow-up drafts, which deals to deprioritise in the list. Recommends: pricing, scope, go/no-go. Never: sends a proposal or accepts terms.

### C5. Marketing and positioning

| Recurring task | Cadence | Output |
|---|---|---|
| Positioning statement per offer and product (competitive alternatives, unique attributes, value, best-fit customers, market category, per April Dunford's method) [45] | Quarterly, and on launch | One page per offer |
| Publishing rhythm: talks, posts, open-source release notes, training material re-used as content | Weekly plan, monthly review | Calendar; what got traction |
| Customer-evidence collection: quotes, case notes (client permission recorded) | Monthly | Evidence file |
| Launch plan per product release (section G) | Per launch | Checklist |
| Channel metrics: a few numbers only, chosen to match the pipeline | Monthly | Scorecard rows [7] |

May decide: drafts, scheduling suggestions. Recommends: positioning, claims, channels. Never: publishes under the principal's name. A public claim about a client always goes through Legal.

### C6. Product and research

| Recurring task | Cadence | Output |
|---|---|---|
| Problem statement and current bet per product, sized as an "appetite" with a fixed time box (Shape Up) [43] | Per cycle (six weeks in Shape Up) | Pitch; kill or continue date |
| Continuous discovery: weekly user contact, an opportunity solution tree linking outcome, opportunities and experiments [44]; interviews that ask about past behaviour, not opinions (The Mom Test) [46] | Weekly | Interview notes, tree updates |
| Research watch for each domain the principal teaches or builds in | Monthly | Three items worth reading; training material candidates |
| Advice to ventures as a senior contributor: roadmap review, technical due diligence preparation | Per venture cadence | Written comments |

May decide: synthesis, ranking of opportunities. Recommends: bets, kills. Never: commits a venture's roadmap.

### C7. Engineering and open source

| Recurring task | Cadence | Output | Jurisdiction |
|---|---|---|---|
| Delivery health per product: deployment frequency, lead time, change failure rate, time to restore (DORA's four keys) [48] | Monthly | Four numbers, trend | Any |
| Security baseline against OWASP ASVS level 1 for anything with users' data [49]; dependency and repository hygiene with OpenSSF Scorecard [50] | Quarterly | Gap list | Any |
| Licensing: licence and copyright in every file (REUSE) [52], an OSI-approved licence [53], and contributions under the DCO or a CLA [51] | On release | Compliant release | Any |
| Cyber Resilience Act: free software developed outside a commercial activity is out of scope; an open-source steward (a legal person supporting software intended for commercial use) must keep a cybersecurity policy and cooperate with authorities, with reporting duties from 11 December 2027 and no fines; manufacturers' reporting of exploited vulnerabilities started on 11 September 2026 [35][36] | Annual, and when a venture monetises a library | Scope memo per project | EU |
| App-store compliance for the founder's own apps: review-guideline changes, account renewal [55] | Quarterly | Items | Any |

The principal is a technical founder, so this team's marginal value is lowest; it should reuse the fleet's existing code reviewers [1] and add only the licensing, security and regulatory checks above.

### C8. Ventures, investors and boards

| Recurring task | Cadence | Output |
|---|---|---|
| Venture page refresh (section H): role, time budget, stage, what it needs this month | Monthly | One page per venture |
| Investor update for ventures the principal leads: metrics, highlights, lowlights, specific asks, kept short and monthly (YC-style, per secondary sources) [41] | Monthly | Draft update |
| Board preparation and follow-up: agenda, pack, minutes or written consents, action list; quarterly is the common early-stage cadence [42] | Quarterly | Board pack; action items |
| Cap-table position per venture: shares, options, SAFEs, vesting status, QSBS clock (for stock issued after 4 July 2025: 50% exclusion after three years, 75% after four, 100% after five; $15 million cap; $75 million gross-asset test) [14] | Quarterly | Position table |
| Fiduciary and conflict map: where the principal is a director or officer, which charters renounce corporate opportunities (Delaware allows renunciation by charter or board action) [30][31] | On any new role; annual | Conflict map |
| Time budget versus actual, per venture | Monthly | Over/under list for the weekly review |

May decide: drafts and the page itself. Recommends: asks, board agenda. Never: votes, consents, or communicates with investors.

## D. The escalation line: where a licensed professional is required

The rule for every team: **an agent may explain, organise, draft for the principal's own use, and prepare a brief; it may not be the professional of record.** When a request crosses a line below, the team's output is a brief for a named professional, not an answer.

| Team | Escalate to | When (non-exhaustive) | Why |
|---|---|---|---|
| Legal, IP and compliance | Corporate / IP lawyer (US); avocat (FR) | Any financing, equity plan, M&A, dispute, employment termination, regulatory filing with penalties, cross-border structure, licence change on a released project | France reserves paid, habitual legal consultation and drafting of private deeds for others to qualified persons (law of 31 December 1971, art. 54 ff.) [61]; US unauthorised-practice rules and the ABA's guidance on lawyers' use of AI (Formal Opinion 512) [64] assume a lawyer exercises the judgement |
| Finance, tax and administration | CPA or enrolled agent (US); expert-comptable (FR) | Tax returns, tax positions, R&D credit claims, cross-border personal tax, payroll set-up, any accounts kept for an entity other than the principal's own | Representation before the IRS is limited to attorneys, CPAs and enrolled agents [63]; in France keeping, closing and reviewing accounts for others is reserved to chartered accountants, confirmed by the Cour de cassation in January 2026 [62] |
| People and contracting | Employment lawyer; payroll provider | Hiring an employee, terminating anyone, reclassifying a contractor, equity to employees | Classification errors are retroactive in France (requalification from day one) [27] |
| Ventures, investors and boards | Company counsel; valuation firm | Any vote, consent, conflict, 409A, secondary sale | 409A safe harbour relies on an independent appraisal [13] |
| Sales, Marketing, Product, Engineering | Rarely licensed; Legal on claims, contracts and privacy | Comparative or regulated claims; contracts; personal data | Routed through Legal |

A specific trap for this principal: the consulting and training business may be asked to deliver documents to clients (a data-processing agreement, an AI-use policy). An agent drafting those *for a client* is drafting for others, which in France is a reserved activity [61]; the team should flag it and route it to a partner lawyer.

## E. The two-country compliance calendar (starter set)

| When | What | Who | Jurisdiction |
|---|---|---|---|
| 15 Jan | Q4 individual estimated tax [25] | Finance | US |
| 31 Jan | Form 1099-NEC to contractors and the IRS (secondary sources; confirm) [25] | Finance | US |
| 1 Mar | Delaware annual report and franchise tax [9] | Legal | US |
| 15 Mar, 15 Jun, 15 Sep, 15 Dec | Corporate income tax instalments [17] | Finance | FR |
| 15 Apr, 15 Jun, 15 Sep | Individual estimated tax [25] | Finance | US |
| 15 Apr (auto-extension 15 Oct) | FBAR, if applicable [65] | Finance | US |
| 1 Apr to 31 May | BPF for training activity (2026 campaign dates) [23] | Sales / Finance | FR |
| Second working day after 1 May | VAT CA12 (simplified regime) [19] | Finance | FR |
| Within the statutes' deadline (six months for a SASU), then one month | Accounts approval, then filing at the greffe [16] | Legal | FR |
| July, December | VAT instalments (simplified regime) [19] | Finance | FR |
| 15th of each month | DSN, if any employees [20] | Finance | FR |
| 15 Dec | CFE balance [18] | Finance | FR |
| 30 days from any restricted-stock transfer | 83(b) election [11] | People | US |
| 15 days from first sale in a round | Form D [12] | Legal | US |
| 12 months from last 409A, or a material event | New 409A before grants [13] | Ventures | US |
| 2 Aug 2026 / 2 Dec 2026 / 2 Dec 2027 | AI Act transparency / generated-content labelling for existing systems / stand-alone high-risk systems [37] | Legal, Engineering | EU |
| 11 Dec 2027 | CRA full application; steward reporting [36] | Engineering | EU |
| Years 5-6, then every 10 years from registration | US trademark maintenance [56] | Legal | US |

Entity-specific dates (fiscal year end, statutes, payroll) are added from each entity's dossier. Weekend and holiday roll-forward rules apply in both countries [17][25].

## F. The dossier each team keeps

Every team reads one shared **principal file** and one **venture file** per entity, and writes its own section. Facts live once; teams reference them.

**Principal file (shared, private).** Tax residences and citizenships (which decides which personal filings exist); entities owned and roles held in each; personal agreements that touch IP (employment, consulting, advisory); professionals of record (lawyer, accountant per country); risk tolerance and one-way-door list from the preference model [1]; time budget per venture; languages in which each document must exist.

**Venture file (one per entity).** Legal name, form (C-corp, SAS, LLC, sole proprietorship), jurisdiction, registration numbers, fiscal year end; the principal's roles (founder, director, officer, employee, contractor, advisor, shareholder) and their documents; cap table position; IP chain; contracts register; regulatory profile (personal data, children, AI features, training funding, app stores, open source); bank and accounting system; calendar dates; open decisions.

**Team sections.** Legal: filings, contracts, IP chain, regulatory memos. Finance: books location, last close, cash, tax positions taken (by whom). People: people register with classification and agreements. Sales: pipeline and clients (never named in public artefacts). Marketing: positioning pages. Product: bets and evidence. Engineering: repositories, licences, security status. Ventures: board and investor history.

## G. Cross-domain items and how small companies coordinate them

Small companies coordinate cross-functional work with one named owner per item (Apple's "directly responsible individual", adopted in public handbooks such as GitLab's) [60], a weekly leadership meeting that walks the scorecard, the quarterly priorities and an issues list (EOS's Level 10 meeting) [7], and checklists for recurring events. For the agent organisation: the **secretary is the DRI** for every cross-domain item; each event type has a checklist that creates one item per team; the weekly review closes them.

| Event | Teams touched | Checklist core |
|---|---|---|
| Equity grant | People, Legal, Finance, Ventures | Board approval; 409A current [13]; grant agreement; 83(b) within 30 days [11] or BSPCE conditions [22]; cap table updated; QSBS dates noted [14] |
| Fundraise (SAFE or priced) | Ventures, Legal, Finance, People | Template choice [38][39]; Form D and state notices [12]; 409A refresh [13]; data room; investor-update list updated |
| New consulting or fractional client | Sales, Legal, Finance, People | Conflict check against the venture map [30]; MSA and SOW with IP retained and background IP listed; tax forms; invoicing and VAT; subcontractor papers |
| Training engagement in France | Sales, Finance, Legal | Funding route (Qualiopi needed for OPCO or CPF) [24]; activity declaration; BPF data captured as delivered [23] |
| Product launch (an app, an educational tool) | Product, Marketing, Legal, Engineering, Finance | Positioning [45]; privacy notice and GDPR register [32]; COPPA if under-13 users in the US [33]; AI disclosures if AI features [37]; security baseline [49]; store tax set-up [54] |
| Open-source release or licence change | Engineering, Legal, Marketing | Licence and REUSE compliance [52][53]; contributor terms [51]; CRA scope memo if a venture monetises it [35]; announcement |
| New contractor | People, Legal, Finance | Classification [26][27]; IP assignment [28][29]; tax form; access set-up |
| Taking a new role in a venture | Ventures, Legal, Finance | Role documents; corporate-opportunity and conflict review [30][31]; time budget; tax consequences of equity |
| Year end | All | Calendar for the next year generated; venture pages reviewed; engagement letters renewed |

## H. The venture page template

```
# <Venture code name>                       updated <date>   next review <date>
Role: <founder | co-founder | director | officer | employee | contractor | advisor | shareholder>
Entity: <form>, <jurisdiction>, FYE <month>      Stage: <idea | pre-seed | seed | A | revenue | wind-down>
Time budget: <hours/week agreed>   Actual (last 4 weeks): <hours/week>
Ownership: <% fully diluted>, <instrument>, vesting <status>, QSBS/BSPCE clock <date>
Rights and duties: <board seat? information rights? non-compete or exclusivity? IP assignment?>
This month it needs from me: <1-3 items>
Decisions waiting on me: <item links>
Next dates: <filing, board, update, renewal>
Risks: <conflicts, IP, cash, people>
Health: cash/runway <..>  |  last update sent <..>  |  last board <..>
Kill / continue criteria: <what would make me reduce or end my role>
```

One page per venture follows the portfolio practice of the fractional executives themselves, who report per client on a fixed cadence [4][5][6]; the template's fields are this report's synthesis. The client engagement that may become a company gets the same page, with "client" as the role until it changes.

## I. Standard terminology

- **Fractional executive**: part-time senior leader on a retainer with defined deliverables [3][4]. **Retainer**, **statement of work (SOW)**, **master services agreement (MSA)**.
- **Engagement letter** (here): the per-team contract in section B.
- **DRI**: the one person accountable for an item [60]. **RAPID**, **RACI**: decision-role frameworks, see [1].
- **Accountability Chart, Rocks, Scorecard, Level 10 meeting, IDS** (EOS) [7].
- **Compliance calendar**: the dated list of statutory obligations per entity.
- **Month-end close**, **13-week cash flow**, **burn**, **runway**, **board pack**, **variance commentary** [5].
- **Cap table**, **409A valuation**, **83(b) election**, **QSBS**, **SAFE (post-money)**, **Form D**, **written consent** [11][12][13][14][38].
- **Corporate opportunity**, **renunciation (DGCL §122(17))** [30].
- **Invention assignment agreement** (US), **dévolution des droits sur le logiciel** (FR, CPI art. L113-9) [28][29].
- **Worker classification** (US), **requalification**, **lien de subordination** (FR) [26][27].
- **Approbation des comptes**, **dépôt au greffe**, **acomptes d'IS**, **CA12**, **CFE**, **DSN** (FR) [16][17][18][19][20].
- **BSPCE** (French founder warrants), **CIR / CII / JEI** (French R&D incentives) [21][22].
- **Numéro de déclaration d'activité** (training activity number; abbreviated NDA, not to be confused with a non-disclosure agreement), **BPF**, **Qualiopi**, **OPCO**, **CPF** [23][24].
- **Record of processing activities (RoPA / registre)** [32]; **open-source steward**, **manufacturer** (CRA) [35]; **provider**, **deployer**, **AI literacy** (AI Act) [37].
- **Positioning**, **ideal customer profile (ICP)** [45]; **MEDDICC** [47]; **appetite**, **betting table** [43]; **opportunity solution tree** [44]; **DORA four keys** [48]; **ASVS** [49]; **DCO / CLA** [51].
- **Unauthorised practice of law** (US), **exercice illégal du droit / de l'expertise comptable** (FR) [61][62].

## J. Minimum viable version

1. **The calendar and the pages, no model judgement.** Section E as reminders, plus one venture page per entity and the principal file, filled once by interview. This alone removes the no-cure misses (83(b), Form D, Delaware, French filings).
2. **Two teams with engagement letters: Legal-IP-compliance and Finance-tax-admin.** Each runs a weekly sweep (items only on breach), a monthly pack and the escalation line. They are where errors are irreversible and where two countries interact.
3. **The cross-domain checklists in section G**, triggered by the principal or by an event the fleet already sees (a new repository, a new contract file, a calendar entry).
4. **Then Ventures-investors-boards and Sales-BD**, because they carry the time budget and the cash engine.
5. **Then People, Marketing, Product**; Engineering reuses existing reviewers plus the licensing and CRA checks.

Every addition is judged by the phase-1 metrics (shown items per day, override rate, misses) [1][2].

## K. Things you did not ask that matter

- **IP is the portfolio founder's largest silent risk.** Code written for a client, for a venture and for oneself can sit in the same habits and even the same repositories. Default ownership differs: in France an employee's software vests in the employer, but contractor and pre-incorporation code does not [29]; in the US an invention-assignment agreement may capture more than intended, within state limits [28]. The Legal team's quarterly chain-of-title check (C1) and an explicit "background IP" schedule in every SOW are the cheapest protection.
- **Conflicts between roles.** A director owes duties to each company; Delaware lets a company renounce classes of opportunities in its charter or by board action, but only where it has done so [30][31]. Consulting in the same field as a venture needs a written conflict check (section G).
- **Cross-border personal tax is the highest-stakes licensed line.** Which personal filings apply depends on citizenship and residence; if the principal is a US person with accounts outside the US, FBAR and Form 8938 are separate obligations [65]. This needs one adviser who knows both systems; the agent's role is the calendar and the document pack.
- **Training is regulated in France in ways consulting is not**: activity declaration, annual BPF, and Qualiopi for funded training [23][24]. Selling AI training also meets the AI Act's AI-literacy duty, which client organisations must support [37]; it is a selling point as much as an obligation.
- **Children and schools change the product's legal profile.** An educational tool with under-13 users in the US is under the amended COPPA Rule [33][34]; one sold to schools meets FERPA [57]; in the EU, GDPR applies with the CNIL's guidance [32].
- **Open source is mostly outside the CRA until a venture monetises it**; then the venture may be a manufacturer or a steward, with different duties [35][36].
- **Regulation is moving fast.** BOI reporting, QSBS, Section 174, BSPCE, CII, the AI Act and the CRA all changed between 2024 and 2026 [10][14][15][21][22][36][37]. Each reference pack needs a "last checked" date and a quarterly re-check, or the experts will be confidently out of date.
- **Unverified in this report**: the 2027 change to France's simplified VAT regime [19]; YC's own investor-update template (only secondary descriptions found) [41]; the exact Form 1099-NEC date for 2027 [25]; the reasoning of the full HBR article beyond its introduction [3].

## What this means for the build

1. Add a `team` record to equerry: mandate, deliverables with cadence, may-decide / recommend / never action classes, escalation line, dossier path, monthly budget. Eight instances, two enabled in v1.
2. Ship the two-country compliance calendar as data (date rule, entity, team, jurisdiction, source URL, last-checked) feeding the reminder machinery; no model in the loop.
3. Add a principal file and venture files as the dossier, private, referenced by every team; the venture page renders from them.
4. Add "escalate to professional" as an item outcome that produces a brief (facts, question, documents, deadline), distinct from an answer.
5. Add event-triggered cross-domain checklists (section G) that open one item per team with the secretary as DRI.
6. Reviewers per concern from phase 1 load each team's checklist and reference pack; reference packs carry last-checked dates and a quarterly re-verification job.
7. Measure by misses (calendar), shown items per team per week, and escalations the principal agreed with.

## REFERENCES

1. [equerry: A principal model and a review organisation for a solo operator of many coding agents (2026-11-09)](principal-model-and-review-organisation.md)
2. [equerry: Synthesis, what the build takes from the seven reports (2026-11-09)](synthesis.md)
3. [Yokoi T, Bonsall A. How Part-Time Senior Leaders Can Help Your Business. Harvard Business Review, 2024-07-02 (introduction only read)](https://hbr.org/2024/07/how-part-time-senior-leaders-can-help-your-business)
4. [Arc.dev: Fractional CTO: what they do, what they cost, and when a startup should hire one (vendor source)](https://arc.dev/employer-blog/?p=5494)
5. [Burkland Associates: What startup founders should expect from a fractional CFO service package (2026-03-17, vendor source)](https://burklandassociates.com/2026/03/17/what-startup-founders-should-expect-from-a-fractional-cfo-service-package/)
6. [Matthew Deal: Fractional CMO scope of work, what it should actually include (practitioner source)](https://matthewdeal.com/blog/fractional-cmo-scope-of-work)
7. [EOS Worldwide: EOS glossary (Accountability Chart, Rocks, Scorecard, Level 10 meeting)](https://www.eosworldwide.com/glossary)
8. [London Business School: Handy essentials (portfolio life)](https://www.london.edu/think/handy-essentials)
9. [Delaware Division of Revenue: Franchise tax](https://revenue.delaware.gov/franchise-tax/)
10. [Davis Polk: FinCEN eliminates beneficial ownership reporting requirements for U.S. companies and U.S. persons (2025)](https://www.davispolk.com/insights/client-update/fincen-eliminates-beneficial-ownership-reporting-requirements-us-companies)
11. [IRS Form 15620: Section 83(b) election](https://www.irs.gov/pub/irs-pdf/f15620.pdf)
12. [Haven: Form D filing guide for startups (secondary)](https://www.usehaven.com/blog-posts/form-d-filing-guide-for-startups-sec-compliance-explained)
13. [Carta: A founder's guide to the 409A valuation](https://carta.com/learn/startups/equity-management/409a-valuation/)
14. [Gibson Dunn: Focus on QSBS provisions in the One Big Beautiful Bill Act (2025)](https://www.gibsondunn.com/focus-on-qsbs-provisions-in-the-one-big-beautiful-bill-act/)
15. [Weaver: R&E tax relief, how the One Big Beautiful Bill Act impacts Section 174A](https://weaver.com/resources/re-tax-relief-how-the-one-big-beautiful-bill-act-impacts-section-174a/)
16. [Legalstart: Délai d'approbation des comptes (secondary; see C. com. art. L227-9)](https://www.legalstart.fr/fiches-pratiques/comptabilite-entreprise/delai-approbation-des-comptes/)
17. [BOFiP: Impôt sur les sociétés, paiement des acomptes et du solde](https://bofip.impots.gouv.fr/bofip/3558-PGP.html)
18. [impots.gouv.fr: Impôts locaux, CET (CFE et CVAE)](https://www.impots.gouv.fr/portail/professionnel/cet-cfe-et-cvae)
19. [Legifiscal: TVA, dépôt de la CA12 au 5 mai 2026 (secondary)](https://www.legifiscal.fr/actualites-fiscales/4465-tva-depot-ca12-5-mai-2026.html)
20. [Legisocial: Calendrier DSN 2026, dates limites (secondary)](https://www.legisocial.fr/paie/declarations-sociales/calendrier-dsn-2026-dates-limites.html)
21. [economie.gouv.fr: Crédit d'impôt innovation](https://www.economie.gouv.fr/entreprises/credit-impot-innovation)
22. [Legifiscal: Bons de souscription de parts de créateur d'entreprise (BSPCE) (secondary)](https://www.legifiscal.fr/creation-entreprise/capital-operations-juridiques/bons-souscription-parts-createur-entreprise-bspce.html)
23. [Centre Inffo: Transmission du BPF, report au 31 mai 2026](https://www.centre-inffo.fr/site-droit-formation/actualites-droit/transmission-du-bpf-report-au-31-mai-2026)
24. [DREETS Normandie: Qualiopi, la certification qualité des organismes de formation](https://normandie.dreets.gouv.fr/QUALIOPI-la-certification-qualite-des-organismes-de-formation)
25. [IRS: Important tax deadlines and dates](https://www.irs.gov/node/144561)
26. [IRS Topic no. 762: Independent contractor vs. employee](https://www.irs.gov/taxtopics/tc762)
27. [Bpifrance Création: Indépendance juridique, risque de requalification du contrat de mission en contrat de travail](https://bpifrance-creation.fr/encyclopedie/micro-entreprise-regime-auto-entrepreneur/lessentiel-ce-regime/independance-juridique)
28. [California Labor Code §2870 (Justia)](https://law.justia.com/codes/california/2020/code-lab/division-3/chapter-2/article-3-5/section-2870/)
29. [Kohen Avocats: Dévolution des droits sur le logiciel créé par un salarié, CPI art. L113-9 (secondary)](https://kohenavocats.fr/?p=2090563)
30. [Delaware Code title 8 §122 (Justia), including §122(17)](https://law.justia.com/codes/delaware/title-8/chapter-1/subchapter-ii/section-122)
31. [Ervin Cohen & Jessup: Delaware SB 154, a shift in the corporate opportunity doctrine (secondary)](https://www.ecjlaw.com/ecj-blog/delaware-sb-154-a-shift-in-the-corporate-opportunity-doctrine-by-jeffrey-r-glassman)
32. [CNIL: Le registre des activités de traitement (with template)](https://www.cnil.fr/fr/le-registre-des-activites-de-traitement)
33. [FTC: Complying with COPPA, frequently asked questions](https://www.ftc.gov/business-guidance/resources/complying-coppa-frequently-asked-questions)
34. [Hunton: COPPA Rule amendment compliance deadline approaches (2026)](https://www.hunton.com/privacy-and-cybersecurity-law-blog/coppa-rule-amendment-compliance-deadline-approaches)
35. [European Commission: Cyber Resilience Act, open source](https://digital-strategy.ec.europa.eu/en/policies/cra-open-source)
36. [European Commission: Cyber Resilience Act, reporting obligations](https://digital-strategy.ec.europa.eu/en/policies/cra-reporting)
37. [Lewis Silkin: Council of the EU gives AI Omnibus final green light (2026-07-09)](https://lewissilkin.com/insights/2026/07/09/council-of-the-eu-gives-ai-omnibus-final-green-light-102nbb1)
38. [Y Combinator: Documents (post-money SAFE and user guide)](https://www.ycombinator.com/documents)
39. [NVCA: Model legal documents](https://nvca.org/model-legal-documents/)
40. [Cooley GO: Documents](https://www.cooleygo.com/documents/)
41. [Visible: Tips from YC, using asks, metrics and a recap to power your investor updates (secondary)](https://visible.vc/blog/tips-from-yc-using-asks-metrics-and-a-recap-to-power-your-investor-updates/)
42. [Seth Levine: Why your board meetings aren't working (2026)](https://sethlevine.com/archives/2026/02/why-your-board-meetings-arent-working.html)
43. [Basecamp: Shape Up](https://basecamp.com/shapeup)
44. [Product Talk: Opportunity solution trees](https://www.producttalk.org/opportunity-solution-trees/)
45. [April Dunford: Books (Obviously Awesome; Sales Pitch)](https://www.aprildunford.com/books)
46. [Rob Fitzpatrick: The Mom Test](https://www.momtestbook.com/)
47. [MEDDICC: MEDDPICC sales methodology and process](https://meddicc.com/meddpicc-sales-methodology-and-process)
48. [DORA: DORA's software delivery metrics, the four keys](https://dora.dev/guides/dora-metrics-four-keys/)
49. [OWASP: Application Security Verification Standard](https://owasp.org/www-project-application-security-verification-standard/)
50. [OpenSSF Scorecard](https://scorecard.dev/)
51. [Developer Certificate of Origin](https://developercertificate.org/)
52. [FSFE: REUSE](https://reuse.software/)
53. [Open Source Initiative: Licenses](https://opensource.org/licenses)
54. [Apple: App Store Connect Help, understanding taxes](https://developer.apple.com/help/app-store-connect/making-payments-to-apple/understanding-taxes)
55. [Apple Developer: Membership renewal](https://developer.apple.com/support/renewal/)
56. [USPTO: Keeping your registration alive](https://www.uspto.gov/trademarks/maintain/keeping-your-registration-alive)
57. [U.S. Department of Education, Student Privacy: FERPA](https://studentprivacy.ed.gov/ferpa)
58. [David Allen Company: GTD Weekly Review checklist](https://gettingthingsdone.com/wp-content/uploads/2014/10/Weekly_Review_Checklist.pdf)
59. [Paul Graham: Maker's schedule, manager's schedule](https://www.paulgraham.com/makersschedule.html)
60. [GitLab Handbook: Directly responsible individuals](https://handbook.gitlab.com/handbook/people-group/directly-responsible-individuals/)
61. [Conseil national des barreaux: Vade-mecum de l'exercice du droit (2026), on law no. 71-1130 of 31 December 1971, art. 54 ff.](https://cnb.avocat.fr/medias/file/vademecum---l-exercice-du-droit---2026-6ad648b13fc736.93807795.pdf)
62. [Eurojuris: Monopole des experts-comptables, Cass. crim. 21 January 2026 no. 24-81.008 (secondary)](https://www.eurojuris.fr/actus-particuliers/patrimoine/construction/articles/monopole-experts-comptables-interdiction-montage-mise-a-disposition-43629.htm)
63. [IRS: Enrolled agents, frequently asked questions (who may practise under Circular 230)](https://www.irs.gov/tax-professionals/enrolled-agents/frequently-asked-questions-faqs)
64. [American Bar Association: Formal Opinion 512, Generative artificial intelligence tools (2024-07-29)](https://www.americanbar.org/content/dam/aba/administrative/professional_responsibility/ethics-opinions/aba-formal-opinion-512.pdf)
65. [FinCEN: Report foreign bank and financial accounts (FBAR)](https://www.fincen.gov/report-foreign-bank-and-financial-accounts)

All URLs accessed 2026-11-09. Items marked secondary or vendor were not checked against a primary source. Some primary pages (NVCA, EOS, Carta, Justia, the ABA PDF, economie.gouv.fr) refused automated fetches; their content here rests on search-indexed text and the secondary sources named alongside them.
