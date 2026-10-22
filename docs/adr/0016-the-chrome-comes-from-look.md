# The chrome comes from look

Resolves [#34](https://github.com/rails49/.github/issues/34), the org decision
on the [look map](https://github.com/rails49/look/issues/1). Supersedes
[ADR-0005](0005-the-look-rules-travel-as-a-copied-file-not-a-package.md) and
[ADR-0010](0010-the-values-check-runs-in-the-consumers-gate-because-it-fetches-nothing.md).
Amends
[ADR-0003](0003-the-look-rules-bind-place-colour-and-small-screens-not-code.md),
[ADR-0007](0007-an-installation-is-a-name-and-every-ui-is-a-label-under-it.md)
and
[ADR-0012](0012-the-band-says-what-the-ui-is-about-and-what-is-true-of-it.md).

ADR-0003 bound the UIs to a set of values and a written convention and left
the code free. ADR-0005 carried the values as a copied file, checked by a test
in each consumer, with a change announced by an issue in each. The UIs drifted
anyway: eight values were bound and everything else was free, and the consumer
issues stayed open.

[`@rails49/look`](https://github.com/rails49/look) is one package that draws
the band, the rail, the dialogs and the Shoelace theme. `control`, `occupancy`,
the dcc-ex UI, the installation page and the landing page all draw their
chrome with it.

## Decision

1. **Every rails49 UI draws its chrome with look.** A built UI depends on it
   by a pnpm git dependency pinned to a tag. A static page loads its
   single-file build at a tag. Each UI's own chrome code is deleted. JMRI
   takes nothing.
2. **look holds the values and their meaning.** The values are in its
   `src/tokens.ts`, and what each one means, who takes the chrome, and the
   convention are in its `docs/LOOK.md`. This repository holds no values.
3. **A UI styles none of the chrome.** Every consumer runs look's `look-check`
   in its required gate, with no opt-out. It fails on a selector naming a
   `look-` element, on setting one of look's tokens or a `--sl-*` property,
   and on `sl-*::part()` in app code
   ([look#8](https://github.com/rails49/look/issues/8)).
4. **A release reaches every consumer by itself.** A release of look is
   reviewed once, on look's demo page, and tagged. The tag opens a bump pull
   request in each consumer, which merges itself when that consumer's gate is
   green. Each gate stands in for a review of the bump
   ([look#3](https://github.com/rails49/look/issues/3),
   [look#10](https://github.com/rails49/look/issues/10)).

## What this changes in earlier ADRs

- **ADR-0003.** What one system binds stands: where a control sits, what a
  colour means, how a small screen behaves, and that the theme follows the
  operating system. Who is bound stands, except that the landing page and the
  installation page take the band and nothing else. These go: "not code",
  "whole components are not binding", the values held in `docs/LOOK.md`, a
  values test in each consumer, an issue in each consumer for a change, and a
  shared package taking its values from this repository.
- **ADR-0005** is superseded. Nothing is copied. A change is announced by the
  bump pull request, not by hand.
- **ADR-0007.** The installation page draws look's band, with the box's name
  on its left.
- **ADR-0010** is superseded. There is no values check to place; `look-check`
  is in every gate.
- **ADR-0012.** What the band's two ends mean stands. look's band places them
  in one order: what the UI is about on the left; status, the gear and track
  power on the right, in that order. That replaces "Nothing else is fixed
  until a second UI shows it."

## Values

`docs/tokens.css` is deleted. Where it disagreed with look, look's value is the
one every UI draws:

- `--stop` is `#ef4444`: the stop sign, track power stopped, a fault, or a
  connection down. `--stop-ink` goes; nothing draws with it.
- `--power-on` is added: track power on, or a connection up.
- The rail turns into a strip at or below a window height of 640px, not below.

## Consequences

- `docs/LOOK.md` here becomes a pointer to look's.
- `GLOSSARY.md` retires **Look rules** and adds **Look**. **Reference copy**
  stays, for the bus contract
  ([ADR-0006](0006-the-bus-contract-is-controls-and-travels-when-something-reads-it.md)).
- A consumer that does not merge a bump stays on its tag and keeps working;
  the release run fails and says which.

## Considered

- **Keep `tokens.css` here as the source and have look read it.** look's build
  would fetch from another repository, which is what ADR-0005 kept every check
  away from, and the meaning would sit in one repository and the code in
  another.
- **Keep the copied file beside the package.** Two places to change for one
  value, and the copy's check only ever said a consumer had drifted after it
  had.
- **Announce releases by hand.** That is what left the consumer issues open.
