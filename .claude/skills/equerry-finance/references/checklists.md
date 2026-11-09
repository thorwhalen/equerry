# Finance team: checklists

Four procedures the finance lead runs and the `checker` verifies against. Each section ends with "Done when", the line the checker tests. Numbers in brackets are entries of `sources.md`. The team prepares; the principal or the accountant of record locks, pays and files.

## Month-end close

Per entity, after the last bank statement of the month is available.

1. Bank, card and payment-provider feeds complete to the last day of the month; each account reconciled to its statement balance (state the difference if any, never force it to zero).
2. Every transaction categorised; the uncategorised ones listed with amount and date.
3. Receivables: open invoices with their age; a chase draft for each one past its due date (a draft for the principal, never sent by the team).
4. Payables: what falls due in the next 30 days; nothing is paid by the team.
5. Recurring and annual items: subscriptions paid yearly spread or noted; accruals the accountant asked for.
6. Separation: personal expenses on a company account, or company expenses paid personally, flagged for reimbursement.
7. Tax lines captured: `[FR]` VAT collected and deductible for the period; `[US]` sales tax where the entity, not a store, is liable (app stores remit in most storefronts [54]).
8. Variance note: the month against the previous month and the budget, in plain sentences, every figure computed by code.
9. Handover: what the principal or the accountant must do to lock the month. Keeping, closing or reviewing the accounts of an entity other than the principal's own is reserved in France [62]: stop and brief.

Done when: every account has a reconciliation line, the uncategorised list is empty or stated, every overdue invoice has a draft, and the variance note's figures come from shown code.

## Monthly pack

Per entity with investors or a board, and one for the principal personally. Built after the close.

1. P&L, balance sheet, cash flow for the month and year to date.
2. One line: cash, burn, runway, change since last month; a 13-week cash view when runway is below the letter's floor [5].
3. The handful of KPIs the letter names, same definitions every month.
4. Variance commentary: what moved and why, in sentences.
5. Tax and filing dates in the next 60 days from `calendar.md` and the venture page.
6. Decisions the principal must take, each with options and the cash effect computed by code.
7. Every figure: its source (ledger export and date) and whether it is certain or an estimate.

Done when: the pack has all seven parts, every figure names its source, and every estimate is labelled.

## Admin estate sweep

Monthly, across every entity and the principal personally.

| Item kind | Fields |
|---|---|
| Subscription or tool | name, entity that pays, cost, cycle, next renewal, card or account it bills (by label, never the number), last used, owner |
| Domain | registrar, expiry, auto-renew on or off, entity that owns it, DNS host |
| App-store membership | store, entity, renewal date (yearly for the Apple Developer Program [55]), tax set-up state [54] |
| Insurance policy | insurer, cover, entity, renewal date, notice period |
| Cloud and platform account | provider, entity, monthly run rate, budget alert set or not |

1. Renewals in the next 60 days become items, with the decision needed (renew, cancel, downgrade).
2. Unused for 90 days, or duplicated by another tool: a cancel recommendation with the yearly saving computed by code.
3. Billed to the wrong entity, or personally for a company tool: flagged.
4. A domain or membership with auto-renew off and an expiry inside the window: a `blocker` item (a lapsed domain or store account is hard to recover).

Done when: every item on the previous sweep is accounted for (kept, cancelled, renewed), every renewal inside the window has an item, and every saving figure comes from shown code.

## Offer pricing

With the bizdev team, per offer and at the quarterly rate-card review. The rule: every number by code, shown; never mental arithmetic.

1. Separate the sense of the offer (what it does for the client) from its economics; both are judged, separately.
2. List the inputs, each **given** (source, date) or **estimate** (basis): billed days, unbilled preparation days, sales time per engagement, direct costs (travel, tools, venue), day rate or package price, number of engagements a year the time budget allows.
3. Compute with `python -c`, inputs printed, so the principal can change one and rerun. Example shape (the values are placeholders, not advice):

   ```bash
   python -c "
   billed_days = 2        # given: offer definition
   prep_days = 1          # estimate: unbilled preparation
   sales_days = 0.5       # estimate: unbilled selling per engagement
   price = 3000           # estimate: proposed package price, in the offer's currency
   direct_costs = 250     # estimate: travel and materials
   per_year = 10          # estimate: engagements the time budget allows
   margin = price - direct_costs
   effective_day_rate = margin / (billed_days + prep_days + sales_days)
   print(f'margin={margin:.0f} effective_day_rate={effective_day_rate:.0f} yearly_margin={margin * per_year:.0f}')
   "
   ```

4. Compare the effective day rate (unbilled time included) with the rate card and with dated market references; flag underpricing.
5. Name the tax treatment: VAT `[FR]` `[EU]`, sales tax `[US]`, which entity invoices; anything unsure is **to verify** with the accountant.
6. For training sold in France with public or pooled funding: the funding route's requirements are bizdev's and legal's check; note the BPF data the engagement will need [23].
7. Output: a table (inputs with labels, outputs), the code, the assumptions list, and a recommendation the principal decides. The team never quotes a price to a client.

Done when: the code runs as shown and reproduces every figure in the table, every input is labelled given or estimate, and the effective day rate includes unbilled time.
