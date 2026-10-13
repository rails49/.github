# A UI may ask the installation to turn the box off

Resolves [#32](https://github.com/rails49/.github/issues/32). Amends
[ADR-0009](0009-the-door-and-the-page-are-the-installations-in-a-repository-of-their-own.md):
the installation is five things, the fifth turning the box off.

A box is turned off today by pulling its plug or by `ssh` and
`sudo poweroff`. The first loses whatever was being written: a drawing being
saved, a backup being committed. The second needs a shell, which a person on
an iPad does not have. A person wants a button, and wants it where they
already are: `control`'s settings dialog.

By [ADR-0001](0001-a-ui-of-its-own-is-about-something-other-than-the-loaded-railroad.md)
the box is not the loaded railroad, so the box is not `control`'s subject. By
[ADR-0004](0004-the-installation-serves-the-uis-that-are-about-it.md) the
box's own UI is the installation's page. But the one way turning the box off
goes wrong on the layout is turning it off with the track live: the apps and
the translator stop, the command station keeps the last speed it was given,
and nothing can stop a moving loco short of its own plug. Only `control`
reads track power, and the page must not join `control`'s bus to read it
([ADR-0002](0002-a-ui-talks-to-the-bus-the-store-and-its-own-apps-face.md)).

## Decision

1. **Turning the box off is the installation's.** It is the one repository
   about the box, and turning the box off stops every stack on it.
2. **A UI may ask for it.** This is the case ADR-0001 already allows, a UI
   sending a request another party answers, as the control UI sends events
   dcc-ex answers. Whether this box can be turned off is the installation's
   answer, never the UI's.
3. **The request is served on every label's origin and on the box name**,
   so each UI reaches it on its own origin with nothing proxied by the UI
   (ADR-0004). Two requests:
   - `GET /box` answers `{"can_turn_off": true}` or `false`. Where nothing
     answers — a development machine at `localhost`
     ([ADR-0011](0011-a-development-machine-runs-the-installation-or-is-reached-at-localhost.md))
     — the answer is no.
   - `POST /box/off` answers `202` and the box turns off. A refusal answers
     with a status and one line of plain text, which the UI shows as written.
4. **A page on another origin is refused**, by the same rule the store and
   the broker apply (`control`
   [ADR-0055](https://github.com/rails49/control/blob/main/docs/adr/0055-a-browser-is-not-on-the-lan-and-the-store-refuses-it.md),
   [ADR-0057](https://github.com/rails49/control/blob/main/docs/adr/0057-one-origin-rule-and-both-faces-read-it.md)).
   Without it, any web page a person on the LAN happens to open could turn
   the box off.
5. **The UI that asks owns its own precondition.** `control`'s settings
   dialog offers the button only where `GET /box` says yes, and keeps it
   dead with a line saying why while `tc49/layout/state/power` is not `off`.
   The installation checks nothing about the railroad.

## Consequences

- The installation owes the service, its router and the two requests
  ([rails49/installation#25](https://github.com/rails49/installation/issues/25),
  installation ADR-0003).
- `control` owes the button
  ([rails49/control#745](https://github.com/rails49/control/issues/745)).
- The check in decision 5 is in the page only. A request sent from outside
  a browser skips it; the LAN is the trust boundary
  (`control` [ADR-0042](https://github.com/rails49/control/blob/main/docs/adr/0042-the-edge-terminates-tls-and-the-lan-is-the-trust-boundary.md))
  and the operator is the backstop for track power
  (`control` [ADR-0051](https://github.com/rails49/control/blob/main/docs/adr/0051-the-panel-commands-track-power-and-the-operator-is-the-backstop.md)).
- [#29](https://github.com/rails49/.github/issues/29) cuts track power when
  the box goes silent, which turning it off is. That is a backstop for the
  time nobody is at the layout, and decision 5 stays.
- When the box comes back, every train is placed again by hand: the broker
  keeps no retained row across a restart, and a train may have been moved
  while the box was off (`control` CONTEXT.md, **Recovery**).
- The installation's page, occupancy and the dcc-ex UI owe nothing. Any of
  them may add the button later on the same two requests.

## Considered

- **The installation's page only.** It follows ADR-0004 to the letter, but
  the page cannot read track power, so the precondition becomes a sentence
  instead of a dead button.
- **A privileged container in `control`.** A railroad stack would then turn
  off a box that also runs JMRI, occupancy and dcc-ex.
- **A topic on `control`'s bus.** The installation would join a bus it is
  not about and depend on `control` being up to be turned off.
