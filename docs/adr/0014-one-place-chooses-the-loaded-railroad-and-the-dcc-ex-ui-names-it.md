# One place chooses the loaded railroad, and the dcc-ex UI names it

*Superseded by [ADR-0020](0020-the-bands-right-ends-with-the-gear-and-help.md).*

Resolves [#30](https://github.com/rails49/.github/issues/30). Amends
[ADR-0012](0012-the-band-says-what-the-ui-is-about-and-what-is-true-of-it.md)
and dccex
[ADR-0015](https://github.com/rails49/dccex/blob/main/docs/adr/0015-the-script-is-a-railroads-document-in-the-store.md)
d.5.

`control` loads a railroad from a picker on its band's left. The dcc-ex UI
runs the script of whichever railroad that is, and shows no railroad at all:
its script view lists every railroad in the store, starts with none picked,
and does not mark the running one. A person editing a script there cannot
tell whether it is the one the translator runs.

`control` is moving the choice into a settings dialog behind a gear, with
backup and restore and creating a railroad
([rails49/control#733](https://github.com/rails49/control/issues/733),
`control` ADR-0072). It is not a view: by
[ADR-0001](0001-a-ui-of-its-own-is-about-something-other-than-the-loaded-railroad.md)
a view shows or edits the loaded railroad, and choosing which one is loaded
is not that. occupancy already has a settings gear on its band.

## Decision

1. **`control`'s band left names the loaded railroad and is not pressed.**
   ADR-0012 lets the left be pressed; `control`'s no longer is.
2. **A settings gear sits at the right of the band, just left of track
   power, or last where a UI shows no track power.** It is the second thing
   that shows in more than one UI, so its spot is fixed. occupancy's is
   already there.
3. **The dcc-ex UI names the loaded railroad on its band's right**, read from
   `tc49/layout/state/railroad`. It is true of the whole system the UI is
   about: the translator runs that railroad's script.
4. **The dcc-ex script view edits the loaded railroad's script and no
   other.** Where none is loaded it says so. A script for another railroad
   is written after loading that railroad, which is when the translator
   would first run it.

## Consequences

- `LOOK.md`'s convention places the gear.
- `control` owes the gear and the label
  ([#733](https://github.com/rails49/control/issues/733),
  [#732](https://github.com/rails49/control/issues/732)).
- The dcc-ex UI owes the script view
  ([rails49/dccex#238](https://github.com/rails49/dccex/issues/238)) and the
  band reading ([rails49/dccex#239](https://github.com/rails49/dccex/issues/239)).
- occupancy and the installation page owe nothing.

## Considered

- **A view on `control`'s rail.** It gives the dialog a full pane, at the
  cost of amending ADR-0001, and puts settings in a different spot from
  occupancy's.
- **Keep the dcc-ex list and mark the loaded railroad.** It still lets a
  person edit a script that nothing runs, with nothing to check it against.
