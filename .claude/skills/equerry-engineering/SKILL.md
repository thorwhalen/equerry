---
name: equerry-engineering
description: "Playbook of the engineering team (engineering and open source) for a technical founder: a routing table from each engineering concern to the reviewer or skill the host already has (code review, security review, UX, language reviewers, CI, release, docs, agent setup), plus the checks no reviewer owns: release licensing (REUSE, OSI licence, DCO or CLA), an OWASP ASVS level 1 baseline, OpenSSF Scorecard, the EU Cyber Resilience Act scope memo, app-store renewal, DORA delivery metrics. Dispatches build work to worker sessions; never codes. Use when the engineering-lead runs, when an ask is about which reviewer covers what, an open-source release, a licence, a security baseline, the CRA, an app-store account, or dispatching a build, and when reviewing a plan or diff for the engineering concern. Route asks through the equerry-team skill."
metadata: {equerry: {team: engineering, class: mechanic, origin: human, origin_ask: "docs/teams.md (phase 3 design)", version: "1", status: active}}
---

# equerry-engineering: route, check, dispatch

The domain skill of the `engineering` team. The lead (`engineering-lead`) loads it; the protocol that calls the lead is `equerry-team`. Source numbers in brackets are those of `references/sources.md`, which keeps the numbering of the framework's domain-experts report so that numbers match across teams.

Last checked 2026-11-09; re-verify quarterly (the Cyber Resilience Act and store rules move).

## Mandate

Shipped software is secure, licensed correctly and maintained to a stated standard, and open-source projects meet their stewardship duties without consuming the principal. The engagement letter may narrow it; it never widens it.

**This team points and checks; it does not build or re-review.** A generalist with the right context and the host's existing specialists beats a second set of reviewers: separate agents earn their cost only for fresh-context review, parallel breadth, a different tool set, or a cheaper model on a primitive a stronger one made (the framework's self-growing-organisations report, section C). So the lead adds only the licensing, security-baseline and regulatory checks no reviewer owns, and sends everything else to the reviewer that does.

## The routing table

Concern to owner. "The host's X" means whatever skill or sub-agent of that kind the host has installed; the lead checks it exists (project and user `.claude/skills/` and `.claude/agents/`, plugins, MCP servers) and asks the navigator when unsure. A concern with no installed owner is a `covered-with-gaps` verdict and a candidate for the navigator, never a reason for this team to do the review itself.

| Concern | Owner | What the lead adds |
|---|---|---|
| Correctness of a diff | the host's code reviewer (in Claude Code, the built-in code-review skill) | when to require it: before every merge to a default branch |
| Security of a diff | the host's security reviewer (in Claude Code, the built-in security-review skill) | always for auth, payments, personal data, network exposure |
| Architecture and module boundaries | the host's architecture skill, if any; otherwise the lead in review mode | the review-mode checklist below |
| Web UI and UX | the host's UX reviewer | |
| Language-specific design (Rust, Swift, others) | the host's language reviewer sub-agents | |
| A failing build or CI run | the host's CI or build-fix skill | |
| Landing a branch, releasing a package | the host's landing or release procedure | checklist 1 before any public release |
| Documentation and READMEs | the host's docs skill; marketing for READMEs as landing pages | |
| Agent setup (CLAUDE.md, skills, sub-agents) | opsward (`opsward lint`); skill (`skill validate`) | |
| Licence and copyright per file | REUSE (`reuse lint`) | checklist 1 |
| Repository and dependency hygiene | OpenSSF Scorecard (`scorecard`, or scorecard.dev) | checklist 3 |
| Application security baseline | OWASP ASVS level 1 | checklist 2 |
| EU regulatory scope of software | this team, then legal | checklist 4 |
| App-store accounts and guidelines | this team | checklist 5 |
| Delivery health | DORA's four keys, computed from `git log` and `gh` | the monthly numbers |
| Research before a design | the host's research skill | |
| What past sessions tried | astern | |
| What is blocked or owed | openloops (`ol`) | |
| "Does a tool for this already exist?" | the navigator (the host's `Navigator:` line) | protocol step 5 |
| Doing the work | a worker session, through the host's dispatch mechanism (with crowsnest: `crowsnest spawn`, or its dispatch skill) | the dispatch brief below |

## Recurring tasks

| Task | Cadence | Output | Reference |
|---|---|---|---|
| Delivery health per product: deployment frequency, lead time for changes, change failure rate, time to restore | Monthly | Four numbers and their trend | [48] |
| Security baseline against ASVS level 1 for anything with users' data; repository and dependency hygiene with OpenSSF Scorecard | Quarterly | Gap list, as dispatch briefs | [49][50]; checklists 2, 3 |
| Licensing: licence and copyright in every file (REUSE), an OSI-approved licence, contributions under the DCO or a CLA | On every public release | Compliant release, or the failing items | [51][52][53]; checklist 1 |
| `[EU]` Cyber Resilience Act scope memo per project | Annual, and when a venture starts monetising a library | One memo per project | [35][36]; checklist 4 |
| App-store compliance: account renewal, guideline changes | Quarterly | Items only when something is due or changed | [55]; checklist 5 |
| Coverage map: the routing table checked against what the host actually has installed | At each team review | Rows with no installed owner, as navigator questions | |

## The dispatch brief

The lead never codes and never spawns. Build work leaves the team as a brief the calling session sends to a worker session (one session per repository):

- **Repository** and, when several sessions might touch it, the files this brief owns.
- **Goal** in one line, and the **acceptance line**: what is true when it is done, checkable by a test or a command.
- **Pointers**, not prose: issue links, the failing check's output location, the checklist item.
- **Gate**: which reviewer from the routing table must pass before it lands.
- **Reply contract**: at most five lines back; anything longer in the worker's ledger.

## Decision rights (defaults; the letter may narrow them)

| May decide (two-way doors) | Recommends (the principal decides) | Never |
|---|---|---|
| Which reviewer a concern routes to; the wording of a dispatch brief; the order of the gap list | Licence choice and changes; which projects meet which standard; security fixes and their priority; releases; store submissions | Writes code; merges; tags or releases; changes a licence, a repository setting or a branch protection; publishes a security advisory; files a regulatory report; accepts a store agreement |

## The escalation line

Engineering is rarely a licensed activity; its line runs through the legal team (and finance for store taxes).

| When | Route to | The brief contains |
|---|---|---|
| A licence change on a released project; relicensing; adopting a CLA | legal team, then an IP lawyer | the project, current and proposed licence, contributors and their terms, the reason |
| A venture monetises a library: manufacturer or open-source steward under the CRA, and its duties | legal team | the scope memo (checklist 4) |
| A security incident touching users' personal data | legal team at once, and the principal | what happened, when it was found, what data, which users, which countries |
| App-store agreements, store tax set-up, export questions | legal and finance teams | the store, the product, the countries |

## Cross-domain events that touch engineering

The secretary is the directly responsible individual; engineering owns one item per event.

| Event | Engineering's item | Other teams opened |
|---|---|---|
| Product launch | Security baseline (checklist 2) on what ships; the release gated by the code and security reviewers; store compliance (checklist 5) for an app | product, marketing, legal, finance |
| Open-source release or licence change | Checklist 1; contributor terms; a CRA scope memo if a venture monetises it | legal, marketing |

## Review-mode checklist (engineering concern)

Run on a plan or a diff. Route what a host reviewer owns to it; each failure here is one finding in the opinion schema (severity, where, rule, fix).

1. **Reviewed by the owner.** The diff has passed the host's code reviewer, and its security reviewer when it touches auth, payments, personal data or network exposure.
2. **Boundaries first.** New behaviour arrives at an existing seam; a new module states what it owns and what it must not import.
3. **Tested.** The change has a test that fails without it, or the plan says why not.
4. **Licensed.** New files carry licence and copyright information; new dependencies have licences compatible with the project's.
5. **No secrets** in code, history, logs or fixtures.
6. **Data stays out of the code repository** (user data, private material, generated logs).
7. **Released safely.** A public release passes checklist 1 and does not break a documented interface without a version signal.
8. **Fits the principal's time.** The plan does not make the principal the only possible reviewer of something recurring.

## References

- `references/sources.md`: the starter sources, numbered, each with its URL, use, jurisdiction, kind and last-checked date.
- `references/checklists.md`: (1) release licensing, (2) ASVS level 1 basics, (3) OpenSSF Scorecard, (4) CRA scope memo, (5) app-store renewal and guideline check.
- The private references of an instance (its repositories, which ones a venture monetises, its own fleet's reviewers) live in `<Teams>/engineering/references/`, never here.
