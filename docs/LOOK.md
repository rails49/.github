# Look rules

What one system is binding on across rails49's UIs, decided in
[ADR-0003](adr/0003-the-look-rules-bind-place-colour-and-small-screens-not-code.md).
This page says what each token means and who is bound by it; the values are in
[`tokens.css`](tokens.css) beside it, and neither restates the other
([ADR-0005](adr/0005-the-look-rules-travel-as-a-copied-file-not-a-package.md)).

Nothing installs `tokens.css`. A consumer copies it verbatim to a fixed path of
its own and records the commit it came from; a test asserts that the values the
consumer draws with equal that copy. That test reads only files in the
consumer's own repository, so it runs with the consumer's other tests, in its
required gate if it has one
([ADR-0010](adr/0010-the-values-check-runs-in-the-consumers-gate-because-it-fetches-nothing.md)).
A change here is one pull request, and its author files an issue in each
consumer repository below.

## Who is bound

| UI | Takes |
|---|---|
| `control` | everything |
| `occupancy` | everything |
| dcc-ex UI | everything |
| landing page (`rails49.org`) | tokens and band; no rail |
| JMRI | nothing |

## Tokens

The chrome keeps these values in both themes.

| Token | What it is |
|---|---|
| `--band` | the band across the top |
| `--band-ink` | text and glyphs on the band |
| `--rail` | the rail down the left |
| `--rail-group` | a run of buttons that belong together on the rail |

Red on the chrome means stop or a fault and nothing else. The first UI that
draws an emergency stop on its band adds the token to `tokens.css` and its line
to the table above.

## Sizes

| Token | What it is |
|---|---|
| `--rail-button` | every button on the rail; the minimum a thumb needs |
| `--rail-turns` | window height below which the rail becomes a horizontal strip |

## Theme

Both Shoelace themes are linked and `prefers-color-scheme` decides. No toggle
in the page. The work pane follows the theme; the chrome does not.

Typeface and type scale are Shoelace's theme's.

## Convention

- The chrome is a band across the top and a rail down the left. The band is
  about the whole of the system the UI is about. The rail carries what the
  current view offers, and exists wherever a UI has commands.
- The band's left holds the UI's name; its right holds its controls. A control
  that exists in more than one UI sits in the same spot in each. A UI with no
  such control draws nothing there.
- A colour says the same thing in every UI.
- Where a glyph comes from is free. Its size and its place are not.
- Whole components and tool versions are free.
