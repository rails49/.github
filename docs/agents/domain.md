# Domain docs

How the engineering skills use a rails49 repository's domain documentation when exploring its codebase. Every repository is single-context: one `GLOSSARY.md` at its root and one `docs/adr/`. A repository with rules of its own keeps them in its `docs/agents/domain.md`.

## Before exploring, read these

- `GLOSSARY.md` at the repo root: the glossary.
- `docs/adr/`: read ADRs that touch the area you're about to work in.

If either doesn't exist, proceed without comment. Don't flag the absence and don't suggest creating them upfront. The `/domain-modeling` skill creates them when terms or decisions get resolved.

## What each kind of doc holds

[ADR-0025](../adr/0025-each-fact-has-one-owner-and-an-adr-is-written-after-its-decision-has-run.md) says what each kind of doc holds, which decisions get an ADR, when it is written and how it changes. This repository's `GLOSSARY.md` holds only terms used in more than one repository; a term one repository owns is a one-line entry linking there.

## Use the glossary's vocabulary

When your output names a domain concept (in an issue title, a refactor proposal, a hypothesis, a test name), use the term as defined in `GLOSSARY.md`. Don't drift to synonyms the glossary rejects.

If the concept you need isn't in the glossary yet, either you're inventing language the project doesn't use (reconsider) or there's a real gap (note it for `/domain-modeling`).

## Flag ADR conflicts

If your output contradicts an existing ADR, say so rather than overriding it:

> _Contradicts ADR-NNNN (<title>), but worth reopening because…_
