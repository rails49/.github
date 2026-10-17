# Each fact has one owner, and an accepted ADR does not change

Comes from the doc audit of 2026-10-17. Supersedes no ADR.

The docs drift in two ways: they copy facts that live somewhere else, and
accepted ADRs are edited in place. In `control`, 65 of 73 ADRs have more than
one commit and 41 carry an amendment, supersession or withdrawal note; 28 of
those notes cite only an issue. Reading the current track-power rule takes
ADR-0041, 0051, 0059, 0060, 0062, 0070 and 0073. This repository's
[ADR-0016](0016-the-chrome-comes-from-look.md) amends three ADRs, and its
`CONTEXT.md` restated `control`'s ADR-0038 and `CONTEXT.md`. Only `occupancy`
says an accepted ADR is never edited
([its ADR-0001](https://github.com/rails49/occupancy/blob/main/docs/adr/0001-glossary-and-adrs-alongside-the-spec.md)).

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
   - `CONTEXT.md`: a glossary. An entry is the term, a definition of one to
     three sentences that says what the thing is, a link to the doc that owns
     it, and an _Avoid_ list. Rules, reasons and how it works go in the
     linked doc.
   - `docs/adr/`: decisions and their reasons.
   - Other `docs/*.md`: what the system does now, such as a contract or a
     spec. Edited in place to match the code. Each covers a subject no other
     doc covers.
   - `docs/research/`: research notes and reports, dated in their opening
     line and never edited.
   - Plans and milestones go in issues.
3. **An accepted ADR does not change.** Two edits are allowed: a
   `*Superseded by [ADR-x](…).*` line at line 3, and fixing a link whose target
   moved without changing what it points to. A rename does not reach into
   ADRs; `CONTEXT.md` lists the old word under _Avoid_ as "formerly".
4. **A changed decision gets a new ADR that supersedes the old one in full,**
   also when an issue is what changed it. No ADR amends another. The new ADR's
   opening line names the issue it resolves and the ADRs it supersedes. There
   is no status line: a merged ADR is accepted.
5. **Each fact has one owner, and every other doc links to it.** This
   repository's `CONTEXT.md` holds only terms used in more than one
   repository; a term one repository owns is a one-line entry linking there.
   A repository writes an ADR for an org decision only for the decisions it
   makes in applying it. That ADR cites the org ADR in its opening line and
   does not repeat its context or rules.
6. **Docs change in the same pull request as the code they describe.** Review
   checks it.

## Consequences

- Existing ADRs, their headers, their amendment notes and their chains stay as
  they are. This applies from here on. Where a rule takes a chain to read, the
  doc for what the system does now states it: `control`'s track-power rule
  goes into its `docs/SYSTEM.md`.
- `CONTEXT.md` here is cut to the entry form above in the same change. **View**
  and **Loaded railroad** become one-line entries linking to `control`.
- look's `bump.ts` leaves `docs/adr/` alone. It rewrote line 28 of `control`'s
  ADR-0073 in control#751.
- A repository's link check resolves links to `github.com/rails49/*` instead
  of skipping every https link, so a stale reference fails the gate.
- Applying this is one issue per repository.

## Considered

- **Allow "amends" and keep the current rule in `CONTEXT.md` and the code.**
  Where neither states it, the chain is the only record.
- **Amend for small changes only.** What counts as small drifts.
- **Let a rename sweep the ADRs.** The ADR then says what its author did not
  write.
- **Change a small decision in the code and current docs only.** The ADR then
  states something false and nothing marks it.
- **Collapse the existing chains into superseding ADRs now.** An ADR that only
  gathers reasons already written changes no decision; the current docs state
  the rule.
- **A status line on every ADR.** It would need an edit to change, and
  superseded is the only status left.
- **Move `docs/agents/` into this repository.** The skills read it from each
  repository's own path.
- **A checkbox in the pull request template.** It gets ticked without being
  read.
