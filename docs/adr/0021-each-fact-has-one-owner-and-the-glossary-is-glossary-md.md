# Each fact has one owner, and the glossary is GLOSSARY.md

Resolves [#54](https://github.com/rails49/.github/issues/54). Supersedes
[ADR-0017](0017-each-fact-has-one-owner-and-an-accepted-adr-does-not-change.md).

mattpocock-skills 1.3.0 renamed the glossary its skills read and write from
`CONTEXT.md` to `GLOSSARY.md`. `domain-modeling` creates a root `GLOSSARY.md`
when it finds none, and no skill reads `docs/agents/domain.md`, so a
repository that keeps `CONTEXT.md` ends up with two glossaries. ADR-0017
names `CONTEXT.md` in its rules and lets neither an accepted ADR nor a
research note follow a rename.

## Decision

1. **This binds every rails49 repository.** No repository writes an ADR
   adopting it. Each `CLAUDE.md` links to it in one line, and each
   `docs/agents/domain.md` points to it instead of stating a doc policy of its
   own.
2. **Each kind of doc holds one kind of thing.**
   - `README.md`: what the repository is, and how to install, run and test it.
   - `CLAUDE.md`: what an agent cannot work out from the repository: pointers,
     conventions and traps. Commands go in the README; architecture goes in
     the code or `docs/`.
   - `GLOSSARY.md`: a glossary. An entry is the term, a definition of one to
     three sentences that says what the thing is, a link to the doc that owns
     it, and an _Avoid_ list. Rules, reasons and how it works go in the
     linked doc.
   - `docs/adr/`: decisions and their reasons.
   - Other `docs/*.md`: what the system does now, such as a contract or a
     spec. Edited in place to match the code. Each covers a subject no other
     doc covers.
   - `docs/research/`: research notes and reports, dated in their opening
     line and changed only as rule 3 allows for an ADR.
   - Plans and milestones go in issues.
3. **An accepted ADR does not change.** Three edits are allowed: a
   `*Superseded by [ADR-x](…).*` line at line 3, fixing a link whose target
   moved without changing what it points to, and changing a file's name where
   that file was renamed. A term rename does not reach into ADRs;
   `GLOSSARY.md` lists the old word under _Avoid_ as "formerly".
4. **A changed decision gets a new ADR that supersedes the old one in full,**
   also when an issue is what changed it. No ADR amends another. The new ADR's
   opening line names the issue it resolves and the ADRs it supersedes. There
   is no status line: a merged ADR is accepted.
5. **Each fact has one owner, and every other doc links to it.** This
   repository's `GLOSSARY.md` holds only terms used in more than one
   repository; a term one repository owns is a one-line entry linking there.
   A repository writes an ADR for an org decision only for the decisions it
   makes in applying it. That ADR cites the org ADR in its opening line and
   does not repeat its context or rules.
6. **Docs change in the same pull request as the code they describe.** Review
   checks it.

## Consequences

- Each repository runs `git mv CONTEXT.md GLOSSARY.md` and changes every
  mention of `CONTEXT.md`, its ADRs and research notes included.
- Each `CLAUDE.md` and `docs/agents/domain.md` links here instead of to
  ADR-0017. A repository ADR that cites ADR-0017 in its opening line keeps
  the citation.
- Applying this is one issue per repository. This repository merges first,
  r49 before occupancy, so each repository's link check finds the glossaries
  it links to.

## Considered

- **Keep `CONTEXT.md`, and have each `CLAUDE.md` forbid `GLOSSARY.md`.** It
  holds only while an agent reads `CLAUDE.md` before a skill acts, and
  nothing checks that.
- **Pin mattpocock-skills at 1.2.3.** The plugin is installed once per
  machine, so the pin blocks later fixes for every project.
- **A `GLOSSARY.md` symlink to `CONTEXT.md`.** One file under two names, and
  GitHub shows a symlink as a one-line page naming its target.
- **Leave the old name in ADRs and research notes.** A file name that no
  longer exists points the reader nowhere.
