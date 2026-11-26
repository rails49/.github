# Each fact has one owner, and an ADR is written after its decision has run

Resolves [#84](https://github.com/rails49/.github/issues/84). Supersedes
[ADR-0021](0021-each-fact-has-one-owner-and-the-glossary-is-glossary-md.md).

ADR-0021 rule 4 gives every changed decision a new ADR. A decision written
down before it has run is corrected by the work that builds it, and under
rule 4 each correction is another ADR. Since 2026-11-04, starting with
[#60](https://github.com/rails49/.github/issues/60), three rules have been
applied that ADR-0021 does not hold:

- A decision gets an ADR only if it is hard to reverse, surprising without
  context and the result of a real trade-off.
- An ADR is written after its decision has run.
  [ADR-0024](0024-each-ui-has-an-origin-of-its-own-at-a-label-or-a-ui-port.md)
  was written after each of its four issues had run on a box.
- A change to an ADR under a month old gets a line on that ADR naming the
  issue, and no new ADR.
  [ADR-0020](0020-the-bands-right-ends-with-the-gear-and-help.md) got such a
  line at 23 days old, for
  [rails49/look#42](https://github.com/rails49/look/issues/42).

Only control's `CLAUDE.md` states the third rule. Every other repository
links ADR-0021, which says the opposite.

ADR-0021 lets no ADR amend another, so this one supersedes it in full. It
restates every rule of ADR-0021 that stands. Rules 3, 4 and 7 are new, and
rule 6 gains its exception.

## Decision

1. **This binds every rails49 repository.** No repository writes an ADR
   adopting it or states a doc policy of its own. Each `CLAUDE.md` links to
   it in one line.
2. **Each kind of doc holds one kind of thing.**
   - `README.md`: what the repository is, and how to install, run and test it.
   - `CLAUDE.md`: what an agent cannot work out from the repository: pointers,
     conventions and traps. Commands go in the README; architecture goes in
     the code or `docs/`.
   - `GLOSSARY.md`: a glossary. An entry is the term, a definition of one to
     three sentences that says what the thing is, a link to the doc that owns
     it, and an _Avoid_ list. Rules, reasons and how it works go in the
     linked doc.
   - `docs/adr/`: decisions and their reasons. Numbers, names and field lists
     go in the spec or contract doc.
   - Other `docs/*.md`: what the system does now, such as a contract or a
     spec. Edited in place to match the code. Each covers a subject no other
     doc covers.
   - `docs/research/`: research notes and reports, dated in their opening
     line and changed only as rule 5 allows for an ADR.
   - Plans and milestones go in issues.
3. **A decision gets an ADR only if it is hard to reverse, surprising without
   context, and the result of a real trade-off.** Any other choice keeps its
   reason in the issue that made it, and the behaviour it produces goes in
   the doc or docstring that describes the code now.
4. **An ADR is written after its decision has run.** Until then the decision
   stays in its issue and can change there.
5. **An accepted ADR does not change.** Three edits are allowed: the line at
   line 3 that rule 6 or rule 7 gives it, fixing a link whose target moved
   without changing what it points to, and changing a file's name where that
   file was renamed. A term rename does not reach into ADRs; `GLOSSARY.md`
   lists the old word under _Avoid_ as "formerly".
6. **A changed decision gets a new ADR that supersedes the old one in full,**
   also when an issue is what changed it, unless rule 7 applies. No ADR
   amends another. The new ADR's opening line names the issue it resolves and
   the ADRs it supersedes, and line 3 of each of those becomes
   `*Superseded by [ADR-x](…).*`. There is no status line: a merged ADR is
   accepted.
7. **A change to an ADR under a month old gets no new ADR.** A decision that
   changes that soon was easy to reverse. Line 3 of the ADR names the issue
   and, where only one rule changes, the rule:
   `*Rule 2 superseded by [rails49/look#42](…): <what holds now>.*` The new
   behaviour goes in the doc that describes it.
8. **Each fact has one owner, and every other doc links to it.** This
   repository's `GLOSSARY.md` holds only terms used in more than one
   repository; a term one repository owns is a one-line entry linking there.
   A repository writes an ADR for an org decision only for the decisions it
   makes in applying it. That ADR cites the org ADR in its opening line and
   does not repeat its context or rules.
9. **Docs change in the same pull request as the code they describe.** Review
   checks it.

## Consequences

- Each `CLAUDE.md` links here instead of to ADR-0021, and look's instead of
  to ADR-0017. A repository ADR that cites either in its opening line keeps
  the citation.
- control's `CLAUDE.md` drops its copy of rule 7.
- By rule 8 the text that every repository's `docs/agents/` files shared has
  one owner, this repository's `docs/agents/`. A repository keeps only the
  lines that are its own, and its `CLAUDE.md` links there.

## Considered

- **Edit a young ADR in place.**
  [ADR-0017](0017-each-fact-has-one-owner-and-an-accepted-adr-does-not-change.md)
  counted where that leads: in `control`, 65 of 73 ADRs had more than one
  commit. A line naming the issue leaves the text as it was accepted.
- **Leave the three rules in each repository's `CLAUDE.md`.** Only control
  states one of them, and each `CLAUDE.md` links the ADR that says the
  opposite.
