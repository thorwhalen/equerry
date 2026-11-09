# Legal team: compliance calendar (starter rows)

The legal team's rows of the two-country starter calendar (section E of `docs/research/domain-experts-for-a-solo-technical-founder.md`), as data. Dates are rules, not model output: the weekly sweep reads these rows, adds the entity-specific dates, and raises an item only for a date inside the warning window (60 days by default; the letter may change it).

- **Entity-specific dates come from the venture pages**: fiscal year end, the statutes' deadline for approving accounts, financing dates, registration dates of trademarks. This file holds the rules; the pages hold the facts they apply to.
- **Roll-forward**: a deadline falling on a weekend or public holiday usually moves to the next business day in both countries; confirm the exact rule for each form before relying on it.
- **Which rows apply** depends on the principal's entities and jurisdictions (a row for a Delaware corporation does not apply to a French SAS). The instance's letter or venture pages say which entities exist.
- Fields: `when`, `what`, `team`, `jurisdiction`, `source` (an entry of `sources.md` and its URL), `last_checked`, and an optional `note`. Last checked 2026-11-09; re-verify quarterly.

```yaml
- when: "1 March, yearly"
  what: "Delaware annual report and franchise tax; check the cheaper method (Assumed Par Value Capital) was used; late penalty plus monthly interest; no good-standing certificate while unpaid"
  team: legal
  jurisdiction: "[US]"
  source: "[9] https://revenue.delaware.gov/franchise-tax/"
  last_checked: 2026-11-09

- when: "within the statutes' deadline after fiscal year end (six months for a SASU), then within one month of approval (two if filed electronically)"
  what: "SAS accounts approval, then filing of the accounts at the greffe"
  team: legal
  jurisdiction: "[FR]"
  source: "[16] https://www.legalstart.fr/fiches-pratiques/comptabilite-entreprise/delai-approbation-des-comptes/"
  last_checked: 2026-11-09
  note: "secondary source; primary text C. com. art. L227-9; the statutes of each entity set the actual deadline"

- when: "15 calendar days from the first sale in a Regulation D round"
  what: "Form D, SAFEs included; state notice filings"
  team: legal
  jurisdiction: "[US]"
  source: "[12] https://www.usehaven.com/blog-posts/form-d-filing-guide-for-startups-sec-compliance-explained"
  last_checked: 2026-11-09
  note: "event-driven: the financing date comes from the venture page; secondary source"

- when: "2 August 2026"
  what: "AI Act Article 50 transparency obligations apply (people told they interact with an AI system; generated content marked)"
  team: legal
  jurisdiction: "[EU]"
  source: "[37] https://lewissilkin.com/insights/2026/07/09/council-of-the-eu-gives-ai-omnibus-final-green-light-102nbb1"
  last_checked: 2026-11-09
  note: "already in force on the last check; shared with the engineering team"

- when: "2 December 2026"
  what: "AI Act: labelling of generated content for AI systems already on the market"
  team: legal
  jurisdiction: "[EU]"
  source: "[37] https://lewissilkin.com/insights/2026/07/09/council-of-the-eu-gives-ai-omnibus-final-green-light-102nbb1"
  last_checked: 2026-11-09
  note: "shared with the engineering team"

- when: "2 December 2027"
  what: "AI Act: obligations for stand-alone high-risk AI systems"
  team: legal
  jurisdiction: "[EU]"
  source: "[37] https://lewissilkin.com/insights/2026/07/09/council-of-the-eu-gives-ai-omnibus-final-green-light-102nbb1"
  last_checked: 2026-11-09
  note: "shared with the engineering team; relevant only if a product is high-risk"

- when: "between the 5th and 6th year after registration, then every 10 years from registration"
  what: "US trademark maintenance: Section 8 declaration, then renewal"
  team: legal
  jurisdiction: "[US]"
  source: "[56] https://www.uspto.gov/trademarks/maintain/keeping-your-registration-alive"
  last_checked: 2026-11-09
  note: "registration dates come from the venture pages"
```

Rows owned by other teams that legal watches in the cross-domain events, without duplicating them here: the 83(b) election within 30 days of a restricted-stock transfer (people), a new 409A before grants after 12 months or a material event (ventures), CRA full application on 11 December 2027 (engineering), and the finance team's tax calendar.
