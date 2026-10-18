# The band starts with the mark and the UI name

Resolves [#49](https://github.com/rails49/.github/issues/49). Supersedes
[ADR-0012](0012-the-band-says-what-the-ui-is-about-and-what-is-true-of-it.md).

ADR-0012 has the band's left name one thing: the UI's own name, the box's name,
or the loaded railroad's name. Each UI's left does:

| UI | Band left |
|---|---|
| `control` | the loaded railroad |
| `occupancy` | the layout, or `occupancy` with none |
| dcc-ex UI | `dcc-ex` and the build |
| installation page | the box's name |
| landing page | `Rails49` |

Nothing on the band says the page is part of rails49, and where the left names
a railroad nothing says which UI is showing.

The rest of ADR-0012 stands and is restated here, so that one ADR holds it.

## Decision

1. **The band's left starts with the mark**: rails49's drawing, rails curving
   into the distance and `rails49`, or `49` where the window is narrow. Every
   UI that takes the band shows it, the landing page included. look draws it.
2. **The mark is a link to `https://rails49.org` that opens in a new tab.** It
   is not a press: it fires nothing for the UI to handle. It is a link on
   every page, the landing page included.
3. **The UI name follows the mark.** It is the UI's label under the
   installation's name
   ([ADR-0007](0007-an-installation-is-a-name-and-every-ui-is-a-label-under-it.md)):
   `control`, `dccex`, `occupancy`. The installation page and the landing page
   have no label and show no UI name.
4. **Then the left names what the UI is about**: the box's name or the loaded
   railroad's name, whichever tells a person what they are looking at. It may
   be pressed to change which one it is. It is left out where it would repeat
   the UI name or the mark.
5. **The band's right holds what is true of the whole system the UI is about,
   pressable or not.** A reading and the press that sets it sit together. A
   press that acts on the current view is not the band's: it goes on the rail,
   which carries what the current view offers. The order on the right is
   [ADR-0016](0016-the-chrome-comes-from-look.md)'s, and the gear's spot is
   [ADR-0014](0014-one-place-chooses-the-loaded-railroad-and-the-dcc-ex-ui-names-it.md)'s.
6. **Anything that shows in more than one UI sits in the same spot in each.**
   Track power is at the far right.
7. **Switching views is the rail's**, at the top of the rail
   ([`control` ADR-0064](https://github.com/rails49/control/blob/main/docs/adr/0064-the-chrome-is-a-band-and-a-rail.md)).

A UI with nothing true of its whole system to show draws nothing on the right.
No spot is reserved.

ADR-0012's rules took the place of one sentence of
[ADR-0003](0003-the-look-rules-bind-place-colour-and-small-screens-not-code.md);
the rules here take the place of ADR-0012's.

## Consequences

- look draws the mark and the UI name, and its `docs/LOOK.md` states them
  ([look#27](https://github.com/rails49/look/issues/27)).
- After look's release, one issue per UI:
  - `control` and `occupancy` set their UI name. `occupancy` no longer shows
    `occupancy` where no layout is loaded.
  - The dcc-ex UI sets `dccex` and drops `dcc-ex` from its left. Where its
    build goes is the dcc-ex UI's to decide.
  - The landing page drops `Rails49` from its left.
  - The installation page owes nothing.
- `CONTEXT.md` adds **Mark** and **UI name**, linking to look, and the
  **Band** entry names them.
- No doc outside accepted ADRs cites ADR-0012.

## Considered

- **The mark as a press**, firing an event each UI handles. Every UI would
  write the same handler to open the same page.
- **The link in the same tab.** A person driving trains in `control` would
  leave it.
- **The mark without the UI name.** On `control` the left names the railroad,
  and nothing would say which UI is showing.
- **The UI name in place of what the UI is about.** `control`'s railroad and
  the installation page's box name would need another place.
- **Superseding ADR-0014 and ADR-0016 as well**, to state the whole band in
  one ADR. Both decide more than the band and would have to be restated
  whole.
