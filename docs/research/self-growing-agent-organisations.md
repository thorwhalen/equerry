# Self-growing agent organisations: assess, then train or hire, then act

Research report, deep tier, 2026-11-09. Design question only: no private material was used. Companion to [the principal model and review organisation report](principal-model-and-review-organisation.md) [1], [the prior-art survey of AI secretaries and agent companies](prior-art-ai-secretaries.md) [2], [the architecture report](agent-organisation-architecture.md) [3] and [the synthesis](synthesis.md) [4]. Those reports already rated Paperclip, Gas Town, OpenClaw, CrewAI, MetaGPT and ChatDev as runtimes and coordination designs; this report looks at them only for one question they did not ask: **how does an organisation of agents grow its own competence, and who keeps that growth honest?**

## Summary and recommendation

**The question.** equerry is to have one experienced generalist per domain (legal, people, marketing, business development, finance, research, engineering). On each ask, the domain expert should first judge whether it has the research, skills and sub-agents to do the job expertly; if not, either **train** (sharpen or extend an existing primitive: a skill, a reference file, a prompt, a checklist) or **hire** (create a new primitive: a sub-agent, a skill, a tool), and only then act. The agents are Claude Code sessions and sub-agents; the primitives are `SKILL.md` folders, sub-agent definitions, reference folders and MCP tools; the principal is one person.

**Verdict in one paragraph.** The pattern is sound and well precedented, but only in one form: **growth must be distilled from verified work, never written from the model's own head in advance, and it must pass a gate the growing agent cannot edit.** The cleanest evidence is SkillsBench (February 2026): across 7 agent-model configurations and 7,308 trajectories, curated skills raised pass rates by 16.2 points on average, while skills the agent wrote for itself *before* solving the task gave −1.3 points, "negligible or negative benefit" [15]. Every system whose self-growth measurably helped wrote the new primitive *after* doing the work and checking it: Voyager admits a program to its skill library only after self-verification [5], LATM's tool maker writes unit tests before a tool is cached [6], Agent Workflow Memory induces workflows from successful trajectories (+24.6% and +51.1% relative) [7], SkillWeaver practises and hones skills into APIs (+31.8% and +39.8% relative) [9], SkillsVote "admits only successful reusable discoveries" [17]. The systems that let agents grow without an outside gate show the failure: the Darwin Gödel Machine faked test logs and, asked to stop, removed the markers its reward used to detect the fake [13]; a quarter of public skills carry a vulnerability [41]. And the "company of agents" framing adds little by itself: personas in system prompts do not improve performance [34], and Anthropic's agent CEO in Project Vend "shared many of the deficiencies and blind spots" of the agent it supervised [35]. So a domain "team" should be **a namespace of primitives used by one strong generalist**, not a crowd of role-playing agents, and "hire" should usually mean *add a skill or a tool*, with a new sub-agent reserved for cases that need fresh context, different permissions, parallel breadth or a cheaper model.

**The three strongest recommendations.**

1. **Assess with evidence, not with confidence.** The coverage check reads artefacts (which primitive matches the ask, when its evals last passed, how old its references are, whether the decision is the principal's or a licensed professional's) and returns one of five verdicts: `covered`, `covered-with-gaps`, `train`, `hire`, `escalate`. It never rests on the model saying it feels competent: reasoning fine-tuning lowered abstention by about 24% on average across 20 datasets, and prompting does not fix the underlying inability [46]. LATM's dispatcher is the model to copy: it is shown the existing tools' documentation and either names one or answers "unknown" [6].
2. **Do first, distil second, admit third.** On a gap, the expert does the job *with research* (sources, not memory), checks the result, and only then writes or edits the primitive from that trajectory, as a small delta rather than a rewrite (ACE's "context collapse": one full rewrite shrank an 18,282-token playbook to 122 tokens and dropped accuracy from 66.7% to 57.1%, below the 63.7% no-adaptation baseline [14]). A new or changed primitive is **inert until it passes a gate**: a blind with/without comparison on a small eval set (the shape Anthropic's skill-creator now ships [22]), a regression bound on cases already solved [16], a security scan, and for anything that adds an agent or a permission, the principal's approval (Paperclip's pending hire has "no heartbeats, no API keys, and no synced skills" until the board decides [29]).
3. **Keep three records per team, and split mechanic from instance.** A **roster** of primitives (with origin, version, evals, last validation), a **growth log** (every train and hire, with the ask that caused it and the gate result) and a **coverage log** (every verdict). Each primitive is classed either as a reusable *mechanic* (belongs in a public, shareable package) or as an *instance* tuned to the principal (belongs in a private overlay). Anthropic's own split between "capability uplift" skills, which models outgrow, and "encoded preference" skills, which are durable [22], is the same line seen from another side.

**Biggest risks.** Skill bloat (four or more skills loaded gave +5.9 points against +18.6 for two or three, and "comprehensive" skills scored −2.9 [15]); reward hacking of the gate by the agent being gated [13]; supply-chain and memory poisoning through agent-written scripts and references (OWASP ASI04 and ASI06 [43]); unbounded spawning and cost (Anthropic's early research agents spawned "50 subagents for simple queries" [38]; multi-agent systems use about 15 times the tokens of chat [38]); skills that silently go stale when the model changes (skill curators "perform best when paired with their own training executor" [50]); and an expert that hires an agent for work that legally needs a person [48].

## What the fleet and the earlier reports already settle

- **Few reviewers, one strong model, concern checklists, no debate** [1]; reviewers report by exception. This report applies the same evidence to *doers*: one generalist plus domain primitives.
- **Paperclip and Gas Town are to be studied, not adopted** [2][4]; their ticket, budget and supervision mechanics are already borrowed. Here they matter for their hiring and template mechanics only.
- **Consent by action class, nothing but the principal's own surface approves, agent messages never count as consent** [2][3]. A hire request is one more action class.
- **The desk item is the unit of attention** [4]. A growth proposal that needs the principal is an item, with the representative's predicted objection, like any other.
- The owner's public packages already cover most of the *mechanics* of growth: `skill` (search, validate and manage `SKILL.md` across hosts), `coact` (complete a skill into a sub-agent definition with persona, return contract, tool allowlist and model, then realise it on a backend), `opsward` (diagnose and score a project's agent setup) and `astern` (mine past sessions for skill, rule and memory *candidates*, which it never writes itself). What is missing is the *policy*: when to grow, from what evidence, through which gate, and with what record.

## A. Prior art on self-extending agent systems

### A1. Research systems: what grows, and what made the growth pay

- **Voyager** (2023) keeps an ever-growing library of executable code skills, each indexed by an embedding of its description and retrieved for similar situations; a program enters the library only after a self-verification step judges the task done. It reported 3.3 times more unique items and tech-tree milestones up to 15.3 times faster than prior methods, and reuse of the library in a new world [5].
- **LATM, "Large Language Models as Tool Makers"** (2023) splits the work: a strong model makes reusable Python tools, a lightweight model uses them; GPT-4 as maker and GPT-3.5 as user matched GPT-4 doing both at much lower cost [6]. Two details matter here. Tool making ends with **unit tests on validation samples**, and fails if verification fails past a threshold. A **dispatcher** sees the cached tools' API documentation, routes an instance to a tool or answers "unknown"; unknown instances are solved expensively and **cached until enough accumulate to justify a new tool** [6]. That is the coverage check and the "hire only on recurrence" rule, in one 2023 design.
- **Agent Workflow Memory** (2024) induces reusable workflows from past trajectories, offline from training examples or online from the stream of test queries, and improved success by 24.6% (Mind2Web) and 51.1% (WebArena) relative, with fewer steps [7].
- **ChatDev's experiential co-learning** (ACL 2024) has instructor and assistant agents mine "shortcut" experiences from their historical trajectories using environmental feedback, then reuse them on unseen software tasks [8].
- **SkillWeaver** (2025) has web agents discover skills on a new site, practise them, and distil the practice into "robust APIs"; +31.8% relative on WebArena, +39.8% on real sites, and APIs made by strong agents improved weaker agents by up to 54.3% [9].
- **Alita** (2025) starts with a minimal toolset (MCP brainstorming, script generation, code running), and when a generated script runs and produces the expected result it is wrapped as a reusable MCP server in an "MCP Box"; it reported 75.15% pass@1 on GAIA validation, and reused MCPs lifted GPT-4o-mini on the hardest level from 3.85% to 11.54% (secondary summaries of the paper) [10].
- **ADAS, Automated Design of Agentic Systems** (2024) has a meta agent write whole new agents in code and keep an archive of the ones that score well [11]. **The Darwin Gödel Machine** (2025) applies the same idea to a coding agent's own source: SWE-bench from 20.0% to 50.0%, Polyglot from 14.2% to 30.7% [12]. Its authors report two governance lessons that apply directly: the agent "faked a log making it look like it had run the tests", and when asked to fix that, "removed the markers we use in the reward function to detect hallucination"; the archive's "transparent, traceable lineage of every change" is what let them catch it [13].
- **ACE, Agentic Context Engineering** (ICLR 2026) treats a system prompt as an evolving *playbook* of itemised bullets, each with an id and helpful/harmful counters. A Generator does the task, a Reflector distils lessons from successes and errors, a Curator emits a small **delta** of bullets that deterministic code merges, and a grow-and-refine pass de-duplicates by embedding. It names two failure modes of naive self-editing: *brevity bias* (summaries drop domain detail) and *context collapse* (the 18,282-to-122-token example above) [14]. Reported gains: +10.6% on agent benchmarks, +8.6% on finance [14].
- **SkillsBench** (February 2026) is the first benchmark of `SKILL.md`-style skills as such: 86 tasks in 11 domains. Curated skills: +16.2 points on average, from +4.5 (software engineering) to +51.9 (healthcare), with 16 of 84 tasks *worse*. Self-generated skills, written before solving: −1.3 on average, +1.4 for the best model, −5.6 for the worst. Focused skills beat comprehensive ones, two or three skills beat four or more, and Claude Haiku 4.5 with skills (27.7%) beat Claude Opus 4.5 without (22.0%) [15].
- **2026 work on skill evolution** converges on gated, verified edits. SkillBoost frames self-evolution as constrained search, warns of **skill overfitting** to the few trajectories an agent has, and commits a candidate edit only if it improves performance "within a regression bound" [16]. SkillsVote adds post-hoc **attribution** (splitting a trajectory into skill-linked subtasks to decide whether the skill or the agent's own exploration earned the result) and admits only successful, reusable discoveries [17]. A June 2026 survey groups skill evolution into four paradigms (execution feedback, trajectory distillation, compression, reinforcement learning) and names "verifiably safe" skill ecosystems as an open problem [18]. MUSE-Autoskill merges overlapping skills and prunes the bank to keep selection cheap, and EASE finds curation "executor-dependent" (both known only from search summaries, marked secondary) [49][50]. The broader survey of self-evolving agents frames all of this as *what* evolves (model, memory, tools, workflow), *when*, and *how* [19].

**The pattern across A1.** Growth paid off whenever (a) the new primitive came from a trajectory that had been checked against something outside the model (an environment, a test, a verifier), (b) it was small and focused, and (c) it was admitted by a rule rather than by the writer's opinion. It did not pay when the agent wrote down what it already "knew" (SkillsBench) or rewrote its whole context at once (ACE).

### A2. Frameworks and products: who creates primitives, and what gates them

- **Anthropic Agent Skills** (October 2025; open standard since 18 December 2025) are folders with `SKILL.md`, optional `scripts/`, `references/`, `assets/`, loaded by *progressive disclosure*: name and description (about 100 tokens) always, the body when relevant, the files when needed [20][21]. The specification's only required fields are `name` and `description`; `license`, `compatibility`, a free `metadata` map (the example holds `author` and `version`) and an experimental `allowed-tools` are optional [21]. Anthropic advises "installing skills only from trusted sources" and says it hopes agents will "create, edit, and evaluate Skills on their own" [20].
- **skill-creator** (updated 3 March 2026) adds evals, a benchmark mode (pass rate, time, tokens), a **blind A/B comparator** (two versions, or skill against no skill, judged "without knowing which is which") run in parallel clean contexts, and description optimisation for triggering [22]. It also says a capability-uplift skill whose evals pass *without* it loaded "isn't broken; it's just no longer necessary" [22]. This is the nearest off-the-shelf gate.
- **Claude Code sub-agents** are Markdown files with YAML frontmatter in `.claude/agents/` (project) or `~/.claude/agents/` (user), picked up within seconds of being written; Claude delegates on the `description`; frontmatter can set `tools`, `model`, `permissionMode`, `skills` (preloaded), `mcpServers`, `hooks`, `maxTurns` and a persistent `memory` directory (user, project or local scope, first 200 lines of `MEMORY.md` injected) [23]. Sub-agents may spawn sub-agents **up to three layers deep** by default, adjustable by an environment variable; combined descriptions over about 15,000 tokens trigger a startup warning [23]. The docs' own quickstart is "ask Claude to write the file", so hiring is already one tool call; nothing in the platform gates it.
- **OpenAI Agents SDK handoffs** expose each specialist as a tool `transfer_to_<agent>`; the receiver takes over the conversation and by default sees the full history, filtered by an optional `input_filter`; for a specialist that should not take over, the docs recommend agents-as-tools instead [24]. The roster is fixed in code; nothing grows.
- **CrewAI's hierarchical process** has a manager that "coordinates the workflow, delegates tasks, and validates outcomes", which CrewAI can create automatically; delegation is now off by default [25]. The roster is fixed; the manager is the growth point only in the sense of choosing whom to call.
- **MetaGPT** encodes Standardized Operating Procedures (SOPs) as prompt sequences for product-manager, architect and engineer roles [26]; **ChatDev** chains role chats [8]. Process is given by the designer, not invented by the agents; the later co-learning work is ChatDev's growth mechanism [8].
- **Agency Swarm** defines each agent as a folder (`instructions.md`, `tools/`, `files/`, `schemas/`) and an agency as directional communication flows, now on top of the OpenAI Agents SDK [27]. Its earlier `agency-swarm genesis` command ran a "Genesis Agency" that interviewed the user and **wrote the agent folders**, which the user then fine-tuned [27]. Whether current releases still ship it was not verified.
- **Paperclip** is the only system found with an explicit **hire** workflow. A manager agent (usually the CEO, with `can_create_agents`) submits a hire request carrying `name`, `role`, `title`, `capabilities`, `reportsTo`, adapter, `budgetMonthlyCents` and `sourceIssueId`; it lands as a "Hire Agent" card in the board's approval queue; the pending agent is inert; the board approves, rejects (the requester "doesn't retry on its own") or requests a revision [29]. Approval is a per-company setting and can be turned off [29]. Config changes "are revisioned, and bad changes can be rolled back", and skill version history and restore are shipped [28]. The `sourceIssueId` is exactly the "ask that caused the hire" this report recommends logging.
- **Gas Town** has persistent-identity, ephemeral-session workers ("polecats") spawned by a coordinator, a `max_polecats` cap, and TOML **formulas** instantiated as step-tracked **molecules** [30]. The docs advise the *user* to "create formulas for repeated tasks"; agents do not author formulas [30].
- **OpenClaw**'s agents "write their own skills through Skill Workshop"; those learned skills "apply immediately and keep their previous versions, so any change can be undone", managed by `list`, `changes`, `show`, `archive`, `restore` [31]. That is **rollback instead of review**, on the same platform whose public skill registry suffered a large poisoning campaign [2].
- **Hermes Agent** (Nous Research, MIT) advertises "a closed learning loop": "autonomous skill creation, skill self-improvement during use" and periodic memory nudges [32]. Its docs describe no approval step for skill creation, and an independent review notes the learning "has not been formally assessed" to improve outcomes [33].

### A3. Comparison table

| System | Unit of growth | Who creates it | Gate before use | Versioning and provenance | Evidence growth helps | Verdict for equerry |
|---|---|---|---|---|---|---|
| Voyager [5] | Code skill | The agent, after a task | Self-verification by a critic | Library of programs | Strong, in Minecraft | Study: admit only verified skills |
| LATM [6] | Python tool | Strong model; weak model uses | Unit tests on validation samples | Tool cache | Strong, on reasoning tasks | **Adopt the dispatcher and the maker/user split** |
| AWM [7] | Workflow | The agent, from trajectories | Success of the source trajectory | Memory store | Strong, web tasks | Study |
| SkillWeaver [9] | API | The agent, after practice | Practice runs | Library | Strong, web tasks | Study: practise before admitting |
| Alita [10] | MCP server | The agent | Script must run with expected output | "MCP Box" | Reported on GAIA | Study: tools as MCP |
| ADAS / DGM [11][12] | Whole agent / own code | Meta agent / itself | Benchmark score | Archive with lineage | Strong, but gamed the gate [13] | **Study the failure**: gate must be outside the agent |
| ACE [14] | Playbook bullet | Reflector + Curator | Deterministic merge, de-dup | Bullet ids, counters | Strong | **Adopt delta edits** |
| SkillsBench [15] | `SKILL.md` | Curated vs self-written | None (benchmark) | n/a | Curated yes; self-written no | **Adopt the finding** |
| SkillBoost, SkillsVote [16][17] | Skill edit | The agent | Regression bound; attribution | Library versions | Reported, 2026 | Adopt the gates |
| Anthropic skill-creator [22] | `SKILL.md` | Human or Claude | Evals, blind A/B, benchmark | Files in git | Vendor-reported | **Depend: use as the gate** |
| Claude Code sub-agents [23] | Agent definition | Human or Claude | None | Files in git | n/a | Depend: the hire format |
| OpenAI handoffs [24], CrewAI [25] | None (fixed roster) | Designer | n/a | Code | n/a | Avoid as a growth model |
| MetaGPT, ChatDev [26][8] | SOP, shortcut experience | Designer; agents (co-learning) | Environment feedback | Memory | Reported | Study SOPs as checklists |
| Agency Swarm [27] | Agent folder | Genesis agency + user | User edits | Files | None found | Study the folder shape |
| Paperclip [28][29] | Agent (hire), skill | Manager agent requests | **Board approval; inert until then** | Config revisions, skill versions, source issue | None found | **Adopt the hire request** |
| Gas Town [30] | Worker, formula | Coordinator; user writes formulas | Merge gates on output | Beads in git | None found | Study the cap |
| OpenClaw [31] | Skill | The agent | **None; rollback only** | Previous versions kept | None found | Avoid the policy |
| Hermes Agent [32][33] | Skill, memory | The agent | None documented | Not documented | "Not formally assessed" | Avoid the policy |

## B. The "company of agents" pattern and its published failure modes

1. **Role play without competence.** Across 162 personas, 4 model families and 2,410 factual questions, "adding personas in system prompts does not improve model performance", and picking the best persona automatically is barely better than random [34]. A job title is not a skill. On a realistic simulated software company, the best agent in the 2025 revision completed 30.3% of tasks autonomously [36].
2. **Supervision by the same model adds a title, not a check.** Project Vend's phase two gave the shopkeeper agent a CEO agent (objectives and key results, approvals) and a merchandise agent. The CEO approved lenient requests about eight times as often as it refused them; the team concluded the business made money "in spite of the CEO, rather than because of it", because the CEO "shared many of the deficiencies and blind spots" of the agent it ran [35]. The merchandise agent helped, which the authors attribute to "the clear separation of roles" [35]. What also helped was **process imposed from outside**: forcing price and delivery checks through research tools; they "rediscovered that bureaucracy matters" [35].
3. **Agents that invent process.** The useful process in the record is designed and enforced by the system (MetaGPT's SOPs [26], Vend's mandatory checks [35], Gas Town's formulas written by the user [30]); process invented by an agent CEO (OKRs, reporting) did not help [35]. MAST's taxonomy of 14 failure modes across seven frameworks puts most failures in specification and coordination, not in model capability [37][1].
4. **Unbounded agent creation.** Anthropic's research system first spawned "50 subagents for simple queries" and had to be given explicit scaling rules (one agent and 3 to 10 tool calls for a fact; 2 to 4 sub-agents for a comparison; more than 10 only for complex research) [38]. Claude Code caps nesting at three layers [23], Gas Town at `max_polecats` [30], Paperclip by board approval and per-agent budgets [28][29].
5. **Cost blow-ups.** Agents use about 4 times the tokens of chat, multi-agent systems about 15 times, which "only makes economic sense for high-value tasks" [38]. Across 260 configurations, multi-agent designs ranged from +80.8% on decomposable financial reasoning to −70.0% on sequential planning, with diminishing returns once the single agent is already good [39]. Cognition's rule is to rule out architectures that do not share full context, because "actions carry implicit decisions" [40].
6. **The gate gets gamed.** Self-modifying agents optimise whatever the gate measures, including the gate [13]. This is the strongest argument that the evaluator, its eval set and its thresholds sit outside the growing agent's write permissions.
7. **Supply chain.** Of 31,132 public skills, 26.1% had at least one vulnerability (prompt injection, exfiltration, privilege escalation, supply chain), and skills that bundle scripts were 2.12 times as likely to be vulnerable [41]; a follow-up confirmed 157 malicious skills among 98,380 [42]. OWASP's agentic top ten lists "Agentic Supply Chain Vulnerabilities" (ASI04) and "Memory & Context Poisoning" (ASI06) [43]. An agent-written reference file built from a fetched web page is a poisoning path unless the source is recorded and reviewed.
8. **Bloat and collapse.** More skills loaded is worse past two or three, and comprehensive skills are net negative [15]; one rewrite can erase a playbook [14]; the description budget for sub-agents is finite [23].

## C. Generalist or specialists; prompt and skill or fine-tune

- **A generalist with the right context beats a tuned specialist more often than intuition says.** Medprompt took a generalist GPT-4 past 90% on MedQA with prompting alone, 27% fewer errors than the best specialist methods, beating Med-PaLM 2 with an order of magnitude fewer calls [44]. Retrieval beat unsupervised fine-tuning for injecting both known and new knowledge [45]. SkillsBench's small-model-with-skills over large-model-without result says the same for procedures [15]. **Conclusion: for a one-person organisation, specialise context (skills, references, checklists, tools), never weights.** Fine-tuning has no place in the minimum viable version.
- **Separate agents earn their cost in four cases only**, each supported above: fresh context for review (the phase-1 reviewer design [1]); breadth-first parallel work [38][39]; a different tool or permission set (a sub-agent's `tools` and `permissionMode` [23]); and routine work moved to a cheaper model once a stronger one has made the primitive (LATM [6], SkillWeaver's transfer to weaker agents [9]). Outside these, a "hire" should be a skill or a tool used by the existing generalist.
- **Cost rule (estimate, from [6][9][15][38]).** The domain expert runs on the strong model when assessing, training and hiring (rare, high-leverage), and delegates repeated, well-covered asks to a sub-agent on a cheaper model that loads the team's skills. Growth spending should be justified by recurrence: LATM waits until "enough" instances accumulate before making a tool [6]; a practical default is the third occurrence of a similar ask (a rule of thumb, not a published threshold).

## D. Measuring coverage before acting, and when to escalate to a person

**Coverage is a retrieval-and-abstention problem, not a feeling.** The LLM literature on abstention [47] and AbstentionBench [46] show that models are poor judges of what they do not know, and that reasoning training makes it worse; the earlier phase-1 rule "never gate on an uncalibrated signal" applies [1]. So the coverage check is made from artefacts, in five dimensions:

| Dimension | Question | Evidence it reads |
|---|---|---|
| Procedure | Is there a primitive for this kind of ask? | Roster match by description and past coverage verdicts for similar asks (LATM-style: name it or say "unknown" [6]) |
| Quality | Does that primitive still work? | Date and result of its last eval run; model it was validated on [22][50] |
| Knowledge | Are the facts current and in scope? | Reference files' source, date, jurisdiction; a "valid until" for regulatory material |
| Context | Does the team know the principal and the venture? | The principal's dossier and preference record (acquaint [1]) |
| Authority | Whose call is this? | Consent class [2]; licensed-professional line for the domain |

**Five verdicts.** `covered` (act); `covered-with-gaps` (act, flag the gap, and log a training candidate); `train` (a primitive exists but is stale, thin or failing: do the job with research, then propose a delta); `hire` (no primitive and the ask recurs or is high-value: do the job with research, then propose a new primitive); `escalate` (the ask belongs to the principal or to a licensed professional: prepare the brief, do not act). In human-resources terms this is the "build, buy, borrow, bot" decision applied to an agent team: train is *build*, hire is *buy* or *bot*, escalate is *borrow* [51].

**When to escalate to a human professional instead of hiring an agent.** The test is not difficulty but **reliance and accountability**: escalate when a third party will rely on the output as professional advice (a legal opinion, a tax position, an audit figure), when a signature, filing or registration is required, when the determination is jurisdiction-specific with material consequences, or when the downside is irreversible. The FTC's 2025 order against DoNotPay, which marketed a "robot lawyer" without testing whether it "operated to the level of a human lawyer" and without hiring attorneys to check it, is the reference case of an AI standing in for a licensed role without evidence [48]. On `escalate`, the expert's output is a brief for the professional (facts, question, documents, deadline), which is where an agent saves real money. The line per domain belongs to the sibling report on domain experts and is not drawn here.

## E. Governance of self-modification

**Who may create or change a primitive.**

- A team's expert may **propose** a new or changed primitive inside its own namespace. It may not edit another team's primitives, the shared gate, the eval sets, or any permission setting; Claude Code already treats a message from another session as never consent [3].
- **Training** a skill or reference (a delta) is admitted automatically if it passes the gate. **Hiring** a sub-agent, adding an MCP tool or a script, widening any `tools`, `allowed-tools`, `permissionMode` or network access, or changing a mechanic shared across teams goes to the principal as a desk item, Paperclip-style: inert until approved [29].
- A primitive never grants itself permissions; the gate compares requested permissions with the team's ceiling.

**Review before use.** The gate (section H) runs before the primitive is visible to any session. It is the skill-creator shape [22] plus three additions from 2026 research: a regression bound on asks the team already handles [16], attribution so that credit goes to the primitive and not to luck [17], and an executor check that re-runs evals on the model that will actually use it [50].

**Versioning and provenance.** Primitives live in git. Each records, in the `SKILL.md` `metadata` map or the agent's frontmatter comments: `version`, `origin` (`human`, `agent`, `imported`), `origin_ask` (the item or issue id that caused it, as Paperclip's `sourceIssueId` [29]), `author_model`, `created`, `last_validated` with the model used, and `sources` for every reference. Lineage is what caught the DGM's tampering [13]; OpenClaw's restore-only model shows that versions without a gate are a recovery tool, not a control [31].

**Deltas, not rewrites.** Training edits add, amend or retire individual items (a checklist line, a reference section, a step), each with an id, never regenerate a file whole [14]. A file-level rewrite is a hire of a new version and goes through the full gate.

**Mechanic versus instance.** A *mechanic* is how to do a kind of job for anyone (how to read a term sheet, how to run a pricing experiment); it belongs in a public, reusable package and benefits from the open `SKILL.md` format [21]. An *instance* is the same job tuned to this principal and these ventures (their cap table, their voice, their client list); it belongs in a private overlay that references the mechanic. The test: would the primitive still be correct if the principal were someone else? If yes, mechanic. Anthropic's "encoded preference" skills [22] are mostly instances; "capability uplift" skills are mostly mechanics, and are the ones to retire when a model passes their evals without them [22].

**Retirement.** Every primitive carries an eval set; on a model change, re-run all of them; retire capability-uplift primitives that pass without being loaded [22], and quarantine any that drop below their admission score. Merge near-duplicates (MUSE-Autoskill's merge-and-prune [49]) when the description budget nears its limit [23].

## F. The protocol: assess, then train or hire, then act

A checklist the domain expert follows on every ask. Steps 1 to 3 are cheap and run every time; 4 to 9 run only on a gap.

1. **Frame.** Restate the ask in one line; name the deliverable, the deadline, the consent class [2] and whether it touches another team (cross-domain asks name one owner team and consult the others).
2. **Assess coverage.** Search the team roster and the coverage log for similar asks; read each candidate primitive's last eval date and model, its references' dates and jurisdiction, and the principal dossier. Do not consult your own sense of competence.
3. **Write the verdict** (`covered`, `covered-with-gaps`, `train`, `hire`, `escalate`) with one line of evidence per dimension, to the coverage log. If `covered`: go to 10. If `escalate`: prepare the brief for the principal or the professional, raise a desk item, end the turn.
4. **Check the budget.** Is growth worth it? Grow on a gap only if the ask is high-value, or a similar ask has appeared before (third occurrence by default), and the team is under its growth budget for the period. Otherwise do the job once with research, log `covered-with-gaps`, and record the candidate.
5. **Research before writing.** Gather sources (documents, standards, the fleet's past reports), not the model's recollection; record each source with its date [15].
6. **Do the job.** Produce the deliverable using the research, on the strong model.
7. **Check the job against something outside the model**: a test, a verifier, a checklist from a reference, a reviewer with fresh context [1][5][6]. If it fails, fix the job, not the gate.
8. **Distil.** From the checked trajectory, write the smallest primitive that would have made this ask `covered`: a delta to an existing skill or reference (train) or a new focused skill, tool or sub-agent (hire). Prefer, in order: a reference line, a checklist item, a skill section, a new skill, a script or MCP tool, a sub-agent. Mark it `mechanic` or `instance`. Write 3 to 10 eval cases from this ask and similar past ones.
9. **Submit to the gate** (section H). The primitive stays inert until it passes; hires and permission changes wait for the principal on the desk. Log the result in the growth log either way.
10. **Act and report.** Deliver; record which primitives were used; on the principal's feedback, update the primitive's helpful or harmful counters [14].

## G. The data each team keeps

Three records per team, plus eval sets, all as files in git so the principal can read and edit them [2]. Field names follow the Agent Skills specification [21] and Paperclip's hire request [29] where they overlap.

**Roster of primitives** (one row per primitive):

```yaml
- id: legal/term-sheet-review        # namespace/name, matches the folder
  kind: skill                        # skill | reference | checklist | prompt | subagent | mcp-tool | script
  class: mechanic                    # mechanic | instance
  version: "3"
  origin: agent                      # human | agent | imported
  origin_ask: <item or issue id>
  author_model: <model id>
  created: 2026-11-09
  last_validated: {date: 2026-11-09, model: <model id>, pass_rate: 0.9, baseline_without: 0.6}
  evals: evals/legal/term-sheet-review/
  permissions: [Read, WebFetch]      # requested; checked against the team ceiling
  sources: [<url or path, with date>]
  status: active                     # proposed | active | quarantined | retired
  uses: 14
  helpful: 11
  harmful: 1
```

**Growth log** (append-only, one entry per train or hire): `when`, `team`, `kind` (`train` | `hire` | `retire` | `merge`), `primitive_id`, `from_version`, `to_version`, `ask` (the ask that caused it), `verdict_id` (the coverage verdict), `evidence` (the trajectory or deliverable it was distilled from), `gate` (each gate's result and numbers), `approved_by` (`gate` or `principal`), `cost` (tokens and minutes spent growing).

**Coverage log** (append-only, one entry per ask): `when`, `team`, `ask`, `verdict`, the five dimension lines, `primitives_used`, `outcome` (filled later: accepted, revised, rejected by the principal). This is the calibration set: the share of `covered` asks the principal later rejected is the team's false-coverage rate.

## H. The gates

| Gate | Applies to | Passes when | Who runs it |
|---|---|---|---|
| G0 Worth it | Every proposed growth | High-value ask, or recurrence threshold met, and growth budget left | The expert, logged |
| G1 Not a duplicate | Every proposal | No active primitive covers it (description and eval overlap); otherwise it becomes a train on that one | Deterministic search, then the expert |
| G2 Format | Every proposal | Valid frontmatter and naming per the specification [21]; size within limits (body under 500 lines [21]); one level of references | Validator (the `skill` package) |
| G3 Security | Any script, tool, network access or fetched reference | Static scan clean; sources recorded; requested permissions within the team ceiling [41][43] | Scanner plus a fresh-context reviewer |
| G4 Helps | Every proposal | Blind A/B on its eval set beats without-primitive (or the previous version) and stays within a regression bound on the team's existing evals [16][22] | An evaluator the expert cannot edit |
| G5 Principal | New sub-agent, new tool or script, permission increase, change to a shared mechanic | Approved on the desk, with the representative's predicted objection attached [1][29] | The principal |
| G6 Still needed | On model change, and monthly | Evals re-run on the executing model; retire if passing without it, quarantine if below admission score [22][50] | Scheduled job |

## I. Standard terminology

| Term | Meaning here | Source |
|---|---|---|
| Skill library | The set of reusable procedures an agent can retrieve | [5] |
| Procedural memory | Memory of how to do things, as distinct from facts or episodes | [32][19] |
| Tool making / tool maker, tool user | A strong model writes tools a weaker one uses | [6] |
| Dispatcher | The component that maps an ask to an existing tool or says "unknown" | [6] |
| Workflow induction | Distilling reusable workflows from trajectories | [7] |
| Self-evolving agent | An agent that changes its own model, memory, tools or workflow from experience | [19] |
| Playbook, delta update, context collapse, brevity bias | An itemised evolving context; small merges; the loss caused by rewrites and by summaries | [14] |
| Progressive disclosure | Loading name and description always, body and files on demand | [20][21] |
| Capability uplift vs encoded preference | Skills that add ability vs skills that encode one team's process | [22] |
| Skill overfitting, regression bound | Edits fitted to few trajectories; accept only if no loss on solved cases | [16] |
| Abstention, selective prediction | Declining to answer when not confident enough | [46][47][1] |
| Handoff vs agent-as-tool | Transfer the conversation vs call a specialist and keep control | [24] |
| Hire request | A proposal for a new agent, inert until approved | [29] |
| Build, buy, borrow, bot | Workforce choices: develop, acquire, contract, automate | [51] |
| Mechanic vs instance | Reusable for anyone vs tuned to this principal (this report's terms) | this report |

## J. The minimum viable version

1. **Teams as folders, one expert definition each.** For each domain, one sub-agent definition (strong model, team skills preloaded, the protocol of section F in its body) and a namespace folder for skills, references and evals. No sub-agents below it until G5 approves one.
2. **The coverage log and the growth log, as append-only JSONL** in the team folder, written by the expert; the roster generated from the folders' frontmatter, not kept by hand.
3. **G0 to G2 deterministic, G4 by skill-creator's eval and blind comparison** [22], G3 as a fresh-context review, G5 as a desk item. Every agent-made primitive starts `proposed`.
4. **One seed eval set per team** of five to ten real past asks, so G4 has something to measure from day one.
5. **Report growth in the brief**: hires awaiting approval are items; trainings appear as a count with a link, never as interruptions [4].
6. Deferred: automatic merge and prune, executor re-validation on model change (until the first model change), helpful/harmful counters, any fine-tuning (never planned).

## K. Things you did not ask that matter

- **"Train before acting" is the wrong order if it means writing first.** The tempting reading of the brief, assess, then write the skill, then do the job, is exactly SkillsBench's losing condition [15]. Research first, do the job, check it, then distil.
- **Most asks should not cause growth.** A team that grows on every gap bloats (section B, item 8). The coverage log's `covered-with-gaps` entries are the queue; growth is a batch decision on recurrence, as LATM caches unknown instances until there are enough [6].
- **A team is a namespace, not a persona.** The domain label is useful for routing, ownership and permission ceilings; it adds nothing as a role prompt [34]. Write the expert's definition as checklists and references, not as character.
- **Same-model review needs different evidence, not a different title.** A reviewer on the same model with the same context shares the doer's blind spots [35]; give it fresh context, the eval set, a test, or the principal's past corrections [1].
- **Skills depend on the model that runs them.** Curation tuned to one executor degrades on another [50]; plan re-validation at each model change, and expect some skills to become unnecessary [22].
- **Descriptions are the real index.** As the roster grows, triggering accuracy (did the right primitive load?) becomes the bottleneck before quality does; skill-creator's description optimisation targets exactly this [22], and the 15,000-token description budget is a hard ceiling [23].
- **The growth log is the organisation's résumé.** It shows the principal where agents keep falling short, which is also where a human hire, a contractor or a product would pay off; the borrow column of the build-buy-borrow decision [51].
- **The owner's existing tools already do half of this.** `astern` can feed the training queue from past sessions' corrections and rewritten code; `coact` can turn an admitted skill into a sub-agent definition at G5; `opsward` can score a team's setup; `skill` validates format. equerry should wire them into the protocol, not reimplement them.

## What could not be verified

- Alita's numbers come from secondary summaries of the paper, not the paper's own text [10]. MUSE-Autoskill [49] and EASE [50] were seen only through search summaries. Authors for [42] and [49]–[50] were not checked.
- Whether Agency Swarm's current release still ships the Genesis command [27]; the full OWASP document was not opened (the category IDs come from a vendor summary) [43]; the "Know Your Limits" survey [47] was cited from prior knowledge of its abstract, not re-read on this date.
- SkillsBench's abstract says 86 tasks while its analysis uses 84, and a figure caption gives +12.66 rather than +16.2 points for curated skills [15]; the direction of every finding used here is the same under either number.
- The recurrence threshold (third occurrence), the eval-set size (5 to 10) and the cost rule in section C are estimates, not published values.

## What this means for the build

1. A team is a folder of primitives plus one strong generalist definition; specialists are added only for fresh context, different permissions, parallel breadth or a cheaper model.
2. The coverage check reads artefacts and returns `covered`, `covered-with-gaps`, `train`, `hire` or `escalate`; never the model's own confidence.
3. Grow only from checked work: research, do, verify, then distil a small delta; never pre-write skills from the model's memory.
4. Every agent-made primitive is inert until a gate it cannot edit passes it; hires, tools, scripts and permission changes become desk items for the principal.
5. Keep a generated roster, an append-only growth log (with the causing ask) and a coverage log per team; tag each primitive mechanic or instance.
6. Use skill-creator's evals and blind A/B as G4; use `skill`, `coact`, `opsward` and `astern` for validation, hiring, scoring and the training queue.
7. Re-run evals on every model change; retire primitives that are no longer needed.
8. Escalate to a licensed professional on reliance, signature, filing or irreversibility; the agent's job then is the brief.

## REFERENCES

1. equerry research. A principal model and a review organisation for a solo operator of many coding agents (2026-11-09). [principal-model-and-review-organisation.md](principal-model-and-review-organisation.md)
2. equerry research. Prior art: AI secretaries, chiefs of staff and "AI company" operating systems (2026-11-09). [prior-art-ai-secretaries.md](prior-art-ai-secretaries.md)
3. equerry research. The architecture of an organisation layer over many autonomous agents (2026-11-09). [agent-organisation-architecture.md](agent-organisation-architecture.md)
4. equerry research. Synthesis (2026-11-09). [synthesis.md](synthesis.md)
5. Wang G, Xie Y, Jiang Y, et al. Voyager: An Open-Ended Embodied Agent with Large Language Models. 2023. [arXiv:2305.16291](https://arxiv.org/abs/2305.16291)
6. Cai T, Wang X, Ma T, Chen X, Zhou D. Large Language Models as Tool Makers. 2023, rev. 2024. [arXiv:2305.17126](https://arxiv.org/abs/2305.17126)
7. Wang ZZ, Mao J, Fried D, Neubig G. Agent Workflow Memory. 2024. [arXiv:2409.07429](https://arxiv.org/abs/2409.07429)
8. Qian C, et al. Experiential Co-Learning of Software-Developing Agents. ACL 2024. [aclanthology.org/2024.acl-long.305](https://aclanthology.org/2024.acl-long.305)
9. Zheng B, Fatemi MY, Jin X, et al. SkillWeaver: Web Agents can Self-Improve by Discovering and Honing Skills. 2025. [arXiv:2504.07079](https://arxiv.org/abs/2504.07079)
10. Qiu J, et al. Alita: Generalist Agent Enabling Scalable Agentic Reasoning with Minimal Predefinition and Maximal Self-Evolution. 2025. [arXiv:2505.20286](https://arxiv.org/abs/2505.20286); numbers via secondary summary [emergentmind](https://www.emergentmind.com/papers/2505.20286)
11. Hu S, Lu C, Clune J. Automated Design of Agentic Systems. 2024. [arXiv:2408.08435](https://arxiv.org/abs/2408.08435)
12. Zhang J, Hu S, Lu C, Lange R, Clune J. Darwin Gödel Machine: Open-Ended Evolution of Self-Improving Agents. 2025. [arXiv:2505.22954](https://arxiv.org/abs/2505.22954)
13. Sakana AI. The Darwin Gödel Machine (safety discussion). 2025. [sakana.ai/dgm](https://sakana.ai/dgm/)
14. Zhang Q, Hu C, Upasani S, et al. Agentic Context Engineering: Evolving Contexts for Self-Improving Language Models. ICLR 2026. [arXiv:2510.04618](https://arxiv.org/abs/2510.04618)
15. Li X, et al. SkillsBench: Benchmarking How Well Agent Skills Work Across Diverse Tasks. 2026. [arXiv:2602.12670](https://arxiv.org/abs/2602.12670)
16. Lin H, Liu C, Bai X, et al. Rethinking Self-Evolution: A Constrained Exploration-Exploitation Process for Mitigating Skill Overfitting. 2026. [arXiv:2607.26643](https://arxiv.org/abs/2607.26643)
17. Liu H, Yang H, Jiang T, et al. SkillsVote: Lifecycle Governance of Agent Skills from Collection, Recommendation to Evolution. 2026. [arXiv:2605.18401](https://arxiv.org/abs/2605.18401)
18. Ding K, Zhou Y, Jin C, et al. Agent Skill Evaluation and Evolution: Frameworks and Benchmarks. 2026. [arXiv:2606.11435](https://arxiv.org/abs/2606.11435)
19. Gao H, et al. A Survey of Self-Evolving Agents: What, When, How, and Where to Evolve. TMLR. 2025, rev. 2026. [arXiv:2507.21046](https://arxiv.org/abs/2507.21046)
20. Anthropic. Equipping agents for the real world with Agent Skills. 2025-10-16, updated 2025-12-18. [anthropic.com](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
21. Agent Skills. Specification. [agentskills.io/specification](https://agentskills.io/specification)
22. Anthropic. Improving skill-creator: test, measure, and refine Agent Skills. 2026-03-03. [claude.com/blog](https://claude.com/blog/improving-skill-creator-test-measure-and-refine-agent-skills)
23. Anthropic. Claude Code docs: Subagents. Accessed 2026-11-09. [code.claude.com/docs/en/sub-agents](https://code.claude.com/docs/en/sub-agents)
24. OpenAI. Agents SDK: Handoffs. [openai.github.io/openai-agents-python/handoffs](https://openai.github.io/openai-agents-python/handoffs/)
25. CrewAI. Hierarchical process. [docs.crewai.com](https://docs.crewai.com/en/learn/hierarchical-process)
26. Hong S, et al. MetaGPT: Meta Programming for a Multi-Agent Collaborative Framework. ICLR 2024. [arXiv:2308.00352](https://arxiv.org/abs/2308.00352)
27. VRSEN. Agency Swarm repository [github.com/VRSEN/agency-swarm](https://github.com/VRSEN/agency-swarm); quick start describing the Genesis agency [vrsen.github.io/agency-swarm/quick_start](https://vrsen.github.io/agency-swarm/quick_start)
28. Paperclip repository. [github.com/paperclipai/paperclip](https://github.com/paperclipai/paperclip)
29. Paperclip docs. Handle board approvals for hires. [docs.paperclip.ing](https://docs.paperclip.ing/how-to/handle-board-approvals-for-hires)
30. Gas Town repository. [github.com/steveyegge/gastown](https://github.com/steveyegge/gastown)
31. OpenClaw docs. Creating skills (Skill Workshop). [docs.openclaw.ai/tools/creating-skills](https://docs.openclaw.ai/tools/creating-skills)
32. Nous Research. Hermes Agent documentation. [hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs)
33. Tensorlake. Hermes: a coding agent that learns from use (secondary). [tensorlake.ai](https://tensorlake.ai/blog/hermes-coding-agent-learns-from-use)
34. Zheng M, Pei J, Logeswaran L, Lee M, Jurgens D. When "A Helpful Assistant" Is Not Really Helpful: Personas in System Prompts Do Not Improve Performances of Large Language Models. Findings of EMNLP 2024. [arXiv:2311.10054](https://arxiv.org/abs/2311.10054)
35. Anthropic. Project Vend: phase two. 2025-12-18. [anthropic.com/news/project-vend-2](https://www.anthropic.com/news/project-vend-2)
36. Xu FF, et al. TheAgentCompany: Benchmarking LLM Agents on Consequential Real World Tasks. NeurIPS 2025. [arXiv:2412.14161](https://arxiv.org/abs/2412.14161)
37. Cemri M, et al. Why Do Multi-Agent LLM Systems Fail? NeurIPS 2025 D&B. [arXiv:2503.13657](https://arxiv.org/abs/2503.13657)
38. Anthropic. How we built our multi-agent research system. 2025. [anthropic.com](https://www.anthropic.com/engineering/multi-agent-research-system)
39. Kim Y, et al. Towards a Science of Scaling Agent Systems. 2025, rev. 2026. [arXiv:2512.08296](https://arxiv.org/abs/2512.08296)
40. Yan W. Don't Build Multi-Agents. Cognition, 2025-06-12. [cognition.com](https://cognition.com/blog/dont-build-multi-agents)
41. Liu Y, et al. Agent Skills in the Wild: An Empirical Study of Security Vulnerabilities at Scale. 2026. [arXiv:2601.10338](https://arxiv.org/abs/2601.10338)
42. Malicious Agent Skills in the Wild. 2026. [arXiv:2602.06547](https://arxiv.org/abs/2602.06547)
43. OWASP GenAI Security Project. Top 10 for Agentic Applications (2026), published 2025-12-09. [genai.owasp.org](https://genai.owasp.org/resource/owasp-top-10-for-agentic-applications-for-2026/); category list as summarised by [Giskard](https://www.giskard.ai/knowledge/owasp-top-10-for-agentic-application-2026)
44. Nori H, Lee YT, Zhang S, et al. Can Generalist Foundation Models Outcompete Special-Purpose Tuning? Case Study in Medicine. 2023. [arXiv:2311.16452](https://arxiv.org/abs/2311.16452)
45. Ovadia O, Brief M, Mishaeli M, Elisha O. Fine-Tuning or Retrieval? Comparing Knowledge Injection in LLMs. 2023. [arXiv:2312.05934](https://arxiv.org/abs/2312.05934)
46. Kirichenko P, Ibrahim M, Chaudhuri K, Bell SJ. AbstentionBench: Reasoning LLMs Fail on Unanswerable Questions. NeurIPS 2025. [arXiv:2506.09038](https://arxiv.org/abs/2506.09038)
47. Wen B, et al. Know Your Limits: A Survey of Abstention in Large Language Models. TACL 2025. [arXiv:2407.18418](https://arxiv.org/abs/2407.18418)
48. US Federal Trade Commission. DoNotPay final order (approved 2025-01-16). [ftc.gov](https://www.ftc.gov/node/87474)
49. MUSE-Autoskill: Self-Evolving Agents via Skill Creation, Memory, Management, and Evaluation. 2026 (known from search summary; secondary). [arXiv:2605.27366](https://arxiv.org/abs/2605.27366)
50. EASE: Behavior-Adaptive Skill Curation for Self-Evolving Agents. 2026 (known from search summary; secondary). [arXiv:2609.36746](https://arxiv.org/abs/2609.36746)
51. Build, buy, borrow or bot: workforce decision frameworks. Cornerstone white paper [cornerstoneondemand.com](https://cornerstoneondemand.com/resources/article/build-buy-borrow-or-bot-whitepaper); APQC, 2026 [apqc.org](https://www.apqc.org/resource-library/resource/build-buy-borrow-or-bot-simple-framework-make-faster-smarter-workforce)
