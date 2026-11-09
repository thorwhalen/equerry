# Finance team: tax and admin calendar (starter rows)

The finance team's rows of the two-country starter calendar (section E of `docs/research/domain-experts-for-a-solo-technical-founder.md`, plus the corporate tax balance date from its section C2), as data. Dates are rules, not model output: the weekly sweep reads these rows, adds the entity-specific dates, and raises an item only for a date inside the warning window (60 days by default; the letter may change it).

- **Entity-specific dates come from the venture pages**: fiscal year end (which moves the corporate tax balance), the VAT regime each entity is under, whether it employs anyone, whether it sells training. This file holds the rules; the pages hold the facts they apply to.
- **Roll-forward**: a deadline falling on a weekend or public holiday moves to the next business day in both countries [17][25]; confirm the rule for each form before relying on it.
- **Which rows apply** depends on the principal's entities, residences and citizenships: personal rows apply only if the principal files in that country; FBAR only for a US person with accounts abroad.
- Fields: `when`, `what`, `team`, `jurisdiction`, `source` (an entry of `sources.md` and its URL), `last_checked`, and an optional `note`. Last checked 2026-11-09; re-verify quarterly.

```yaml
- when: "15 January"
  what: "Individual estimated tax, Q4 of the previous year"
  team: finance
  jurisdiction: "[US]"
  source: "[25] https://www.irs.gov/node/144561"
  last_checked: 2026-11-09

- when: "31 January"
  what: "Form 1099-NEC to contractors and to the IRS"
  team: finance
  jurisdiction: "[US]"
  source: "[25] https://www.irs.gov/node/144561"
  last_checked: 2026-11-09
  note: "date from secondary calendars; confirm the year's date on the IRS page"

- when: "15 March, 15 June, 15 September, 15 December"
  what: "Corporate income tax instalments (acomptes d'IS)"
  team: finance
  jurisdiction: "[FR]"
  source: "[17] https://bofip.impots.gouv.fr/bofip/3558-PGP.html"
  last_checked: 2026-11-09

- when: "15th day of the fourth month after fiscal year end (15 May for a calendar year)"
  what: "Corporate income tax balance (solde d'IS)"
  team: finance
  jurisdiction: "[FR]"
  source: "[17] https://bofip.impots.gouv.fr/bofip/3558-PGP.html"
  last_checked: 2026-11-09
  note: "from section C2 of the report; the fiscal year end comes from the venture page"

- when: "15 April, 15 June, 15 September"
  what: "Individual estimated tax, Q1 to Q3"
  team: finance
  jurisdiction: "[US]"
  source: "[25] https://www.irs.gov/node/144561"
  last_checked: 2026-11-09

- when: "15 April (automatic extension to 15 October)"
  what: "FBAR, if the principal is a US person with foreign accounts above the aggregate threshold; Form 8938 is separate"
  team: finance
  jurisdiction: "[US]"
  source: "[65] https://www.fincen.gov/report-foreign-bank-and-financial-accounts"
  last_checked: 2026-11-09

- when: "1 April to 31 May (2026 campaign dates)"
  what: "BPF, the training provider's annual pedagogical and financial report"
  team: finance
  jurisdiction: "[FR]"
  source: "[23] https://www.centre-inffo.fr/site-droit-formation/actualites-droit/transmission-du-bpf-report-au-31-mai-2026"
  last_checked: 2026-11-09
  note: "shared with the bizdev team; only for an entity with a training activity declaration number; the campaign dates are set each year"

- when: "second working day after 1 May (5 May in 2026)"
  what: "VAT annual return CA12, simplified regime"
  team: finance
  jurisdiction: "[FR]"
  source: "[19] https://www.legifiscal.fr/actualites-fiscales/4465-tva-depot-ca12-5-mai-2026.html"
  last_checked: 2026-11-09
  note: "secondary source; secondary sources report the simplified regime ends on 1 January 2027 (not verified): confirm with the expert-comptable before planning 2027"

- when: "July and December"
  what: "VAT instalments, simplified regime (55% in July, 40% in December)"
  team: finance
  jurisdiction: "[FR]"
  source: "[19] https://www.legifiscal.fr/actualites-fiscales/4465-tva-depot-ca12-5-mai-2026.html"
  last_checked: 2026-11-09
  note: "same caveat as the CA12 row"

- when: "15th of each month"
  what: "DSN payroll declaration, employers under 50 staff"
  team: finance
  jurisdiction: "[FR]"
  source: "[20] https://www.legisocial.fr/paie/declarations-sociales/calendrier-dsn-2026-dates-limites.html"
  last_checked: 2026-11-09
  note: "only for an entity with at least one employee; secondary source"

- when: "15 December"
  what: "CFE balance"
  team: finance
  jurisdiction: "[FR]"
  source: "[18] https://www.impots.gouv.fr/portail/professionnel/cet-cfe-et-cvae"
  last_checked: 2026-11-09

- when: "yearly, on the membership anniversary"
  what: "Apple Developer Program renewal"
  team: finance
  jurisdiction: "any"
  source: "[55] https://developer.apple.com/support/renewal/"
  last_checked: 2026-11-09
  note: "the admin-estate sweep holds the actual date per account"
```

Rows owned by other teams that finance watches in the cross-domain events, without duplicating them here: the 83(b) election within 30 days of a restricted-stock transfer (people), a new 409A before grants (ventures), the Delaware franchise tax and the French accounts filing (legal).
