# The band says what the UI is about, and what is true of it

Resolves [LOOK.md says the band's right holds controls; the UIs put status
there](https://github.com/rails49/.github/issues/22), which the
[UI map](https://github.com/rails49/.github/issues/1) did not chart. It was
raised by [rails49/dccex#1](https://github.com/rails49/dccex/issues/1), which
left it here.

[ADR-0003](0003-the-look-rules-bind-place-colour-and-small-screens-not-code.md)
says the band's left holds the UI's name and its right holds its controls. No
UI with a band draws it that way:

| UI | Band left | Band right |
|---|---|---|
| `control` | the loaded railroad, as a picker that asks for another | faults, the session clock, and ON, STOP and OFF |
| dcc-ex UI | the name `dcc-ex` | two readings: the link and the rails; no controls |
| `occupancy` | the view switch and a status line | a link to the source and the settings gear |
| installation page | the box's name ([#19](https://github.com/rails49/.github/issues/19)) | nothing |

The rule drew its line by whether a thing can be pressed. The UIs drew it by
what a thing is about, and `control`'s band says why in its own source: track
power is a fact about the whole railroad rather than about any view, so its
reading and the presses that set it belong together, and both belong on the
band. ADR-0003 already says the band "is about the whole of the system the UI
is about". This carries that line to both ends of the band.

## Decision

**The band's left names what the UI is about.** That is the UI's own name, the
box's name, or the loaded railroad's name, whichever tells a person what they
are looking at. It may be pressed to change which one it is, as `control`'s
railroad picker is.

**The band's right holds what is true of the whole system the UI is about,
pressable or not.** A reading and the press that sets it sit together. A press
that acts on the current view is not the band's: it goes on the rail, which
carries what the current view offers.

**Anything that shows in more than one UI sits in the same spot in each.**
ADR-0003 said this of controls; it now covers readings too. One spot is fixed:
track power is at the far right. `control` and the dcc-ex UI already put it
there. Nothing else is fixed until a second UI shows it.

**Switching views is the rail's.** It exists in both `control` and
`occupancy`, so it must sit in one place, and `control`'s
[ADR-0064](https://github.com/rails49/control/blob/main/docs/adr/0064-the-chrome-is-a-band-and-a-rail.md)
put it at the top of the rail.

A UI with nothing true of its whole system to show still draws nothing on the
right. No spot is reserved.

## What this amends in ADR-0003

The sentence "It says the UI's name is on the left and its controls on the
right, and that a control which exists in more than one UI sits in the same
spot in each" is replaced by the four paragraphs above. The rest of that
paragraph stands: the band binds its place and its look, not its content, and
no spot is reserved.

## Consequences

- `control` owes nothing for placement. Its picker is the left, its faults,
  clock and power presses are the right, and power is at the far right.
- The dcc-ex UI owes nothing.
- `occupancy` owes four moves: the view switch to the top of its rail, the
  layout name to the band's left, the status line to the band's right, and the
  source link into the settings dialog, since the source is about the project
  rather than the camera and detector.
- The landing page owes nothing. The installation page draws nothing
  differently, but the comment on its band gives the old rule as the reason
  its right is empty, and owes an edit.
- `LOOK.md`'s Convention section is rewritten to match, and each consumer gets
  an issue, as any change to it requires (ADR-0003).
- `control` draws STOP in red on its band, which `LOOK.md` says obliges the
  first UI to do so to add the token. That was missed. It is tracked
  separately, because it is about colour and not place.

## Considered

- **Reword the right end only**, as #22 proposed. `control`'s left breaks the
  rule too, and the next ticket would have been the same argument about the
  other end.
- **Controls only on the right, as written.** `control` would move its faults
  and clock somewhere with no place for them, and the dcc-ex UI's band would be
  a name and nothing else.
- **Status only on the right.** `control` would move ON, STOP and OFF to the
  rail. Its ADR-0051 put them on the band because track power is no view's.
- **The UI's name on the left, literally.** `control` would move its picker to
  a place nothing names, and #19 had already read "the UI's name" as the box's
  name for the installation page.
- **Leave the left free.** It would stop saying what a person is looking at,
  which is the one thing every UI's left already does.
- **Let the right also hold things about the UI itself**, such as a source
  link. It keeps `occupancy` unchanged at the cost of a second kind of thing on
  the right, and the link is one click away in the settings dialog.
