# The band's right ends with the gear and Help

Resolves [#52](https://github.com/rails49/.github/issues/52). Supersedes
[ADR-0014](0014-one-place-chooses-the-loaded-railroad-and-the-dcc-ex-ui-names-it.md)
and
[ADR-0019](0019-the-band-starts-with-the-mark-and-the-ui-name.md).

ADR-0019 fixes the band's right in one order: status, then the gear, then
track power at the far right. ADR-0014 put the gear just left of track power.
A UI can add its own readings and presses among the status items without a
release of look, but they land left of the gear, away from track power.
`control` now has a railroad-wide driving mode, manual or automatic, that
belongs with track power and the stop sign
([`control` ADR-0079](https://github.com/rails49/control/blob/main/docs/adr/0079-the-railroad-has-one-driving-mode-and-a-train-one-throttle.md)).
Each such change would be a release of look, and the next UI's would be
another.

No band links to a UI's own help. ADR-0012 kept things about the UI itself off
the band's right, as a second kind of thing among what is true of the system.
Help is that kind of thing; here it sits apart from the UI's items, at the far
end, and look draws it.

The decisions of ADR-0019 and ADR-0014 that stand are restated here, so that
one ADR holds them.

## Decision

1. **The band's left starts with the mark**: rails49's drawing, rails curving
   into the distance and `rails49`, or `49` where the window is narrow. Every
   UI that takes the band shows it, the landing page included. look draws it.
2. **The mark is a link to `https://rails49.org` that opens in a new tab.** It
   is not a press.
3. **The UI name follows the mark.** It is the UI's label under the
   installation's name
   ([ADR-0007](0007-an-installation-is-a-name-and-every-ui-is-a-label-under-it.md)):
   `control`, `dccex`, `occupancy`. The installation page and the landing page
   show none.
4. **Then the left names what the UI is about**: the box's name or the loaded
   railroad's name. It may be pressed to change which one it is; `control`'s
   is not pressed. It is left out where it would repeat the UI name or the
   mark.
5. **The band's right is, from left to right, the UI's items, the gear, and
   Help.**
   - **The UI's items** are what is true of the whole system the UI is about,
     pressable or not, in the order the UI chooses. Track power and the stop
     sign are the rightmost of them. A reading and the press that sets it sit
     together. A press that acts on the current view goes on the rail. A UI
     with nothing to show draws nothing here; no spot is reserved.
   - **The gear** opens the UI's settings. A UI without settings shows none.
   - **Help** is a link to the user docs of the UI that is showing, and opens
     in a new tab. It is on every band. A UI gives its docs' address; until it
     does, Help links to the UI's repository, `github.com/rails49/<UI name>`,
     or to `github.com/rails49` where there is no UI name.
6. **look places track power, the gear and Help, and nothing else on the
   right.** Where a UI has no gear, the stop sign sits one place further right
   than in a UI that has one.
7. **Where the band does not fit, it drops the UI name, then Help, then the
   gear, in that order and only as far as it must.** Status text is cut short
   first. The mark, track power and the UI's presses keep their size.
8. **A UI's press with two states shows each by its glyph and tooltip, not by
   a colour.** A UI may use any Bootstrap icon on the band. A drawn glyph is
   added to look.
9. **Switching views is the rail's**, at the top of the rail
   ([`control` ADR-0064](https://github.com/rails49/control/blob/main/docs/adr/0064-the-chrome-is-a-band-and-a-rail.md)).
10. **The dcc-ex UI names the loaded railroad among its items**, read from
    `tc49/layout/state/railroad`. The translator runs that railroad's script.
11. **The dcc-ex script view edits the loaded railroad's script and no
    other.** Where none is loaded it says so.

## Consequences

- look draws Help, takes the docs' address as an attribute of the band, puts
  the gear right of track power, and drops the UI name, Help and the gear when
  the band does not fit. Its `docs/LOOK.md` and `CONTEXT.md` say so.
- `control` moves its driving mode from the rail to the band, as the last of
  its items before track power.
- The dcc-ex UI, `occupancy`, the installation page and the landing page owe
  nothing. Each shows Help after look's release.
- Where each UI serves its docs is a later decision.
- `CONTEXT.md` here adds **Help**, linking to look, and the **Band** entry
  names the gear and Help.

## Considered

- **Each UI orders track power, the stop sign and the gear itself.** The stop
  sign would move between UIs, and a person reaches for it without looking.
- **The gear where it is, left of track power.** The driving mode would sit
  apart from track power, or between it and the gear by a rule look would have
  to hold.
- **Every item that shows in more than one UI in the same spot.** ADR-0019
  held this. Nothing checks it outside look's own items, and holding the UIs to
  it brings back a release of look for each new item.
- **Help on the left, after the UI name.** The left says what a person is
  looking at.
- **Help in the settings dialog**, where `occupancy`'s source link went. The
  dcc-ex UI has no settings.
- **No Help; the mark already links to rails49.org.** That page is about the
  project, not the UI that is showing.
- **Dropping items at fixed window widths.** The dcc-ex UI shows fewer items
  and would lose its UI name and Help with room to spare.
- **A colour for a press's second state.** Green and red each say one thing
  already.
