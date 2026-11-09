# Engineering checklists

Each checklist is something the `checker` sub-agent can verify a deliverable against: every item is a yes or no with the evidence that makes it yes. The lead runs them read-only and reports the failing items as dispatch briefs; it never fixes. Numbers in brackets point to `sources.md`. Values marked "default" are the framework's; an engagement letter may set its own. Last checked 2026-11-09.

## 1. Release licensing (every public release)

- [ ] **One OSI-approved licence** for the project, its text in the repository, its identifier in the package metadata [53]. A licence that is not OSI-approved is a finding for legal, not a judgement here.
- [ ] **REUSE compliant**: every file carries copyright and licence information (an SPDX header, a `.license` sidecar for files that cannot hold a comment, or an entry in `REUSE.toml`); every licence used has its text in `LICENSES/`; `reuse lint` passes [52]. Without the tool installed, mark this "to verify" and put `reuse lint` in the dispatch brief.
- [ ] **Third-party code and assets** vendored into the repository keep their own licence and copyright lines, and those licences are compatible with the project's (incompatibility goes to legal).
- [ ] **Dependencies**: their licences listed and compatible with how the project is distributed (to verify with legal when a copyleft licence meets a permissive or proprietary product).
- [ ] **Contributions** come in under the Developer Certificate of Origin (`Signed-off-by` on each commit, checked in CI) or a contributor licence agreement, and the contributing guide says which [51]. Adopting or changing a CLA goes to legal.
- [ ] **No change of licence** in this release; if there is one, legal has signed off before the tag.
- [ ] **Release notes** mention any licence or contributor-terms change, drafted for marketing.
- [ ] **A venture monetises this project?** Then checklist 4 is current for it.

## 2. ASVS level 1 basics (quarterly, for anything with users' data)

A first pass against the baseline areas of the OWASP Application Security Verification Standard at level 1 [49]. Map each area to the current ASVS version's level 1 requirements before reporting (the numbering changed between versions; to verify each quarter).

- [ ] **Authentication**: passwords or tokens handled by a maintained library or provider; no default credentials; rate limiting on login.
- [ ] **Session management**: sessions expire, are invalidated on logout, and use secure, HTTP-only cookies.
- [ ] **Access control**: every request checks the user may act on that object, server-side; deny by default.
- [ ] **Input handling and output encoding**: input validated at the boundary; queries parameterised; output encoded for its context.
- [ ] **Cryptography**: no home-made cryptography; data in transit over TLS; secrets at rest encrypted where the threat model needs it.
- [ ] **Secrets**: none in code, history, logs, fixtures or client bundles; a secret that ever leaked has been rotated.
- [ ] **Error handling and logging**: errors shown to users carry no internals; security events logged without personal data beyond need.
- [ ] **Data protection**: personal data minimised, retention stated, deletion possible; the processing listed for legal's register.
- [ ] **Configuration and dependencies**: production debug off; security headers set; dependencies with known vulnerabilities updated or justified.
- [ ] Gaps listed by severity, each as a dispatch brief gated by the host's security reviewer.

## 3. OpenSSF Scorecard (quarterly, per public repository)

Run `scorecard --repo=<host>/<owner>/<repo>` (it needs a read-only token in the environment) or read the public result on scorecard.dev [50]. Record the overall score and every check below its threshold (default: below 7 out of 10).

- [ ] **Maintained**, **Security-Policy** (a `SECURITY.md` saying how to report a vulnerability), **License**.
- [ ] **Code-Review** and **Branch-Protection** on the default branch, as far as a solo maintainer can honestly have them.
- [ ] **Dangerous-Workflow** and **Token-Permissions**: no untrusted input in privileged CI; workflow tokens read-only by default.
- [ ] **Pinned-Dependencies** and **Dependency-Update-Tool**: CI actions and build dependencies pinned; an update bot or a stated update rhythm.
- [ ] **Vulnerabilities**: no open known vulnerabilities in dependencies, or each one justified.
- [ ] **Binary-Artifacts**: no unexplained binaries in the repository.
- [ ] **Signed-Releases** and **Packaging**: releases published from CI, signed or with provenance where the registry supports it.
- [ ] **CI-Tests** and **SAST**: tests run on every change; a static analyser where the language has a good one.
- [ ] Check names and thresholds confirmed against the current Scorecard documentation (to verify each quarter); findings as dispatch briefs.

## 4. Cyber Resilience Act scope memo `[EU]` (annual; when a venture monetises a library)

One memo per project. The determination of status is legal's; this memo prepares it [35][36].

- [ ] **Made available on the EU market in the course of a commercial activity?** Free and open-source software that its manufacturer does not monetise is not a commercial activity and is out of scope; a developer contributing to a project not under their responsibility is out of scope [35].
- [ ] **Who monetises it**, and how (a paid product built on it, paid support, a hosted service, a venture's revenue): the facts, with sources.
- [ ] **Candidate status**, marked "to verify with legal": out of scope; **manufacturer** (places the product on the market: full manufacturer obligations); or **open-source software steward** (a legal person giving sustained, systematic support to software intended for commercial activities without placing it on the market) [35].
- [ ] **If manufacturer**: reporting of actively exploited vulnerabilities and severe incidents applies from 11 September 2026: an early warning within 24 hours of becoming aware, a notification within 72 hours, a final report later, through ENISA's single reporting platform [36]. The project has a way to be told about vulnerabilities and a person who can file within those times.
- [ ] **If steward**: a cybersecurity policy for secure development and vulnerability handling; cooperation with market surveillance authorities; reporting duties from 11 December 2027; stewards are not subject to administrative fines [35][36].
- [ ] **What changes for this project**, as dispatch briefs (a security policy, a vulnerability intake, a software bill of materials where required) and as a brief for legal.
- [ ] The memo dated, with its sources and their last-checked dates; reviewed again at the next annual pass or when monetisation changes.

## 5. App-store renewal and guideline check (quarterly, per store account)

- [ ] **Account renewal**: the developer membership's renewal date for each store account, recorded as a dated item with a reminder well before it (default: 30 days); an Apple Developer Program membership renews yearly, and apps leave the store when it lapses [55].
- [ ] **Agreements**: any new or updated store agreement waiting for acceptance, routed to the principal (and legal when terms changed materially); never accepted by the team.
- [ ] **Guidelines**: changes to each store's review guidelines since the last check, read from the store's own pages (add each page to `sources.md` the first time it is used, with its date), and which shipped products they touch.
- [ ] **Products for children, with AI features, or collecting personal data**: the store's specific rules re-read, and legal's item opened.
- [ ] **Tax and banking set-up** in each store current, routed to finance (finance's source [54]).
- [ ] **Store listing** claims consistent with the positioning page, routed to marketing.
- [ ] Items reach the principal only when something is due within the window or a guideline change touches a shipped product.
