# The look rules bind place, colour and small screens, not code

Resolves [What one system is binding on, and what may differ](https://github.com/rails49/.github/issues/4)
on the [UI map](https://github.com/rails49/.github/issues/1).

`control`'s
[ADR-0064](https://github.com/rails49/control/blob/main/docs/adr/0064-the-chrome-is-a-band-and-a-rail.md)
reached across two repositories once: `control` dropped its menu row because
`occupancy` puts every press in a column down the left, and two apps a person
moves between in one session should not each teach a different place to look
for the same kind of control. That was done by hand with nothing behind it, and
the two have drifted since: a different blue, a different height at which the
rail turns into a strip, a different button size, one theme linked here and the
other there. Each difference is a drift, not a decision, because nothing says
which differences are allowed. This is what says it.

## Decision

**One system binds three things: where a control sits, what a colour means,
and how a small screen behaves.** These are the **look rules**. They are a set
of named tokens with one value each and a written convention, held in
[`docs/LOOK.md`](../LOOK.md). Typeface and type scale come with Shoelace's
theme and are bound by that.

**Whole components are not binding, and neither are tool versions.** `control`
draws its glyphs by hand because the editor must work on the railroad's own
network; `occupancy` fetches Shoelace's icons. The source of a glyph may
differ. Its size and its place may not. TypeScript, Lit, Vite and Shoelace
versions drift for reasons that have nothing to do with the look, and the drift
on record has produced no visible difference.

**The band binds its place and its look, not its content.** The band is about
the whole of the system the UI is about, and that is a different system for
each UI: a camera and a detector for `occupancy`, the railroad and the box for
`control` and the dcc-ex UI. So the rule cannot say what a band shows. It says
the UI's name is on the left and its controls on the right, and that a control
which exists in more than one UI sits in the same spot in each. A UI that has
no such control draws nothing there. No slot is reserved.

**A theme follows the operating system.** Both Shoelace themes are linked and
`prefers-color-scheme` decides; there is no toggle in the page. The band and the
rail keep one value in both themes, because they are what says "this is the
same project" as a person moves between UIs. The work pane follows the theme.

**Who is bound.** `control`, `occupancy` and the dcc-ex UI take all of it. The
landing page takes the tokens and the band and no rail, since it offers nothing
to press; its stack is free. JMRI takes nothing, being a tool the project does
not build.

**Where it is written, and what a change obliges.** `docs/LOOK.md` in this
repository holds the current values and the convention lines; an ADR records
why and freezes at its date, so the page is the thing that changes. A change to
it is one pull request here, and its author files an issue in each consumer
repository. Consumers may lag. Each consumer keeps a test asserting that its
own values equal the page's, so a repository cannot drift on its own between
changes, and the day it copies new values in the test goes red until code
follows.

**Nothing is prototyped first.** Every value exists in running code; the two
apps are the prototype.

## Applied to the drift on record

- The band blue is `control`'s `#1d4ed8`, a fixed value. `occupancy` took
  Shoelace's primary, which moves with the theme, and the band must not.
- The rail turns into a strip below a window height of 640px, `control`'s
  number. `occupancy` had 650.
- A rail button is 44px, `occupancy`'s number: the accepted minimum for a
  thumb, and the throttle lives on a phone. `control` had 35.
- Each links one theme. Both link both.

## Consequences

- `control` owes 44px buttons, both themes, and dark values for the drawing
  tokens that are tuned for a white canvas. Its editor rail carries fifteen
  buttons, which at 44px is a column taller than a laptop window; ADR-0064
  already turns the rail into a strip below a height, and how the column fits
  above it is `control`'s to decide.
- `occupancy` owes the fixed blue, the 640px height, both themes.
- The landing page owes the band and the tokens. Today it is a dark page in a
  palette of its own.
- The dcc-ex UI is bound from its first commit, so it cannot arrive as a fifth
  view a second time.
- Where shared code lives, if any, is the next ticket's question. If a shared
  package exists it takes its values from `docs/LOOK.md`, never the other way
  round.
- `occupancy` and `control` each already have a test pinning their own reflow
  height. Those become the values test.

## Considered

- **Whole components as the binding unit.** One header, one rail, one button,
  shared as code. It would reopen `control`'s hand-drawn glyphs and prejudge
  whether shared code exists at all.
- **Pinned tool versions.** Nothing a person sees depends on them, and the
  cost of holding four repositories in step is the monorepo cost by another
  door.
- **Lockstep on change**: every consumer follows in the same change, or the
  change does not land. Same objection.
- **An advisory definition** that obliges nothing. That is where the drift came
  from.
- **A reserved slot** on every band for track power and emergency stop, empty
  where a UI has neither. An empty slot on `occupancy`'s band would be a gap
  that says nothing.
- **A theme toggle in the page.** One more control to place and bind, for a
  choice the person has already made once in the operating system.
- **A prototype before agreeing.** The values are already in running code.
