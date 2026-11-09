# Placement: mechanic or instance

**The test, in one line: swap the principal for a stranger; if the file is still true and names no person, venture, client, private repository or fact about the principal, it is a mechanic and goes in the public framework; otherwise it is an instance and stays private.**

Run it on every file a team writes, every proposal a distil step produces, every line added to a public file, the diff, title and body of every pull request to the public framework, and every issue drafted for another repository from a private ask. When one part of a file fails the test, split it: the reusable part becomes the mechanic, and the instance file references it.

## What goes where

| Public framework (mechanic) | Private instance |
|---|---|
| the protocol, council and team-review skills | engagement letters |
| the leads, the contradictor, the checker, the generic navigator | rosters |
| the domain skills and their public, dated reference lists | private references and pointers to private repositories, agreements, past reports |
| checklists that would hold for any founder in that jurisdiction | checklists tuned to one principal's entities, clients or rates |
| the file formats and the templates | venture pages |
| fictional worked examples | the dossier (facts about the principal) |
| | a navigator that knows one person's fleet |
| | logs (in the records root, never in any code repository) |

Logs are data, not design: they live in the records root, never in a code repository, public or private.

## Things that fail the test without looking like it

- A real company, client, collaborator or venture name used "just as an example": use a fictional one.
- A path into a private repository, or the name of a private repository or private skill: say "the instance's references folder" or "the host's research skill".
- A rate, a price, a jurisdiction choice or a deadline that is the principal's own: it is a fact about the principal.
- A sentence that only makes sense with one person's history ("as we decided last month").
- A link to a private issue tracker.

## Five worked examples

| # | Candidate | Verdict | Why |
|---|---|---|---|
| 1 | A checklist for reviewing a contractor's IP assignment: present-tense assignment, moral-rights waiver where the law allows, background IP listed, signed before work starts | **mechanic** | true for any founder hiring a contractor; names no one |
| 2 | "Our designer's contract has no IP clause; chase it before the next invoice" | **instance** | names a person and a fact about the principal's contracts; goes in the people team's private references or a desk item |
| 3 | A reference listing the statutory filing dates for a small company in one country, with the official source and the date checked | **mechanic** | true for any company in that jurisdiction; entity-specific dates come from the venture pages |
| 4 | The same reference with the principal's fiscal year end and company registration number filled in | **instance** | a fact about the principal; split it: the generic calendar stays public, the filled rows go in the venture page |
| 5 | A navigator that knows the principal's internal package names and which private repository owns which tool | **instance** | names private repositories; the public framework ships the generic `navigator`, which inventories whatever its host can see |

Two more that people get wrong:

- A skill that encodes one principal's writing voice is an instance even if it names no one: it is a fact about the principal (an encoded preference). The method for capturing any voice is a mechanic.
- A fictional example that is a thin disguise of a real case (same numbers, same sequence of events) fails the test: change the facts, not only the names.

## Before committing to a public repository

Grep what you wrote for the instance's venture, client and people names, private repository names and private paths. The instance keeps that list in a plain file, one name or path fragment per line (a regular expression per line is fine), inside its private repository; the public framework never contains it. Run it on the files, the diff, and any pull-request or issue text:

```bash
rg -n -i -f <the instance's names file> <files>             # files about to be committed
git diff --cached | rg -n -i -f <the instance's names file>   # the staged diff
```

Any hit fails the test. A pre-commit hook that runs the same grep, with the list's path held in local git config rather than in the repository, is the stronger form; until a host installs one, the grep is a step in the checklist.
