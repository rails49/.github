# The look rules travel as a copied file, not a package

Resolves [Where shared UI code lives, and how a repo takes it](https://github.com/rails49/.github/issues/8)
on the [UI map](https://github.com/rails49/.github/issues/1).

ADR-0003 settled what one system is binding on and left the next question
open: if the four UIs are bound to the same values, where do those values live
and how does a repository take them? A survey of the mechanisms
([issue #5](https://github.com/rails49/.github/issues/5)) answered the part
everyone assumes is settled — a package **can** be shared between repositories
without a repository of its own, by a pnpm git dependency naming a
subdirectory — and pointed at that as the best fit.

This ADR does not take it. The reason is what measuring the four UIs turned
up: there is almost nothing to share.

## What is actually shared

Six values: four colours and two sizes. ADR-0003 freed whole components and
tool versions, so nothing else is binding.

The plumbing a thin framework is supposed to force into duplication has not
duplicated, because the two UIs that exist do not do the same things.
`control` routes by `hashchange` and keeps the view in the URL; `occupancy`
has no routes at all and swaps views in component state. `control` has an MQTT
client and a 559-line store client; `occupancy` has neither dependency and
saves to a file instead. The one genuine overlap, Shoelace, is wired
oppositely in each — icons compiled in against assets copied by a Vite
plugin — and neither wiring is binding on the other.

What is coming is different in kind. When `occupancy` publishes sensor topics
and the dcc-ex UI exists, two or three UIs will each want a TypeScript client
for the bus, and ADR-0002 makes the store's face shared, so a store client is
the second. Those are bindings to a contract, not chrome, and
[where the bus contract lives](https://github.com/rails49/.github/issues/2)
decides them.

## Decision

**The values live in [`docs/tokens.css`](../tokens.css) in this repository,
beside `docs/LOOK.md`.** The map's rule that no code lives here means nothing
that ships inside a UI's build. Six custom properties are the decision written
in a form a machine can read, they collide with none of the paths GitHub
special-cases in a repository of this name, and they belong next to the prose
that says what they mean.

**That file is the source, and `LOOK.md` no longer carries the values.**
`LOOK.md` says what each token means and who is bound; `tokens.css` says what
the value is. Nothing generates either from the other. Two copies in one
repository, kept in step by hand, would reproduce inside these walls the drift
the rules exist to stop.

**Nothing is installed anywhere. Every consumer copies the file and records
the commit it came from.** No npm publish, no pnpm git dependency, no
submodule, no subtree. The advantage a git dependency has over a copy is an
exact commit pin, and the recorded commit is that pin.

**A consumer keeps its own form of the values, plus the copy, verbatim, at a
fixed path.** The copy is inert: nothing imports it, the test reads it.
`control` keeps `--band` in the table that holds every other colour it draws
with, and a test asserts the table's value equals the copy's. ADR-0003 already
freed how a UI expresses a value, and `control`'s reason for holding every
colour in one file is a good one that a linked stylesheet would break.

**The check compares against the recorded copy and never fetches.** It fails
only when a consumer edits its own values. A check that reached for this
repository's tip would go red on someone else's commit, and under `control`'s
rule that `main` moves only by a green required check with no bypass, that
red-lights every open pull request in the consumer until someone syncs. That
reason is about fetching and stops there: a check that reads only files in the
consumer's own repository runs with that repository's other tests, in its
required gate if it has one
([ADR-0010](0010-the-values-check-runs-in-the-consumers-gate-because-it-fetches-nothing.md)).

**A change here is announced by hand.** Whoever changes `tokens.css` files an
issue in each consumer repository, as ADR-0003 already says. Nothing is
scheduled and nothing is automated. The values have changed zero times since
they were written down, and three consumers is a list a person can hold in
their head.

**Tool versions are free, explicitly.** Lit, Shoelace, Vite, TypeScript and
the test runner are each repository's own, and they have already drifted
apart with no visible difference. Nothing shared is code, so nothing shared
has a dependency, so there is no version to agree on. The one case where this
could break something — registering a custom element tag twice in one
document, which is a hard error rather than a warning — needs two UIs composed
into a single document, and ADR-0004 keeps them on separate pages at separate
origins.

**No AGPL-3.0 source enters `control`.** `occupancy` is AGPL-3.0 and `control`
is MIT, and a copy mechanism puts the copied file in the consumer's history
permanently. This rules `occupancy` out as a home for anything shared, and it
is a standing rule rather than a fact about this file.

**This repository takes a licence.** It had none. A file meant to be copied
into an MIT repository and an AGPL-3.0 one should say what its terms are, so
`LICENSE` here is MIT.

## What this changes in ADR-0003

ADR-0003 says the look rules are "held in `docs/LOOK.md`" and that a shared
package, if one existed, would take its values from that page. No package
exists, and the values have moved one file sideways. The rules are still held
in `docs/`, `LOOK.md` is still the page a person reads, and no consumer takes
a value from anywhere but the file beside it. ADR-0003's sentence about a
consumer's test asserting its values equal "the page's" now reads: equal its
recorded copy's.

## Consequences

- Three consumer repositories owe a copy, a recorded commit and a test. Each
  gets an issue; no code is written from the map.
- All three are out of compliance today, and the copy is what makes it
  visible. `control` links only the light theme, `occupancy` only the dark,
  against a rule that says both are linked and the operating system decides.
  `occupancy`'s band is Shoelace's primary, which moves with the theme.
  The landing page has no band and a palette of its own.
- The landing page can comply. It is one static HTML file with an inline style
  block and no build of any kind, so a copied CSS file is the only mechanism
  that reaches it at all. Had a package been chosen, the landing page would
  have been dropped from the rules or given a toolchain it does not need.
- A fourth UI starts from `LOOK.md`, the convention and this file, and writes
  its own band and rail. `control` is the evidence that this works: it
  produced a matching band by reading the rules and the other UI.
- [Where the bus contract lives](https://github.com/rails49/.github/issues/2)
  inherits the reasoning, not the address: the source stays out of the
  consumer's history, the consumer pins, a check catches drift. Its own
  answer may put the code somewhere else, because a table a static page must
  read and a TypeScript module only built UIs can import do not have the same
  constraints. The pnpm `#path:` finding is the mechanism standing ready
  there.

## Considered

- **A pnpm git dependency naming a subdirectory.** The survey's own
  recommendation, and the right answer for a package with mass. For six
  declarations it costs a workspace entry, a lockfile line and an
  explanation, and buys the pin the recorded commit already gives. It also
  makes pnpm load-bearing rather than a preference, and it cannot reach the
  landing page.
- **A publish to the npm registry.** The same objection plus a release ritual,
  which is most of the cost of a separate repository without the clarity.
- **GitHub Packages.** A token is needed to install even a public package,
  and every clean clone without one fails with a 404 that looks the same as a
  missing version.
- **A repository of its own.** An install, a version and a release for six
  values. This is the failure the map warns about in its plainest form.
- **A package inside `occupancy`.** The most likely home by size, and ruled
  out by the licence: a copy would put AGPL-3.0 source in `control`'s history
  permanently.
- **A package inside `control/ui`.** Re-centres `control`, which is one
  consumer of four.
- **Components — a shared band and rail.** Contradicts ADR-0003, and the two
  chromes that exist were written apart on purpose, each recording in comments
  why it differs.
- **Nothing at all, with the values copied out of `LOOK.md` by hand.** Then a
  consumer's test can only compare against a second hand-copy, and `LOOK.md`
  and the test drift with nothing to report it.
- **A generator** producing `tokens.css` from `LOOK.md`'s table. Machinery,
  with a test of its own, for six values.
- **A scheduled job** that fetches this file and opens a sync pull request, or
  a workflow here that files the issues. Real machinery, needing a
  cross-repository token to write into three repositories, for an event that
  has happened zero times. If it starts happening monthly, this is the
  upgrade.
