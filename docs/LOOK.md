# Look rules

What one system is binding on across rails49's UIs, decided in
[ADR-0003](adr/0003-the-look-rules-bind-place-colour-and-small-screens-not-code.md).
This page holds the current values. A change to it is one pull request here,
and its author files an issue in each consumer repository below. Each consumer
keeps a test asserting that its values equal these.

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

| Token | Value | What it is |
|---|---|---|
| `--band` | `#1d4ed8` | the band across the top |
| `--band-ink` | `#ffffff` | text and glyphs on the band |
| `--rail` | `#064e3b` | the rail down the left |
| `--rail-group` | `#059669` | a run of buttons that belong together on the rail |

Red on the chrome means stop or a fault and nothing else. The first UI that
draws an emergency stop on its band adds the value here.

## Sizes

| Name | Value | What it is |
|---|---|---|
| button | 44px | every button on the rail; the minimum a thumb needs |
| rail turns | 640px | window height below which the rail becomes a horizontal strip |

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
